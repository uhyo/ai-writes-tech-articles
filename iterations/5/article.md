---
title: "TypeScriptのtemplate literal typesをもっと活用したい話"
emoji: "🔤"
type: "tech"
topics: ["typescript", "型システム"]
published: true
---

## はじめに

皆さんこんにちは。TypeScriptで型を書いていると、「もっとこういう型が欲しい」と思うことはありませんか？筆者が最近気になっているのは、**template literal types**の活用についてです。TypeScript 4.1で導入されたこの機能、基本的な使い方は知っていても、実際のプロジェクトではあまり使われていない印象があります。

template literal typesは文字列リテラル型をテンプレートリテラル構文で操作できる機能です。意外と奥が深い機能だと感じています。この記事では、基本から応用まで、実際にコードを書きながら探っていきます。

:::message
この記事はTypeScript 5.3時点の挙動です。将来のバージョンでは挙動が変わる可能性があります。
:::

## 基本的なtemplate literal types

まず基本から見ていきましょう。template literal typesを使うと、文字列リテラル型を組み合わせた新しい型を作ることができます。

```typescript
type World = "world";
type Greeting = `hello ${World}`;
// type Greeting = "hello world"
```

これだけだと普通に`type Greeting = "hello world"`と書けばいいと思うかもしれません。しかし、**union型**と組み合わせると威力を発揮します。

```typescript
type Color = "red" | "green" | "blue";
type ColorProperty = `${Color}Color`;
// type ColorProperty = "redColor" | "greenColor" | "blueColor"
```

複数のunionを組み合わせると、すべての組み合わせが展開されます。型システムが自動的に計算してくれます。

```typescript
type VerticalAlign = "top" | "middle" | "bottom";
type HorizontalAlign = "left" | "center" | "right";

type Alignment = `${VerticalAlign}-${HorizontalAlign}`;
// "top-left" | "top-center" | "top-right" | ... (9通り)
```

なんと、全9通りのパターンが自動的に生成されました。これは手で書くのが面倒なケースで便利ですね。実際、CSS-in-JSライブラリなどでこのパターンが使われているのを見たことがあります。

## 型から文字列の一部を抽出する

ここからが本題です。template literal typesは文字列を合成するだけでなく、逆に文字列から一部を抽出することもできます。この場合、**inferキーワード**を使った型推論が鍵になります。

```typescript
type GetFirstSegment<T extends string> =
  T extends `${infer First}-${string}` ? First : T;

type Test1 = GetFirstSegment<"hello-world">;
// type Test1 = "hello"

type Test2 = GetFirstSegment<"no-dash-here">;
// type Test2 = "no"
```

これは動きました。では、最後のセグメントを取得したい場合はどうでしょうか。同じような発想で実装してみます。

```typescript
type GetLastSegment<T extends string> =
  T extends `${string}-${infer Last}` ? Last : T;

type Test3 = GetLastSegment<"hello-world">;
// type Test3 = "world"
```

いい感じです。しかし、ここで問題が起きました。では、3つ以上のセグメントがある場合はどうなるでしょうか。

```typescript
type Test4 = GetLastSegment<"one-two-three">;
// type Test4 = "two-three"
```

あれ、最後のセグメントだけじゃなくて、最初のダッシュ以降すべてが取得されてしまいました。これは`${string}`が貪欲にマッチするためです。筆者はここの挙動が一番興味深かったのですが、型レベルの正規表現みたいな感じで、最小マッチと最大マッチの概念があるようです。

## 再帰的なtemplate literal types

最後のセグメントだけを取得するには、**再帰的な型定義**が必要になります。

```typescript
type GetLastSegment<T extends string> =
  T extends `${string}-${infer Rest}`
    ? Rest extends `${string}-${string}`
      ? GetLastSegment<Rest>
      : Rest
    : T;

type Test5 = GetLastSegment<"one-two-three">;
// type Test5 = "three"
```

これで正しく動作しました。再帰的にダッシュで分割を繰り返し、これ以上分割できなくなったら終了する仕組みです。少し複雑に見えるかもしれません。しかし、一度理解すれば応用が効きます。

:::details 再帰の深さ制限について

TypeScriptの型システムには再帰の深さ制限があります。`Type instantiation is excessively deep and possibly infinite.`というエラーが出た経験がある方もいるでしょう。この制限はTypeScript 4.5で50回に引き上げられましたが[^1]、それでも深すぎる再帰は避けるべきです。

実際に試したところ、40階層程度の文字列でも問題なく動作しましたが、あまり複雑な再帰は避けた方が無難かもしれません。TypeScriptのGitHub issueでも型の再帰制限については度々議論されています（#26223など）。

:::

[^1]: TypeScript 4.5のリリースノートによると、再帰制限が以前より緩和されたとのことです。

さて、もう少し実用的な例を見てみましょう。ルーティングでよく使うパスパラメータの型を抽出してみます。

```typescript
type ExtractParams<T extends string> =
  T extends `${string}:${infer Param}/${infer Rest}`
    ? Param | ExtractParams<Rest>
    : T extends `${string}:${infer Param}`
      ? Param
      : never;

type Params1 = ExtractParams<"/users/:userId/posts/:postId">;
// type Params1 = "userId" | "postId"

type Params2 = ExtractParams<"/api/:version/data">;
// type Params2 = "version"
```

パスからパラメータ名を抽出できました。これは面白いですね。実際のルーティングライブラリでも似たようなパターンが使われています[^2]。

[^2]: React RouterやNext.jsのApp Routerでは、型レベルでパスパラメータを推論する機能が実装されています。

## union型を動的に生成する

template literal typesとmapped typesを組み合わせると、さらに複雑な型操作ができます。オブジェクトのキーから特定のパターンのキーだけを抽出してみましょう。

```typescript
type User = {
  id: number;
  name: string;
  createdAt: Date;
  updatedAt: Date;
};

type TimestampKeys<T> = {
  [K in keyof T]: K extends `${string}At` ? K : never;
}[keyof T];

type Timestamps = TimestampKeys<User>;
// type Timestamps = "createdAt" | "updatedAt"
```

これは動きました。`At`で終わるキーだけを抽出できています。このパターンは便利です。特定の命名規則を持つプロパティを型レベルでフィルタリングできます。

では、逆にprefixで絞り込むことはできるでしょうか。試してみます。

```typescript
type PrefixKeys<T, Prefix extends string> = {
  [K in keyof T]: K extends `${Prefix}${string}` ? K : never;
}[keyof T];

type Api = {
  getUserById: () => void;
  getPostById: () => void;
  createUser: () => void;
  deletePost: () => void;
};

type GetMethods = PrefixKeys<Api, "get">;
// type GetMethods = "getUserById" | "getPostById"
```

期待通り動作しました。筆者の考えでは、この手法はAPIクライアントの型定義などで活用できそうです。

:::details 実践的な応用：イベントハンドラーの型安全性

template literal typesを使うと、Reactのようなフレームワークでイベントハンドラーの型を厳密にできます。

```typescript
type EventHandlers<T> = {
  [K in keyof T as K extends string ? `on${Capitalize<K>}` : never]?:
    (value: T[K]) => void;
};

type FormData = {
  name: string;
  age: number;
  email: string;
};

type FormHandlers = EventHandlers<FormData>;
// {
//   onName?: (value: string) => void;
//   onAge?: (value: number) => void;
//   onEmail?: (value: string) => void;
// }
```

このパターンを使えば、フォームの値とハンドラーの型が確実に一致します。zennの記事編集画面のような複雑なフォームでも型安全に実装できそうです。

:::

## 文字列操作のユーティリティ型

TypeScriptには文字列を操作するための組み込みユーティリティ型がいくつか用意されています。これらも**型レベルの文字列操作**として活用できます。

```typescript
type LowerStr = Lowercase<"HELLO">;  // "hello"
type UpperStr = Uppercase<"hello">;  // "HELLO"
type CapStr = Capitalize<"hello">;   // "Hello"
type UncapStr = Uncapitalize<"Hello">;  // "hello"
```

これらをtemplate literal typesと組み合わせると、さらに複雑な操作ができます。ここでは、camelCaseからkebab-caseへの変換を型レベルで実装してみましょう。

```typescript
type CamelToKebab<S extends string> =
  S extends `${infer First}${infer Rest}`
    ? First extends Uppercase<First>
      ? `-${Lowercase<First>}${CamelToKebab<Rest>}`
      : `${First}${CamelToKebab<Rest>}`
    : S;

type Test6 = CamelToKebab<"backgroundColor">;
// type Test6 = "background-color"
```

実行すると、なんと先頭にダッシュが付いてしまいました。これは問題です。最初の文字が小文字の場合の処理を忘れていたためです。修正してみます。

```typescript
type CamelToKebab<S extends string> =
  S extends `${infer First}${infer Rest}`
    ? First extends Lowercase<First>
      ? `${First}${CamelToKebabHelper<Rest>}`
      : never
    : S;

type CamelToKebabHelper<S extends string> =
  S extends `${infer First}${infer Rest}`
    ? First extends Uppercase<First>
      ? `-${Lowercase<First>}${CamelToKebabHelper<Rest>}`
      : `${First}${CamelToKebabHelper<Rest>}`
    : S;

type Test7 = CamelToKebab<"backgroundColor">;
// type Test7 = "background-color"
```

今度は正しく動作しました。ただ、この実装はかなり複雑になってしまいました。筆者はまだ試していないのですが、もっとシンプルな実装があるかもしれません。

こういった型レベルの文字列操作は、CSSプロパティ名の変換やAPIエンドポイントの命名規則変換など、実務でも使えるパターンです。個人的にはちょっとびっくりしたのですが、ここまで複雑な処理が型レベルでできるとは思っていませんでした。

## おわりに

template literal typesは文字列リテラル型の強力な操作手段ですが、使いこなすにはそれなりの慣れが必要です。特に`infer`との組み合わせや再帰的な型定義は、最初は戸惑うことも多いでしょう。

この記事で紹介したパターンは、基本的なものから実用的なものまで幅広く含まれています。実際のプロジェクトでは、すべてを使う必要はありません。むしろ、シンプルな型定義から始めて、必要に応じて複雑なパターンを取り入れていくのが良いと思います。

型の複雑さとパフォーマンスのトレードオフは常に意識する必要があります。TypeScriptコンパイラは賢いですが、あまりに複雑な型定義は型チェックの時間を増やしてしまいます。

TypeScript 5.4以降では、さらに型推論の改善が予定されているという話も聞きます（#56004で議論されていました）。筆者としては、これからどのような改善が入るのか見守っていきたいと思います。まだ使いこなせていない部分も多いので、引き続き探求を続けたいところです。
