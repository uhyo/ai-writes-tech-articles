---
title: "JavaScriptのProxyとReflectで作る透過的なリアクティブシステム"
emoji: "🔮"
type: "tech"
topics: ["javascript", "typescript", "proxy", "vue"]
published: true
---

## ProxyとWeakMapだけで実装できる

結論から言うと、実用的なリアクティブシステムはProxyとWeakMapだけで実装できます。Vue 3のリアクティビティコアも基本的にはこの2つで構成されてる。

で、筆者も去年、社内の状態管理ライブラリを作るプロジェクトで「Reduxみたいなの作ろう」って話になって、最初はimmutableなアプローチを考えてたんですが、途中でVueのソース読んで「あ、Proxyでいけるじゃん」って気づいてProxyベースに変更したことがあります。結果的に3日くらい実装時間が短縮されて、しかもコード量も半分以下になった。

この記事では、そのときに学んだProxyとReflectを使った透過的なリアクティブシステムの作り方を書きます。

## まず動かしてみる

最初にシンプルなやつを作ってみましょう。

```typescript
const targetMap = new WeakMap();
let activeEffect = null;

function reactive(target) {
  return new Proxy(target, {
    get(target, key, receiver) {
      track(target, key);
      return Reflect.get(target, key, receiver);
    },
    set(target, key, value, receiver) {
      const result = Reflect.set(target, key, value, receiver);
      trigger(target, key);
      return result;
    }
  });
}

function track(target, key) {
  if (!activeEffect) return;

  let depsMap = targetMap.get(target);
  if (!depsMap) {
    targetMap.set(target, (depsMap = new Map()));
  }

  let dep = depsMap.get(key);
  if (!dep) {
    depsMap.set(key, (dep = new Set()));
  }

  dep.add(activeEffect);
}

function trigger(target, key) {
  const depsMap = targetMap.get(target);
  if (!depsMap) return;

  const dep = depsMap.get(key);
  if (!dep) return;

  dep.forEach(effect => effect());
}

function effect(fn) {
  activeEffect = fn;
  fn();
  activeEffect = null;
}
```

これで基本的な仕組みは動きます。

```typescript
const state = reactive({ count: 0 });

effect(() => {
  console.log(`Count: ${state.count}`);
});

state.count++; // "Count: 1" が表示される
```

...あ、でもこれ、ネストしたオブジェクトで動かないんだった。

## ネストしたオブジェクトの問題

さっきのコード、こういう場合に死にます：

```typescript
const state = reactive({
  user: {
    name: 'John'
  }
});

effect(() => {
  console.log(state.user.name);
});

state.user.name = 'Jane'; // effectが発火しない！
```

理由は簡単で、`state.user`がただのオブジェクトを返してるから。Proxyで包まれてない。

で、これを直すには`get`のときに返す値もProxyで包む必要があって：

```typescript
function reactive(target) {
  return new Proxy(target, {
    get(target, key, receiver) {
      track(target, key);
      const res = Reflect.get(target, key, receiver);

      // オブジェクトだったらProxyで包む
      if (typeof res === 'object' && res !== null) {
        return reactive(res);
      }

      return res;
    },
    set(target, key, value, receiver) {
      const result = Reflect.set(target, key, value, receiver);
      trigger(target, key);
      return result;
    }
  });
}
```

これで動く...と思いきや、実はこれもまだ問題があって、同じオブジェクトに対して毎回新しいProxyを作ってしまってる。

```typescript
const obj = { foo: 'bar' };
const state = reactive({ data: obj });

console.log(state.data === state.data); // false！
```

これはまずい。同じオブジェクトからは同じProxyを返さないと。

## Proxyのキャッシング

ということで、WeakMapでProxyをキャッシュします[^1]。

```typescript
const proxyMap = new WeakMap();

function reactive(target) {
  // すでにProxyが作られてたら使い回す
  const existingProxy = proxyMap.get(target);
  if (existingProxy) {
    return existingProxy;
  }

  const proxy = new Proxy(target, {
    get(target, key, receiver) {
      track(target, key);
      const res = Reflect.get(target, key, receiver);

      if (typeof res === 'object' && res !== null) {
        return reactive(res);
      }

      return res;
    },
    set(target, key, value, receiver) {
      const result = Reflect.set(target, key, value, receiver);
      trigger(target, key);
      return result;
    }
  });

  proxyMap.set(target, proxy);
  return proxy;
}
```

これでようやくまともに動く。

[^1]: WeakMapを使うのは、オブジェクトが不要になったときにガベージコレクションされるようにするため。普通のMapだとメモリリークする。

## Reflectの必要性

そういえば、なんでReflect使ってるのかって話をしてなかった。

最初、筆者も「別に`target[key]`でいいじゃん」って思ってたんですが、実はこれだとgetterとかで`this`が壊れます。

```typescript
const obj = {
  _value: 1,
  get value() {
    return this._value;
  }
};

const proxy = new Proxy(obj, {
  get(target, key) {
    // これだと動くけど...
    return target[key];
  }
});

console.log(proxy.value); // 1
```

これは動くんですけど、こういうケースで死にます：

```typescript
const obj = {
  _value: 1,
  get value() {
    return this._value;
  }
};

const parent = reactive({ child: obj });

// この場合、childのgetterが呼ばれるとき、thisがchildじゃなくてparentになる
console.log(parent.child.value); // undefined！
```

Reflectの第3引数`receiver`を渡すと、getter内の`this`が正しいProxyオブジェクトを指すようになって、これが解決されます。

```typescript
get(target, key, receiver) {
  const res = Reflect.get(target, key, receiver); // receiverを渡す
  // ...
}
```

この辺はMDNのドキュメントにも書いてあるんだけど、実際にハマるまで重要性が分からなかった。Vue 3の実装見てると、最初のバージョン（#2851とか）では`receiver`渡してなくて、後で修正されてる。

## 配列の扱い

で、ここまで来て気づくんですが、配列がちょっと厄介です。

```typescript
const state = reactive({ items: [1, 2, 3] });

effect(() => {
  console.log(state.items.length);
});

state.items.push(4); // これでeffectが2回発火する！
```

理由は、`push`が内部で`length`の読み取りと書き込みを両方やってるから。で、Vue 3では`track`の中でこの辺を検出して、余計な発火を防ぐ仕組みが入ってます[^2]。

```typescript
function track(target, key) {
  if (!activeEffect) return;

  // 配列のlengthに対するtrackは、pushとかの操作中だったらスキップ
  if (Array.isArray(target) && key === 'length') {
    // ... 複雑なロジック
  }

  // ...
}
```

この辺は正直、実装が複雑すぎて筆者もまだ完全には理解してない。実務ではVueのreactive()使えばいいだけなので、深追いしてない。

[^2]: Vue 3のソースコード見ると、`pauseTracking()`と`resetTracking()`っていう関数があって、配列操作中はtrackingを一時停止する仕組みになってる。

## effectのネストとクリーンアップ

もう一個、実務でハマったのがeffectのネスト。

```typescript
effect(() => {
  console.log('Outer:', state.count);

  effect(() => {
    console.log('Inner:', state.count);
  });
});
```

さっきの実装だと、`activeEffect`がグローバル変数1個だけなので、内側のeffectが外側のeffectを上書きしちゃって、依存関係がおかしくなります。

Vue 3では、effectをスタックで管理してる：

```typescript
const effectStack = [];
let activeEffect = null;

function effect(fn) {
  const effectFn = () => {
    try {
      effectStack.push(effectFn);
      activeEffect = effectFn;
      return fn();
    } finally {
      effectStack.pop();
      activeEffect = effectStack[effectStack.length - 1];
    }
  };

  effectFn();
}
```

...というか、これも最初は気づかなくて、テスト書いてるときに「あれ？おかしいな」って思って、Vue 3のソース読んで「あ、スタックか」ってなった。

あと、effectのクリーンアップも重要で、effectが再実行されるときは前の依存関係をクリアする必要があります。じゃないと、条件分岐で使わなくなったプロパティの変更でもeffectが発火し続ける。

```typescript
function effect(fn) {
  const effectFn = () => {
    // 前の依存関係をクリア
    cleanup(effectFn);

    try {
      effectStack.push(effectFn);
      activeEffect = effectFn;
      return fn();
    } finally {
      effectStack.pop();
      activeEffect = effectStack[effectStack.length - 1];
    }
  };

  effectFn.deps = []; // 依存関係を記録
  effectFn();
}

function cleanup(effect) {
  for (const dep of effect.deps) {
    dep.delete(effect);
  }
  effect.deps.length = 0;
}

function track(target, key) {
  if (!activeEffect) return;

  let depsMap = targetMap.get(target);
  if (!depsMap) {
    targetMap.set(target, (depsMap = new Map()));
  }

  let dep = depsMap.get(key);
  if (!dep) {
    depsMap.set(key, (dep = new Set()));
  }

  dep.add(activeEffect);
  activeEffect.deps.push(dep); // effectからもdepを参照できるようにする
}
```

この辺まで来ると、もうVue 3の実装とほぼ同じ構造になってきます。

## readonly()の実装

ちなみに、Vue 3には`readonly()`っていうAPIもあって、これは変更を禁止するProxyを作ります。

```typescript
function readonly(target) {
  return new Proxy(target, {
    get(target, key, receiver) {
      const res = Reflect.get(target, key, receiver);

      if (typeof res === 'object' && res !== null) {
        return readonly(res);
      }

      return res;
    },
    set() {
      console.warn('Set operation on readonly object failed');
      return true; // エラーにはしない
    },
    deleteProperty() {
      console.warn('Delete operation on readonly object failed');
      return true;
    }
  });
}
```

これ、propsの実装で使われてて、コンポーネントが受け取るpropsを子コンポーネントが書き換えられないようにしてる。Reactだとこの辺Object.freezeで固めてた記憶があるけど、Proxyだと開発時に警告出せるから便利。

## shallowReactive()の話

余談なんですけど、Vue 3には`shallowReactive()`ってAPIもあって、これは深くreactiveにしないやつです。

```typescript
function shallowReactive(target) {
  return new Proxy(target, {
    get(target, key, receiver) {
      track(target, key);
      return Reflect.get(target, key, receiver); // Proxyで包まない
    },
    set(target, key, value, receiver) {
      const result = Reflect.set(target, key, value, receiver);
      trigger(target, key);
      return result;
    }
  });
}
```

パフォーマンスが気になるときとか、大きなデータ構造の一部だけreactiveにしたいときに使う。実務では使ったことないけど、地図データとか3Dモデルみたいな巨大なオブジェクトを扱うときは便利かもしれない。

## ProxyとObject.definePropertyの比較

Vue 2では`Object.defineProperty`を使ってたんですが、Proxyに比べるといくつか制限があって：

1. **プロパティの追加・削除が検出できない** - `Vue.set()`とか`Vue.delete()`が必要だった
2. **配列のインデックス操作が検出できない** - `arr[0] = 'foo'`みたいなのが動かない
3. **Mapとかには使えない** - オブジェクトのプロパティにしか使えない

Proxyならこの辺全部解決してて、しかもコードもシンプル。Vue 3がProxyベースになったのは必然だったと思います。

ただ、IE11がProxyをサポートしてなかったので、Vue 3はIE11切り捨てた。これは当時（2020年）結構話題になって、Twitterで「IE11サポートどうするんだ」的な議論があったけど、今となってはもうどうでもいい話ですね。

## まとめ

ProxyとReflectを使えば、かなり簡単にリアクティブシステムが作れます。基本的な部分は100行くらいで実装できて、あとは細かい最適化とエッジケース対応がメイン。

実務でこれを0から書くことはほぼないと思うけど（Vue 3のreactive()使えばいいので）、仕組みを理解しておくとデバッグのときに役立つし、Vue以外のフレームワークでも似たような実装が多いので、知っておいて損はない。

SolidJSとかPreactのSignalsとかも似たようなアプローチだし、最近だとTC39でSignalsの標準化提案も進んでて、将来的にはブラウザネイティブでリアクティビティが使えるようになるかもしれない。その辺はまた別の機会に書きたい。

あ、そういえば、TypeScriptの型定義周りの話を全然してないけど、これも書くと長くなるので今回は省略。reactive()の返り値の型とか、UnwrapNestedRefsとか、その辺はVue 3の型定義ファイル読むと面白いです。
