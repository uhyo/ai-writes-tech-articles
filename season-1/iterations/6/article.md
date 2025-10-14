---
title: "TypeScriptのConditional Typesで型レベルロジックを組む：実践パターンと落とし穴"
emoji: "🔀"
type: "tech"
topics: ["typescript", "型システム", "conditionaltype"]
published: true
---

## はじめに

TypeScriptのConditional Typesって、最初に見たときは「三項演算子が型でも使えるのか、便利だな」くらいに思ってたんですけど、実際に使い込むとそんな単純な話じゃないんですよね。型レベルでロジックを組める、っていうのは別に比喩じゃなくて、本当にif文みたいな分岐処理を型の世界で実装できる。

で、何がややこしいって、JavaScriptのロジックとは異なるルールで動いてるところです。特にDistributive Conditional Typesとか、`infer`キーワードとか、最初は「なんでこう動くの？」って混乱することが多い。

今回はConditional Typesの実践的なパターンと、実際にハマりやすい落とし穴を掘り下げます。単純な型の絞り込み以上のことをやりたいときに、どう使えばいいのか。

## Conditional Typesの基本と分配法則

まずは基本から。Conditional Typesの構文はこうです：

```typescript
T extends U ? X : Y
```

`T`が`U`に代入可能なら`X`、そうでなければ`Y`になる。これ自体は直感的ですよね。

```typescript
type IsString<T> = T extends string ? true : false;

type A = IsString<string>; // true
type B = IsString<number>; // false
```

で、ここからがややこしい。**ユニオン型をConditional Typeに渡すと、分配法則が働きます**[^1]。

```typescript
type Result = IsString<string | number>;
// これは (string extends string ? true : false) | (number extends string ? true : false)
// つまり true | false に展開される
```

この動作、最初は「なんで勝手に分解されるの？」って思うんですけど、TypeScriptの型システムの設計としては理にかなってます。ユニオン型っていうのは「AまたはB」を表現してるわけで、Conditional Typeで処理するときも「Aの場合」と「Bの場合」をそれぞれ評価する、という動作になる。

ところで、この分配法則が**働かない**場合もあります。型パラメータを`[]`で囲むと、分配を抑制できる：

```typescript
type IsStringNonDist<T> = [T] extends [string] ? true : false;

type Result2 = IsStringNonDist<string | number>; // false
// ユニオン全体をstringと比較するので、false
```

実務だと、「ユニオン型全体を一つの型として扱いたい」ケースがあって、そういうときはこのテクニックが必須。

[^1]: Distributive Conditional Typesと呼ばれる機能です。TypeScript 2.8で導入されました（https://github.com/microsoft/TypeScript/pull/21316）。

## inferキーワードで型のパターンマッチ

`infer`キーワードはConditional Typesの中核機能です。型の中から一部分を「抽出」できる。

```typescript
type ReturnType<T> = T extends (...args: any[]) => infer R ? R : never;
```

このコード、何やってるかというと、関数型`T`が「何らかの引数を取って何かを返す関数」という形にマッチするなら、その戻り値の型を`R`として取り出してる。マッチしなければ`never`。

関数の戻り値を取得する`ReturnType`はTypeScript標準で提供されてますが、自分で実装するとこうなります。で、この`infer`を使うと、かなり複雑な型操作ができる。

去年、社内の古いExpress APIをTypeScript化するプロジェクトで、パスパラメータを型レベルで抽出する仕組みを作ったんですけど（元々は全部`any`で書かれててカオスだった）、最初は「stringから正規表現みたいに抽出できないかな？」って考えてました。実際にやってみると：

```typescript
type ExtractParams<Path> =
  Path extends `${infer _Start}/:${infer Param}/${infer Rest}`
    ? { [K in Param]: string } & ExtractParams<`/${Rest}`>
    : Path extends `${infer _Start}/:${infer Param}`
    ? { [K in Param]: string }
    : {};

type Params = ExtractParams<"/users/:userId/posts/:postId">;
// { userId: string; postId: string }
```

Template Literal Typesと`infer`を組み合わせると、文字列のパターンマッチができます。このコード、再帰的に`:param`形式を探して型を構築してる。

ただ、このコードには穴があります。パラメータ名が重複したときに`&`で型を結合してるから、最初のパラメータが消える[^2]。実務では重複チェックのロジックを追加する必要がありました。気づいたら3日溶けてたんですけど、そもそも「型レベルでどこまでやるべきか」を考え直すきっかけになった。

[^2]: `{ userId: string } & { userId: string }`は`{ userId: string }`になりますが、`{ userId: string } & { id: string }`から先に評価された場合、結果が変わります。TypeScriptの型結合の順序は保証されていません。

## 型レベルでの再帰とループ処理

さて、先ほどの話に戻ると、Conditional Typesは再帰できます。これが強力なんですよ。

配列の要素を全部取り出す`Flatten`型を実装するなら：

```typescript
type Flatten<T> = T extends (infer U)[]
  ? U extends any[]
    ? Flatten<U>
    : U
  : T;

type Nested = Flatten<[1, [2, [3, [4]]]]>;
// 1 | 2 | 3 | 4 が期待値だけど...
```

実はこのコード、深くネストした配列を完全にflattenできません。TypeScriptには型の再帰深度制限があって（だいたい50レベル）、それを超えると`Type instantiation is excessively deep and possibly infinite`エラーが出る。

実装を見ると、TypeScript内部では再帰チェックのカウンターがあって、深すぎる再帰を防いでます。これはパフォーマンスのための制限[^3]。

実務で本当にflattenが必要なケースってそんなに深くネストしないので、5階層くらいまで明示的に展開する実装にするのが現実的です：

```typescript
type Flatten<T, Depth extends number = 5> =
  Depth extends 0
    ? T
    : T extends (infer U)[]
    ? Flatten<U, Prev<Depth>>
    : T;
```

深度制限をパラメータ化して、制限に達したら諦める。ちなみにこのコード、`Prev`っていう数値をデクリメントする型が必要で、それも型レベルで実装しないといけない（タプルを使ったテクニックとか）。やってられない。

[^3]: https://github.com/microsoft/TypeScript/blob/main/src/compiler/checker.ts の`#L17000`あたりに再帰深度チェックの実装があります。

## 実践パターン：関数のオーバーロード解決

Conditional Typesの実践的な使い道として、関数のオーバーロードを型で表現することがあります。

```typescript
type Fetch = {
  (url: string): Promise<string>;
  (url: string, options: { json: true }): Promise<any>;
};
```

こういうオーバーロードされた関数の型から、特定の呼び出しパターンの戻り値型を取得したい。でも`ReturnType`を使っても最後のオーバーロードしか取れない。

これを解決するには：

```typescript
type ReturnTypeOfCall<
  F extends (...args: any[]) => any,
  Args extends any[]
> = F extends (...args: Args) => infer R ? R : never;
```

引数の型を指定して、その引数パターンにマッチするオーバーロードの戻り値を取り出す。実際にはこれも完璧じゃなくて、オーバーロードの解決順序とか考慮しないといけないんですけど、基本的なアイデアはこう。

で、この手の型操作、やりすぎると危険です。コンパイル時間が爆発する。特に大規模な型定義を持つライブラリ（例えばAxiosとか）で複雑なConditional Typesを使うと、IDE補完が固まったりします。

## Conditional Typesを使うべきでないケース

個人的には、Conditional Typesは「他に方法がないとき」に使うものだと思ってます。

:::details 余談：型システムの表現力とトレードオフ
TypeScriptの型システムはチューリング完全に近い表現力を持ってますが、それは必ずしも良いことじゃない。型レベルで複雑なロジックを書けば書くほど、以下の問題が出てきます：

- **コンパイル時間の増加**：型チェックは毎回走るので、複雑な型はビルドを遅くします
- **エラーメッセージの悪化**：型エラーが読めなくなる
- **保守性の低下**：6ヶ月後に自分のコードを読んで理解できるか？
:::

具体的に避けるべきケース：

**1. 単純なユニオンで済む場合**

```typescript
// ❌ 過剰
type Status<T> = T extends "loading" ? { loading: true }
  : T extends "success" ? { data: any }
  : { error: string };

// ✅ こっちでいい
type Status =
  | { status: "loading" }
  | { status: "success"; data: any }
  | { status: "error"; error: string };
```

Discriminated Unionで表現できるなら、そっちの方がシンプルで型推論も効く。

**2. 型の自動推論で解決できる場合**

```typescript
// ❌ 過剰
type ReturnTypeWrapper<T extends (...args: any[]) => any> =
  (...args: Parameters<T>) => ReturnType<T>;

// ✅ ジェネリクスと推論で十分
function wrap<T extends (...args: any[]) => any>(fn: T): T {
  return fn;
}
```

型パラメータと推論を使えば、わざわざConditional Typesで分解・再構築する必要ない。

**3. 実行時にチェックすべきロジック**

型レベルで頑張りすぎて、本来ランタイムで検証すべきことまで型で表現しようとするケース。例えば文字列の長さとか、数値の範囲とか、そういうのは型じゃなくてバリデーション関数で処理すべきです。

## まとめ

Conditional Typesは強力だけど、使いどころを間違えると保守性を犠牲にします。

分配法則の動作とか、`infer`でのパターンマッチとか、理解すれば「型レベルでこんなことできるんだ」って驚きがあるんですけど、実務では**シンプルな型定義が最強**です。

ちなみに、TypeScript 5.0以降だと`const`型パラメータとか、新しい機能も増えてて、Conditional Typesに頼らずに解決できるケースも増えてきました。型システムは進化し続けてるので、「昔はConditional Typesでゴリ押しするしかなかったけど、今は別の方法がある」みたいなことも結構ある。

型レベルロジックの限界を知ること。それが実践で一番重要かもしれない。
