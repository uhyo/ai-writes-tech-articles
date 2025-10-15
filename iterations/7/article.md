---
title: "Proxy と Reflect で実現する実践的メタプログラミング"
emoji: "🪞"
type: "tech"
topics: ["javascript", "typescript", "proxy", "metaprogramming"]
published: true
---

皆さんこんにちは。JavaScript の Proxy と Reflect、使ったことありますか。名前は知ってるけど実際に書いたことはない、という人が多いんじゃないかと思います。

去年、社内の API クライアントライブラリを設計し直す機会があったんですが、そのとき Proxy を使ってメソッドチェーンを自動生成する仕組みを作りました。結果として 300 行くらいあったボイラープレートコードが 50 行程度に圧縮されて、メンテコストが劇的に下がった経験があります。

Proxy って「オブジェクトの操作をフックできる機能」くらいの理解でとどまりがちなんですが、実はもっと根本的な概念があります。それは **「オブジェクトの意味を再定義できる」** という点です。プロパティアクセスが何を意味するのか、関数呼び出しが何を意味するのか、そういう言語レベルの意味を自分で決められる。この概念を理解すると Proxy の使いどころが一気に見えてきます。

## Proxy の基本と Reflect の役割

Proxy は ES2015 で導入された機能で、オブジェクトの基本操作（プロパティアクセス、代入、関数呼び出しなど）をインターセプトできます。

```typescript
const handler = {
  get(target, prop) {
    console.log(`Reading ${String(prop)}`);
    return target[prop];
  }
};

const obj = { name: 'test' };
const proxy = new Proxy(obj, handler);

console.log(proxy.name); // "Reading name" → "test"
```

ここで Reflect が登場します。Reflect は Proxy のハンドラ内でデフォルト動作を呼び出すための API です。

```typescript
const handler = {
  get(target, prop, receiver) {
    console.log(`Reading ${String(prop)}`);
    return Reflect.get(target, prop, receiver); // デフォルト動作
  }
};
```

最初見たとき「なんで `target[prop]` じゃダメなの？」って思ったんですが、実は `receiver` の扱いが違うんです。getter を持つオブジェクトで差が出ます。

```typescript
const base = {
  _value: 10,
  get value() {
    return this._value; // この this が重要
  }
};

// target[prop] だと this が target になる
const handler1 = {
  get(target, prop) {
    return target[prop]; // ❌ this が base になる
  }
};

// Reflect.get だと this が receiver (proxy) になる
const handler2 = {
  get(target, prop, receiver) {
    return Reflect.get(target, prop, receiver); // ✅ this が proxy になる
  }
};
```

あ、これ説明が分かりにくいかも。要するに、Reflect を使わないと継承チェーンで this の参照がおかしくなるケースがあるってことです。詳細は MDN の Proxy ページに譲ります。

## 型安全な API クライアント生成

さっき触れた API クライアントの話を掘り下げます。REST API のエンドポイントって大体こういう構造になりますよね。

```
GET /users/:id
POST /users/:id/posts
DELETE /posts/:postId/comments/:commentId
```

従来は各エンドポイントに対してメソッドを手書きしてましたが、Proxy を使うとパスの構築を動的に生成できます。

```typescript
// 最初こう書いた
function createClient(baseURL: string) {
  return new Proxy({}, {
    get(target, prop) {
      return (id: string) => {
        const path = `/${String(prop)}/${id}`;
        return fetch(`${baseURL}${path}`);
      };
    }
  });
}

const client = createClient('https://api.example.com');
client.users('123'); // GET /users/123
```

これだと型がつかないので TypeScript で使えません。そこで型定義を追加します。

```typescript
type APIClient = {
  users: (id: string) => {
    posts: (postId: string) => Promise<Response>;
  };
  posts: (id: string) => {
    comments: (commentId: string) => Promise<Response>;
  };
};

function createClient<T>(baseURL: string, path: string[] = []): T {
  return new Proxy(() => {}, {
    get(target, prop) {
      return createClient(baseURL, [...path, String(prop)]);
    },
    apply(target, thisArg, args) {
      const fullPath = [...path, args[0]].join('/');
      return fetch(`${baseURL}/${fullPath}`);
    }
  }) as T;
}

const client = createClient<APIClient>('https://api.example.com');
await client.users('123').posts('456'); // GET /users/123/posts/456
```

あ、これだと HTTP メソッドが GET 固定ですね。POST や DELETE も欲しい場合は、もう一段 Proxy を重ねて `.get()` `.post()` のようなメソッドを生やす必要があります。この辺の実装は省略しますが、考え方は同じです。

ちなみにこのパターン、zod とか tRPC みたいな型安全ライブラリでもよく使われてます。Proxy + TypeScript の型推論を組み合わせると、ランタイムとコンパイル時の両方で安全性を担保できるのが強いです。

このアプローチの面白いところは、**メソッドチェーンそれ自体が遅延評価されたパス構築になってる**という点です。`client.users('123').posts` の時点では何も実行されず、最後の関数呼び出しで初めてパスが確定します。普通のオブジェクト指向だと各メソッドが即座に実行されますが、Proxy を使うとメソッド呼び出しの「意味」を変えられる。これがメタプログラミングの本質です。

## リアクティブシステムの実装

Vue 3 とかで使われてる Proxy ベースのリアクティビティ、あれも割とシンプルに実装できます。

```typescript
let currentEffect = null;

function effect(fn) {
  currentEffect = fn;
  fn();
  currentEffect = null;
}

function reactive(obj) {
  const deps = new Map(); // プロパティごとの依存関係

  return new Proxy(obj, {
    get(target, prop, receiver) {
      if (currentEffect) {
        if (!deps.has(prop)) {
          deps.set(prop, new Set());
        }
        deps.get(prop).add(currentEffect);
      }
      return Reflect.get(target, prop, receiver);
    },
    set(target, prop, value, receiver) {
      const result = Reflect.set(target, prop, value, receiver);
      if (deps.has(prop)) {
        deps.get(prop).forEach(fn => fn());
      }
      return result;
    }
  });
}
```

使い方はこんな感じです。

```typescript
const state = reactive({ count: 0 });

effect(() => {
  console.log(`Count is ${state.count}`);
});
// "Count is 0" と表示される

state.count++; // "Count is 1" と自動で再実行される
```

あ、これバグあるな。`effect` の中で複数のプロパティにアクセスしたとき、どのプロパティが変更されたかで実行タイミングがずれるケースがあります。実際の Vue 3 はもっと複雑で、スケジューリングとか batch 更新とか色々やってますが、基本原理はこれです。

そういえば、この実装だとネストしたオブジェクトが reactive にならないですね。

```typescript
const state = reactive({ user: { name: 'test' } });
state.user.name = 'changed'; // これは反応しない
```

直すなら `get` トラップで返す値も reactive にラップする必要があります。

```typescript
get(target, prop, receiver) {
  const value = Reflect.get(target, prop, receiver);

  if (currentEffect) {
    if (!deps.has(prop)) {
      deps.set(prop, new Set());
    }
    deps.get(prop).add(currentEffect);
  }

  // オブジェクトなら再帰的に reactive 化
  if (value !== null && typeof value === 'object') {
    return reactive(value);
  }

  return value;
}
```

まあ、これだと毎回新しい Proxy が作られてパフォーマンス的に微妙なので、実用的には WeakMap でキャッシュする必要があります。この辺は Vue のソースコード読むと詳しく書いてあります（github.com/vuejs/core の reactivity パッケージ）。

## デバッグツールとしての活用

Proxy、デバッグにも使えます。前に似たことやった気がするんですが、オブジェクトへのアクセスを全部ログに吐く Proxy を仕込むと、「どこでこの値が変更されたんだ？」みたいなバグ調査が楽になります。

```typescript
function debugProxy(obj, name = 'object') {
  return new Proxy(obj, {
    get(target, prop) {
      console.log(`[${name}] GET ${String(prop)}`);
      const value = Reflect.get(target, prop);

      // 関数なら呼び出しもログる
      if (typeof value === 'function') {
        return new Proxy(value, {
          apply(target, thisArg, args) {
            console.log(`[${name}] CALL ${String(prop)}`, args);
            return Reflect.apply(target, thisArg, args);
          }
        });
      }

      return value;
    },
    set(target, prop, value) {
      console.log(`[${name}] SET ${String(prop)} =`, value);
      console.trace(); // スタックトレースも出す
      return Reflect.set(target, prop, value);
    }
  });
}
```

使い方としては、怪しいオブジェクトを debugProxy でラップしてコンソール眺めるだけです。

```typescript
const user = debugProxy({ name: 'test', age: 20 }, 'user');
user.name; // "[user] GET name"
user.age = 21; // "[user] SET age = 21" + スタックトレース
```

これ、本番コードに残すと大量のログで死ぬので開発環境限定ですが、複雑な状態管理のデバッグには結構便利です。たぶん2020年くらいに Redux のストアに仕込んだことがあります。

## パフォーマンスとトレードオフ

Proxy、便利なんですが遅いです。特に頻繁にアクセスされるオブジェクトだとオーバーヘッドが無視できません。

一応ベンチマーク取ったことがあるんですが、プロパティアクセスが Proxy 経由だと生のオブジェクトの 3-5 倍くらい遅くなります。といっても、1 アクセスあたりナノ秒単位の差なので、よほどホットパスでなければ問題になりません。

使いどころとしては以下の通りです。

- ✅ API クライアントみたいな初期化時の構築
- ✅ 開発ツールやデバッグ機能
- ✅ フレームワークの内部実装（Vue のリアクティビティとか）
- ❌ ループ内で数万回アクセスされるオブジェクト
- ❌ ゲームエンジンみたいな超高速が必要な処理

あと、Proxy は IE11 で使えません。Polyfill も存在しないので、レガシーブラウザサポートが必要なら諦めるしかないです。まあ、2025 年にもなって IE のこと考える必要ある現場はそんなにないと思いますが。

## 他のトラップと応用例

Proxy のトラップは `get` と `set` だけじゃなくて、全部で 13 個あります。

- `has`: `in` 演算子
- `deleteProperty`: `delete` 演算子
- `ownKeys`: `Object.keys()` とか
- `getPrototypeOf`: `Object.getPrototypeOf()`
- `setPrototypeOf`: `Object.setPrototypeOf()`
- あと色々

面白いのは `has` トラップで、これを使うと存在しないプロパティも「存在する」と嘘をつけます。

```typescript
const infiniteProps = new Proxy({}, {
  has(target, prop) {
    return true; // 何を聞かれても「ある」と答える
  },
  get(target, prop) {
    return `Property ${String(prop)} is virtual`;
  }
});

console.log('anything' in infiniteProps); // true
console.log(infiniteProps.whatever); // "Property whatever is virtual"
```

使いどころあるのか怪しいですが、テストのモック作成とかで使えるかもしれません。まだ実践で試してないので分からないです。

## まとめ

Proxy と Reflect、地味な存在ですが使いこなすとコードの抽象度が一段上がります。特に型安全なライブラリ設計とかリアクティブシステムみたいな、「言語の意味を拡張する」場面では必須の技術です。

とはいえ万能ではなくて、パフォーマンスとのトレードオフは常に意識する必要があります。あとデバッグが難しくなるので、Proxy を重ねすぎると後で自分が困ります（経験談）。

そのうち WeakMap と組み合わせたメモ化の話とか、Symbol.toStringTag を使った型偽装の話も書きたいんですが、今回はこの辺で。
