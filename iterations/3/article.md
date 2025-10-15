---
title: "TypeScript の const 型パラメータと推論の改善"
emoji: "🔒"
type: "tech"
topics: ["typescript", "javascript"]
published: true
---

## const 型パラメータって何だったっけ

TypeScript 5.0で入ったconst型パラメータ、最初見たとき「また新しい構文が...」と思って放置してたんだけど、最近zodライクなライブラリを書いてて「これ、めっちゃ便利じゃん」となった。

簡単に言うと、型パラメータに`const`修飾子をつけられる機能。

```typescript
function identity<const T>(value: T) {
  return value;
}

const result = identity({ x: 10, y: 20 });
// result: { readonly x: 10; readonly y: 20 }
```

従来は`as const`を呼び出し側で書く必要があったのが、関数の宣言側で制御できるようになった。地味だけどライブラリの設計が変わるレベルで便利。

## なぜ必要になったか

そもそもこの機能、Anders Hejlsbergが書いた[PR #51865](https://github.com/microsoft/TypeScript/pull/51865)で入ったんだけど、元々は#30680とか#41114あたりのissueで議論されてた話。

TypeScriptの型推論は基本的に「拡張可能な型」を優先する。

```typescript
// 従来
function makeConfig<T>(config: T) {
  return config;
}

const config = makeConfig({ mode: "development" });
// config: { mode: string }  ← "development"じゃなくてstring
```

これは普通のケースでは便利なんだけど、設定オブジェクトとかルーティング定義みたいな「リテラル型として扱いたい値」を返す関数だとユーザー側で毎回`as const`を書かないといけなかった。

```typescript
// ユーザーに負担を強いる設計
const config = makeConfig({ mode: "development" } as const);
```

「関数の作者がこの引数はリテラル型として扱いたいって決められたらいいのに」というモチベーションがあった。

## const型パラメータの挙動

`const`修飾子をつけた型パラメータは、推論時に以下のような処理が走る。

1. リテラル値は最も狭い型になる（文字列なら`"foo"`、数値なら`42`）
2. 配列は`readonly`になる
3. オブジェクトのプロパティも`readonly`になる

```typescript
function tuple<const T extends readonly unknown[]>(...args: T) {
  return args;
}

const result = tuple(1, "hello", true);
// result: readonly [1, "hello", true]
```

これ、配列とかタプルを扱う関数だとめちゃくちゃ便利。というか、なんで今まで無かったのか不思議なくらい。

:::details 内部実装の話（推測）

ドキュメントには詳しく書いてないんだけど、おそらく型推論の際にコンテキスト情報として`const`フラグが伝播してるっぽい。通常の推論では`widenLiteralType`が走るところを、`const`パラメータの場合はそのスキップが起きてる感じ？実装は見てないので完全に推測ですが。

:::

## 実際の使い所

最初これ書いた。

```typescript
function defineRoutes<T>(routes: T) {
  return routes;
}

const routes = defineRoutes({
  home: "/",
  about: "/about"
});
```

で、`routes.home`の型を取ろうとしたら`string`になってて「あー、これ`as const`必要なやつだ」ってなった。

```typescript
// 修正版
function defineRoutes<const T>(routes: T) {
  return routes;
}

const routes = defineRoutes({
  home: "/",
  about: "/about"
});
// routes: { readonly home: "/"; readonly about: "/about" }
```

これで`routes.home`が`"/"`型として扱われるようになった。ルーティングライブラリとか、設定管理系のライブラリを書くときに超便利。

あと、enumの代わりに使うパターンも良い。

```typescript
function defineEnum<const T extends readonly string[]>(values: T) {
  return values;
}

const Status = defineEnum(["pending", "success", "error"]);
// Status: readonly ["pending", "success", "error"]

type StatusType = typeof Status[number];
// StatusType: "pending" | "success" | "error"
```

enumって色々微妙な点があって個人的には避けたかったんだけど、この書き方ならリテラル型のユニオンとして扱える。

## 制約と注意点

`const`修飾子、使える場所に制限がある。

- 関数、メソッド、クラスの型パラメータ → OK
- インターフェースや型エイリアスの型パラメータ → エラー

```typescript
// これはエラー
interface Container<const T> {
  value: T;
}
```

理由は明確で、インターフェースや型エイリアスは値を持たないので推論のタイミングが存在しない。`const`修飾子は「値からの推論」に介入する機能なので、値のない型定義では意味がない。

あと、過度に使うと型が狭くなりすぎて逆に不便になることもある。

```typescript
function process<const T>(data: T) {
  // data はreadonly扱いになるので変更できない
  data.push(1); // Error: プロパティ 'push' は存在しません
}
```

基本的には「この関数はリテラル型を扱う前提」って決めてるAPIで使うべき。全部に`const`つければいいってもんじゃない。

## 他の推論改善との組み合わせ

TypeScript 5.0前後で推論周りの改善が色々入ってて、const型パラメータもその文脈で理解すると面白い。

例えば、satisfiesオペレータ（4.9で入った）と組み合わせると型安全性が増す。

```typescript
function defineConfig<const T extends Record<string, unknown>>(config: T) {
  return config;
}

const config = defineConfig({
  api: "https://example.com",
  timeout: 3000
} satisfies { api: string; timeout: number });
```

satisfiesで「最低限この型を満たす」ことを保証しつつ、const型パラメータで「実際の値は狭い型として推論する」という合わせ技。これは社内の設定ファイル管理で使ってて、型チェックと補完が両方効いて便利。

まだ試してないけど、5.5とか5.6で入った推論の改善とも相性良さそう。そのうち試したい。

## 実装の歴史的経緯

この機能、実はかなり前から議論されてた。#30680は2019年に立てられたissueで、「ジェネリック関数で`as const`相当のことができないか」って話。

当時の議論では色々な構文案が出てて、`<T as const>`とか`<const T extends unknown>`とか、今とは違う形も検討されてた。最終的にAnders Hejlsbergが「`const`修飾子でいいんじゃね」ってシンプルな案を出して、それがPR #51865として実装された。

ちなみに、TypeScript 5.0のbeta版が出たときTwitterで「constパラメータ便利すぎる」みたいなポストをいくつか見たけど、正式リリース後はあんまり盛り上がってない気がする。地味な機能だからかもしれない。

そういえば、JSDocでもconst型パラメータをサポートする話（#56634）が進んでるっぽい。実装されたかは追ってないけど。

## まとめというか

const型パラメータ、最初は「また構文が増えた」くらいにしか思ってなかったけど、ライブラリのAPIを設計する側になると必要性がよく分かる。

特にルーティング定義とか設定オブジェクトみたいな「値をそのまま型として使いたい」パターンで威力を発揮する。従来は`as const`を呼び出し側に強制してたのが、関数側で制御できるようになったのは大きい。

ただ、使い所は選んだ方がいい。全部に`const`つけると逆に不便になるし、「この関数はリテラル型を扱う前提」って明確な意図があるときに使うべき。

あと、個人的にはこの機能が入ったことで「TypeScriptの型推論は作者側がある程度コントロールできる」って方向性が明確になった気がする。satisfiesとかinferとか、最近の機能は全部その流れにある。型システムがどんどん表現力を増していく感じ、面白い。
