---
title: "TypeScript 5.5の型レベル計算でTemplate Literal Typesの限界を試す"
emoji: "🔤"
type: "tech"
topics: ["typescript", "type", "template-literal"]
published: true
---

皆さんこんにちは。TypeScript 5.5のベータがリリースされ、**型推論の改善**が話題になっています。筆者は最近、自分のプロジェクトでルーティング定義の型安全性を向上させようとして、**Template Literal Types**による**型レベル計算**にどっぷりハマっていました。今回は、Template Literal Typesを使った型レベルでの文字列処理と、その実用的な応用例を試していきます。

:::message
この記事はTypeScript 5.5 beta時点の挙動に基づいています。正式リリースでは挙動が変わる可能性があります。
:::

## Template Literal Typesの基本

Template Literal Typesは、TypeScript 4.1で導入された機能で、文字列リテラル型をテンプレートリテラル記法で組み合わせることができます。基本的な使い方は非常にシンプルです。

```typescript
type Greeting = `Hello, ${string}`;

const valid: Greeting = "Hello, World";  // OK
const invalid: Greeting = "Hi, World";   // Error
```

この例は単純すぎて面白くないのですが、実際にはもっと複雑な型レベル計算が可能です。例えば、特定のパターンに基づいた文字列を生成する型を作ることができます。これはAPIルーティングの型定義などで有用です。

```typescript
type HttpMethod = "GET" | "POST" | "PUT" | "DELETE";
type Endpoint = "/users" | "/posts" | "/comments";
type ApiRoute = `${HttpMethod} ${Endpoint}`;

const route: ApiRoute = "GET /users";  // OK
```

これを実行すると、`ApiRoute`型は16個のユニオン型（4 × 4）として推論されました。型レベルでの直積計算が自動的に行われるわけです。

## 型レベル計算の応用：文字列の変換

さて、ここからが本題です。Template Literal Typesは単なる文字列の連結だけでなく、**条件型**と組み合わせることで文字列の変換処理を型レベルで実現できます。

まずは基本的な例として、snake_caseをcamelCaseに変換する型を作ってみます。文字列の変換は型レベル計算の典型的なユースケースです。

```typescript
type SnakeToCamel<S extends string> =
  S extends `${infer Head}_${infer Tail}`
    ? `${Head}${Capitalize<SnakeToCamel<Tail>>}`
    : S;

type Test1 = SnakeToCamel<"user_name">;  // "userName"
type Test2 = SnakeToCamel<"user_profile_image">;  // "userProfileImage"
```

最初、このコード書いたときに「あ、再帰が動くのか」と少し驚きました。TypeScriptの型システムは再帰的な型定義をサポートしており、文字列を段階的に処理できます。この仕組みを理解すると、様々な文字列変換が実現可能になります。

しかし、ここで問題が発生しました。

```typescript
type Test3 = SnakeToCamel<"very_long_variable_name_with_many_underscores_here">;
// Type instantiation is excessively deep and possibly infinite.
```

再帰の深さ制限に引っかかったようです。TypeScriptは型レベルの再帰に制限を設けており、深すぎる再帰はエラーになります。これはTypeScript 5.0以降で若干緩和されましたが、完全には解決されていません（issue #45711で議論されています）。実用上、この制限は常に意識する必要があります。

## より複雑な例：パスパラメータの型付け

実用的な例として、Expressライクなルーティングパスからパラメータを抽出する型を作ってみます。筆者はこれを実際にWebフレームワークの型定義で使おうとしていました。

```typescript
type ExtractParams<Path extends string> =
  Path extends `${infer _Start}:${infer Param}/${infer Rest}`
    ? { [K in Param | keyof ExtractParams<`:${Rest}`>]: string }
    : Path extends `${infer _Start}:${infer Param}`
    ? { [K in Param]: string }
    : {};

type Route1 = ExtractParams<"/users/:userId">;
// { userId: string }

type Route2 = ExtractParams<"/users/:userId/posts/:postId">;
// { userId: string; postId: string }
```

これを実行すると、期待通りの型が生成されました。パスからパラメータ名を正確に抽出できています。しかし、よく見ると型定義が少し冗長です。というのも、パスの終端とそうでない場合で分岐を書く必要があるからです。

もっと賢い方法がないか考えて、conditional typesのパターンマッチを工夫してみました。

```typescript
type ExtractParams<Path extends string> =
  Path extends `${string}:${infer Param}/${infer Rest}`
    ? { [K in Param]: string } & ExtractParams<`/${Rest}`>
    : Path extends `${string}:${infer Param}`
    ? { [K in Param]: string }
    : {};
```

こちらの方が若干シンプルになり、交差型を使ってパラメータを合成しています。実際のプロジェクトで試したところ、ネストが深いルートでも正しく型が推論されることを確認しました。このパターンは実用的なルーティングライブラリでも十分に使えそうです。

## 限界に挑戦：再帰的な型計算

ここからは、Template Literal Typesの限界を探るために、より複雑な型レベル計算に挑戦してみます。どこまで複雑な処理が可能なのか試していきます。

文字列を反転させる型を書いてみました。これはパズル的な要素が強いですが、型システムの表現力を測る良い例です。

```typescript
type Reverse<S extends string> =
  S extends `${infer First}${infer Rest}`
    ? `${Reverse<Rest>}${First}`
    : S;

type Test = Reverse<"hello">;  // "olleh"
```

驚いたことに、これは正常に動作しました。短い文字列であれば問題なく反転できています。しかし、長い文字列で試すと問題が発生します。

```typescript
type LongTest = Reverse<"this_is_a_very_long_string_that_will_cause_problems">;
// Type instantiation is excessively deep and possibly infinite.
```

やはり再帰の深さ制限に引っかかりました。筆者は「型レベルでの文字列処理は実用的なのか」と疑問に思い始めました。この限界は型システムの設計上避けられないものかもしれません。

さらに、もっと複雑な例として、文字列の長さを計算する型を書いてみます。これもアキュムレータパターンを使った興味深い例です。

```typescript
type StringLength<
  S extends string,
  Acc extends unknown[] = []
> = S extends `${infer _First}${infer Rest}`
  ? StringLength<Rest, [...Acc, unknown]>
  : Acc["length"];

type Len1 = StringLength<"hello">;  // 5
type Len2 = StringLength<"typescript">;  // 10
```

この型は、文字列を一文字ずつ分解しながら配列に要素を追加していき、最終的に配列の`length`を返すという仕組みです。一種のアキュムレータパターンを型レベルで実現しています。関数型プログラミングの概念を型システムで表現している点が面白いです。

ただ、これも長い文字列では失敗します。予想通りの結果でした。

```typescript
type Len3 = StringLength<"this_is_a_string_with_more_than_forty_characters_in_total">;
// Type instantiation is excessively deep and possibly infinite.
```

個人的には、ここで「型レベル計算の実用性には限界がある」という結論に至りました。短い文字列やパスパラメータのような限定的なケースでは有用ですが、汎用的な文字列処理には向いていません。この制約を理解した上で使いどころを見極める必要があります。

:::details TypeScript 5.2以降の改善について

TypeScript 5.2では、型推論のパフォーマンスが改善され、一部の再帰的な型がより深くまで展開できるようになりました。しかし、根本的な制限は残っています。将来的には、型レベル計算のための専用構文が追加されるかもしれません（推測ですが）。

:::

## Template Literal Typesの実用的な使い道

限界を知った上で、どこで使うべきかを考えてみます。筆者の経験では、以下のようなケースで非常に有効でした。

1. ルーティングの型付け: 前述のパスパラメータ抽出のように、限定的なパターンマッチには最適です。
2. CSS-in-JSの型安全性: `padding-${number}px`のような型を定義することで、スタイルプロパティの値を制限できます。
3. イベント名の生成: `on${Capitalize<EventName>}`のようなパターンで、イベントハンドラの名前を型安全に扱えます。

一方で、避けるべき使い方もあります。これらは経験的に問題が発生しやすいパターンです。

- 長い文字列の処理（再帰の深さ制限に引っかかる）
- 複雑な文字列変換（型エラーのデバッグが困難）
- ランタイムでの文字列処理が必要な場合（型レベルでは不十分）

筆者が開発中のルーティングライブラリでは、パスパラメータの型付けにTemplate Literal Typesを使っていますが、それ以外の文字列処理はランタイムに任せています。型システムと実行時処理のバランスが重要だと感じています。適材適所の判断が求められます。

## まとめと今後の展望

今回、Template Literal Typesを使った型レベル計算の可能性と限界を探ってみました。基本的な文字列の組み合わせから、再帰的な型計算まで試しましたが、再帰の深さ制限が実用上の大きな制約となることが分かりました。これは現時点での型システムの重要な制約です。

TypeScript 5.5では、型推論の改善が進んでいますが、型レベル計算の根本的な限界は変わっていません。パフォーマンスの向上は見られるものの、構造的な制約は残っています。筆者としては、今後のTypeScriptで型レベル計算専用の機能が追加されるのか、それとも現在の仕組みで十分とされるのか、見守っていきたいと思います。

実用的には、限定的なパターンマッチや短い文字列の処理に絞って使うのが賢明です。複雑な文字列処理が必要な場合は、ランタイムでの実装と組み合わせることを検討すべきでしょう。この使い分けが型安全性と実用性のバランスを取る鍵になります。

まだ試していないこととして、TypeScript 5.5で追加される予定の型推論改善が、再帰的な型計算にどの程度影響するのかは気になっています。ベータ版では大きな変化は見られませんでしたが、正式リリースでさらなる改善があるかもしれません。継続的にウォッチしていく価値がある領域だと思います。
