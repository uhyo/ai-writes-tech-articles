---
title: "JavaScriptのProxyオブジェクトでリアクティブシステムを作る：Vueの内部実装から学ぶ"
emoji: "🔮"
type: "tech"
topics: ["javascript", "vue", "proxy", "reactivity"]
published: true
---

去年、社内の管理画面のリファクタプロジェクトで「状態管理をどうするか」って話になって、「別にReduxいらなくない？」「VueのreactivityパッケージだけインポートしてReactで使えばよくない？」みたいな議論をしてた。で、実際に`@vue/reactivity`を試してみたんだけど、想像以上に使いやすくて驚いた。`reactive()`でオブジェクトをラップするだけで依存関係の追跡が自動で走る。何これ魔法か？

そこから興味が湧いて、vuejs/coreの`packages/reactivity/src/reactive.ts`をガッツリ読み込むことになった。結論から言うと、これProxyとWeakMapだけで実装できる。しかも案外シンプル。

## Proxyって何だっけ

ES6で入ったProxyは、オブジェクトの基本的な操作をインターセプトできるやつ。

```js
const target = { name: "Taro" };
const handler = {
  get(target, key) {
    console.log(`${key}が読まれた`);
    return target[key];
  },
  set(target, key, value) {
    console.log(`${key}に${value}をセット`);
    target[key] = value;
    return true;
  }
};

const proxy = new Proxy(target, handler);
console.log(proxy.name); // "nameが読まれた" → "Taro"
proxy.age = 25; // "ageに25をセット"
```

この仕組みがあれば、プロパティが「いつ読まれたか」「いつ書き換えられたか」を全部捕捉できる。で、Vueはこれを使って依存関係の自動追跡をやってるわけ。

Vue 2の頃は`Object.defineProperty`でgetter/setterを定義してたけど、これだと動的に追加されたプロパティが追跡できなかった。`Vue.set(obj, 'newKey', value)`みたいなAPIが必要だったのはそのせい。Vue 3でProxyに移行してから、その制約が全部消えた。

## Vueのreactive()の中身

Vueの`reactive()`は、受け取ったオブジェクトをProxyでラップして返す。実装を簡略化するとこんな感じ：

```ts
// vuejs/core の packages/reactivity/src/reactive.ts から抜粋
const reactiveMap = new WeakMap();

function reactive(target) {
  // すでにProxyを作ってたら再利用
  const existingProxy = reactiveMap.get(target);
  if (existingProxy) {
    return existingProxy;
  }

  const proxy = new Proxy(target, {
    get(target, key, receiver) {
      // 依存関係を記録
      track(target, key);
      return Reflect.get(target, key, receiver);
    },
    set(target, key, value, receiver) {
      const result = Reflect.set(target, key, value, receiver);
      // 変更を通知
      trigger(target, key);
      return result;
    }
  });

  reactiveMap.set(target, proxy);
  return proxy;
}
```

WeakMapで元のオブジェクトとProxyを紐付けてる。同じオブジェクトに対して`reactive()`を何度呼んでも、同じProxyが返る。メモリリークを防ぐためにWeakMapを使ってるのがポイント。

ちなみに、`Reflect.get(target, key, receiver)`を使ってるのは、receiverを正しく伝播させるため。Proxyのハンドラ内で直接`target[key]`をアクセスすると、ネストしたオブジェクトでthisの参照が狂う。Reflectを使えばその問題が消える。

## 依存関係の追跡：track()

プロパティが読まれたときに「誰が読んだか」を記録する必要がある。Vueは`track(target, key)`でこれをやってる。

```ts
// 依存関係を格納するグローバルなWeakMap
// target -> key -> Set<Effect>
const targetMap = new WeakMap();

// 現在実行中のeffect
let activeEffect = null;

function track(target, key) {
  // effectが実行中じゃなければ何もしない
  if (!activeEffect) return;

  let depsMap = targetMap.get(target);
  if (!depsMap) {
    targetMap.set(target, (depsMap = new Map()));
  }

  let dep = depsMap.get(key);
  if (!dep) {
    depsMap.set(key, (dep = new Set()));
  }

  // 現在のeffectを依存リストに追加
  dep.add(activeEffect);
}
```

`targetMap`は「対象オブジェクト → キー → 依存effect」という3階層の構造になってる。WeakMap → Map → Setの組み合わせ。

例えば：

```js
const state = reactive({ count: 0, name: "Taro" });
```

このとき`state.count`にアクセスすると、`track(state, 'count')`が呼ばれて、`activeEffect`が依存リストに入る。

## 副作用の実行：effect()

で、そもそも`activeEffect`って何なのか。これは`effect()`で登録される副作用関数のこと。

```ts
function effect(fn) {
  const effectFn = () => {
    activeEffect = effectFn;
    fn();
    activeEffect = null;
  };

  // 初回実行
  effectFn();

  return effectFn;
}
```

使い方はこう：

```js
const state = reactive({ count: 0 });

effect(() => {
  console.log(`countは${state.count}です`);
});
// → "countは0です"

state.count = 1;
// → "countは1です"
```

`effect()`の中で`state.count`にアクセスすると、自動的に依存関係が記録される。そして`state.count`が変更されると、effectが再実行される。これがリアクティブシステムの核心。

Vueのコンポーネントでは、renderやcomputedがeffectとして登録されてる。だから`reactive()`で作ったstateを変更するだけで、画面が自動的に更新される。

## 変更の通知：trigger()

プロパティが書き換えられたら、そのプロパティに依存してるeffectを全部実行する。

```ts
function trigger(target, key) {
  const depsMap = targetMap.get(target);
  if (!depsMap) return;

  const dep = depsMap.get(key);
  if (!dep) return;

  // 依存してるeffectを全部実行
  dep.forEach(effect => effect());
}
```

シンプルだけど、これで動く。`state.count = 1`みたいに値を変更すると、Proxyのsetハンドラが`trigger(state, 'count')`を呼んで、登録されてた全effectが実行される。

実際のVueの実装はもっと複雑で、スケジューリングとかバッチ処理とか色々やってるけど、基本的な仕組みはこれ。PR #2851でshallowとnormalのProxyを別々にtrackする修正が入ったり、#4318で配列のパフォーマンス改善の議論があったりするけど、コアのロジックは変わってない。

:::details 余談：ReflectはなぜProxy内で使うのか
Proxyのハンドラ内では、`target[key]`じゃなくて`Reflect.get(target, key, receiver)`を使うべき。

```js
const obj = {
  get value() {
    return this.x;
  },
  x: 1
};

const proxy = new Proxy(obj, {
  get(target, key) {
    // これだとthisがtargetになる
    return target[key];
  }
});
```

この場合、`proxy.value`にアクセスすると、getter内の`this`が元の`target`を指す。Proxyを経由したアクセスなのに、元のオブジェクトへの参照が出てきてしまう。

```js
const proxy = new Proxy(obj, {
  get(target, key, receiver) {
    // receiverを渡すとthisがproxyになる
    return Reflect.get(target, key, receiver);
  }
});
```

Reflectの第3引数にreceiverを渡せば、getter内の`this`がproxyになる。ネストしたreactiveオブジェクトでも正しく追跡できるのはこのおかげ。

Vueの実装でもちゃんとreceiver付きでReflectを使ってる。地味だけど重要な部分。
:::

## 実際に作ってみる

理屈はわかった。じゃあ実装してみよう。最小限のコードでリアクティブシステムを作る。

```js
// グローバルな依存関係マップ
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
  const effectFn = () => {
    activeEffect = effectFn;
    fn();
    activeEffect = null;
  };

  effectFn();
  return effectFn;
}
```

これで全部。50行くらい。試してみる：

```js
const state = reactive({
  count: 0,
  message: "Hello"
});

effect(() => {
  console.log(`Count: ${state.count}, Message: ${state.message}`);
});
// → "Count: 0, Message: Hello"

state.count++;
// → "Count: 1, Message: Hello"

state.message = "World";
// → "Count: 1, Message: World"
```

完璧に動く。Proxyだけでここまでできる。

去年の社内プロジェクトでは、結局この自作実装を使わずに素直に`@vue/reactivity`をインストールした。本番で使うには、computed、watch、scheduler、cleanup処理とか色々足りない。でも基本原理を理解してると、Vueのドキュメント読むときの理解度が全然違う。

## ネストしたオブジェクトの扱い

上のコードだと、ネストしたオブジェクトがreactiveにならない。

```js
const state = reactive({
  user: { name: "Taro" }
});

effect(() => {
  console.log(state.user.name);
});
// → "Taro"

state.user.name = "Hanako";
// 再実行されない！
```

`state.user`はreactiveだけど、`state.user.name`は普通のオブジェクトのプロパティ。だから変更が検知されない。

解決策は、getハンドラで返す値もreactiveにする：

```js
function reactive(target) {
  return new Proxy(target, {
    get(target, key, receiver) {
      track(target, key);
      const result = Reflect.get(target, key, receiver);

      // 値がオブジェクトならreactiveにする
      if (typeof result === 'object' && result !== null) {
        return reactive(result);
      }

      return result;
    },
    set(target, key, value, receiver) {
      const result = Reflect.set(target, key, value, receiver);
      trigger(target, key);
      return result;
    }
  });
}
```

これでネストしたオブジェクトも追跡できる。Vueの実装も基本的には同じアプローチ。ただし、無限再帰を防ぐためにWeakMapでキャッシュしてる。

個人的には、この「アクセス時に動的にProxyを作る」パターンが気に入ってる。最初から全部Proxyにするより、実際にアクセスされたときだけラップする方が効率的。

## watchの実装

effectだけだと、「何が変わったのか」がわからない。watchは変更前後の値を取れる。

```js
function watch(getter, callback) {
  let oldValue;

  effect(() => {
    const newValue = getter();
    if (newValue !== oldValue) {
      callback(newValue, oldValue);
      oldValue = newValue;
    }
  });
}
```

使い方：

```js
const state = reactive({ count: 0 });

watch(
  () => state.count,
  (newValue, oldValue) => {
    console.log(`${oldValue} → ${newValue}`);
  }
);

state.count = 1;
// → "0 → 1"
```

Vueの`watch()`もこんな感じで実装されてる。getter関数を実行してeffectを発動させて、値が変わったらcallbackを呼ぶ。シンプルだけど強力。

ちなみに、初回実行で`oldValue`が`undefined`になるの嫌だったら、最初にgetterを一度実行しておけばいい。Vueには`immediate: true`オプションもあるし、柔軟に調整できる。

## パフォーマンスの話

Proxyって遅いんじゃないの？って思うかもしれない。実際、素のオブジェクトアクセスと比べれば若干オーバーヘッドはある。でも、Vue 2の`Object.defineProperty`よりは速い。

Vue 3になってから、初期化コストが大幅に下がった。Vue 2だと、reactiveにする時点で全プロパティにgetter/setterを定義してた。Vue 3のProxyは遅延評価だから、実際にアクセスされるまで何もしない。

GitHub issue #4318では配列の最適化について議論されてて、配列の全インデックスにProxyトラップが走るのはコストが高いって話が出てた。でも、実用上は十分速い。むしろ、effectの実行スケジューリングとか、無駄な再計算を減らす方が重要。

自分が計測した範囲だと、100個のプロパティを持つオブジェクトをreactiveにして、全プロパティにアクセスして変更しても、1ミリ秒以内に終わる。ほとんどのアプリケーションで問題にならないレベル。

## 他のライブラリとの比較

ProxyベースのリアクティブシステムはVueだけじゃない。MobXも似たようなアプローチだし、Solidは別の方法で同じ問題を解いてる。

MobXは`observable()`でオブジェクトをラップして、`autorun()`や`reaction()`で依存関係を追跡する。Vueとほぼ同じ仕組み。違いは、Vueがコンポーネント単位でreactivityを管理してるのに対して、MobXはもっとグローバルにストアを扱う設計。

Solidは、シグナルベースの実装で、Vueより細かい粒度で依存関係を追跡する。Proxyは使わず、getter/setterだけで実装してる。その分軽量だし、パフォーマンスも良い。でも、APIの使い勝手はVueの方が直感的だと思う。

Reactは全然違うアプローチで、immutableなstateの更新と仮想DOMの差分計算で変更を検知する。リアクティブシステムは使ってない。どっちが良いって話じゃなくて、設計思想の違い。

## まとめ

Proxyだけでリアクティブシステムが作れるって発見は、個人的に結構大きかった。Vueの内部実装を読むまで、「黒魔術みたいな複雑なコードがあるんだろうな」って思ってたけど、実際はかなりシンプル。

結局、核心は3つだけ：
1. **Proxyで読み書きをインターセプト** - get/setハンドラで全部捕まえる
2. **WeakMapで依存関係を保存** - target → key → Set<effect>の3階層構造
3. **activeEffectで現在の実行文脈を追跡** - effectが実行中の時だけtrackする

このパターンが理解できれば、自分でミニマムなリアクティブライブラリを作れるし、Vueのソースコードを読むのもかなり楽になる。vuejs/coreの`packages/reactivity/src/reactive.ts`を読むと、本番で使えるレベルのエッジケース処理とか最適化が見えて面白い。

個人的には、小規模なプロジェクトだったら自作実装でも十分だと思う。でも、スケジューラとか非同期処理とか、細かい部分で詰まることが多いので、素直に`@vue/reactivity`を使う方が賢い。あのパッケージ、Vue本体から独立してるから、Reactでも普通に使える。状態管理ライブラリ要らずで済むケースは意外と多い。

残る疑問は、Proxyのブラウザサポートをどこまで気にするか。今ならIE11は完全に切っていいと思うけど、プロジェクトによっては難しい場合もある。まあ、そういう時はVue 2使えばいい。
