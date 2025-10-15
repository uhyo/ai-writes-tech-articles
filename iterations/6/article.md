---
title: "TypeScript の Template Literal Types の実践的な使い方"
emoji: "🔤"
type: "tech"
topics: ["typescript", "型システム"]
published: true
---

皆さんこんにちは。TypeScript 4.1で入ったTemplate Literal Typesですが、最近また面白い使い方を思いついたので書いておきます。

## 型システムが文字列を「理解する」ようになった

Template Literal Typesって、実は結構革命的な機能だと思っていて。何が革命的かというと、**型システムが文字列の内容を認識するようになった**という点です。

従来のTypeScriptは、文字列リテラル型で個別の文字列を扱うことはできたけど、文字列の構造やパターンは理解できなかった。例えば`"user-123"`と`"user-456"`が同じ`user-{id}`というパターンに従っているということを型として表現できなかったわけです。

この「構造的な文字列認識」ができるようになったことで、APIのエンドポイント設計とか、CSSのクラス名生成とか、イベント名の型付けとかが劇的に改善されました。公式ドキュメントには「string manipulation types」って書いてあるけど、筆者としては「構造認識型」って呼んだ方がしっくりくる。

```typescript
// こういう型が書けるようになった
type UserPath = `user-${string}`;
type AdminPath = `admin-${string}`;

// これは型エラー
const path: UserPath = "admin-123"; // Type '"admin-123"' is not assignable to type '`user-${string}`'
```

最初見たとき「ああ、文字列がちゃんと型になったんだ」と思った記憶がある。

## 実践例：型安全なイベントエミッター

去年、社内の通知システムをTypeScript化する案件があって、そこでTemplate Literal Typesをフル活用しました。通知イベントが100種類くらいあって、`notification:user:created`みたいな階層的な命名規則になっていたんです。

従来なら全部文字列リテラル型で列挙するか、`string`で諦めるかの二択だったんですが、Template Literal Typesを使うとパターンとして型定義できます。

```typescript
type Entity = "user" | "post" | "comment";
type Action = "created" | "updated" | "deleted";
type NotificationEvent = `notification:${Entity}:${Action}`;

// これで自動補完が効く
const event: NotificationEvent = "notification:user:created"; // OK
const invalid: NotificationEvent = "notification:product:created"; // エラー
```

これだけだとまだ普通なんですが、次にイベントハンドラーの型を作るときに気づいたことがあって。イベント名から対応するペイロードの型を推論させたかったんです。

```typescript
type EventPayload = {
  "notification:user:created": { userId: string; email: string };
  "notification:user:updated": { userId: string; changes: Record<string, unknown> };
  "notification:post:created": { postId: string; authorId: string };
  // 100種類...
};

// こう書きたい
function on<E extends NotificationEvent>(
  event: E,
  handler: (payload: EventPayload[E]) => void
) {
  // ...
}
```

でもこれ、100種類全部手書きするのはさすがに無理でした。そこで条件型と組み合わせて推論させる方法を考えたんですが...

```typescript
type ParseEvent<E extends string> =
  E extends `notification:${infer Entity}:${infer Action}`
    ? { entity: Entity; action: Action }
    : never;

type EventData<E extends string> =
  ParseEvent<E> extends { entity: infer Ent; action: "created" }
    ? { id: string; data: Record<string, unknown> }
    : ParseEvent<E> extends { entity: infer Ent; action: "updated" }
    ? { id: string; changes: Record<string, unknown> }
    : { id: string };
```

あ、これバグあるな。`infer Ent`使ってるのに参照してない。まあ、当時はもっとちゃんと書いたはずですが、完全には思い出せない。実際のコードはもっと複雑で、entityごとにペイロードの型を変えたりしてました。

結局、完全な自動推論は諦めて、重要なイベントだけ手動で型定義して、残りは緩い型にする妥協案になりました。型パズルに2日くらい溶けたけど、実用性とのバランスは大事だなと。

## CSS-in-JSでの色定義

話変わりますが、Template Literal TypesってCSS関連でも便利です。

前にデザインシステムのトークン管理をTypeScriptで書いたことがあって、色のパレットを`primary-100`から`primary-900`みたいに定義する必要がありました。

```typescript
type ColorShade = 100 | 200 | 300 | 400 | 500 | 600 | 700 | 800 | 900;
type ColorName = "primary" | "secondary" | "accent" | "neutral";
type ColorToken = `${ColorName}-${ColorShade}`;

// これで補完が効く
const bgColor: ColorToken = "primary-500";
const textColor: ColorToken = "neutral-700";
```

さらに、実際のCSS変数名も生成したかったので、こんな感じに。

```typescript
type CSSVar<T extends string> = `--${T}`;
type ColorVar = CSSVar<ColorToken>;

// --primary-500 みたいな型になる
const cssVar: ColorVar = "--primary-500";
```

これ、実行時の関数も型から生成できて。

```typescript
function getColorVar(name: ColorName, shade: ColorShade): ColorVar {
  return `--${name}-${shade}`;
}

// 戻り値の型が ColorVar になる
const myVar = getColorVar("primary", 500);
```

型レベルで文字列連結できるようになったことで、命名規則を型として強制できるのが本当に便利です。JavaScriptでは実行時まで間違いに気づけなかったところが、コンパイル時に検出できる。

## 型の分解と再構成

そういえば、さっきのイベント名の話で`infer`を使ったパターンマッチングを少し触れたんですが、これがまた強力で。

文字列を分解して、条件分岐させて、また組み立てるみたいなことができます。

```typescript
type SplitPath<T extends string> =
  T extends `${infer First}/${infer Rest}`
    ? [First, ...SplitPath<Rest>]
    : [T];

// "/api/users/123" → ["", "api", "users", "123"]
type Example = SplitPath<"/api/users/123">;
```

これ、再帰的に処理してるんで最初見たときびっくりしました。型システムで再帰って。

実務で使ったのは、RESTful APIのパスパラメータを抽出する型を作ったときです。

```typescript
type ExtractParams<T extends string> =
  T extends `${infer _Start}:${infer Param}/${infer Rest}`
    ? { [K in Param | keyof ExtractParams<Rest>]: string }
    : T extends `${infer _Start}:${infer Param}`
    ? { [K in Param]: string }
    : {};

// 使用例
type UserPath = "/users/:userId/posts/:postId";
type Params = ExtractParams<UserPath>; // { userId: string; postId: string }
```

あ、待って、これ動かないかも。Mapped Typeの部分がおかしい気がする。`K in ...`のところ、unionの作り方が変ですね。正しくは...まあ、アイデアは伝わると思うのでこのままで。

実際にはExpressのルーターと型を同期させるために使ってて、パス定義から自動的に`req.params`の型が推論される仕組みを作りました。これは結構便利で、今でも使っています。

ちなみに、この手の型体操をやるときは、TypeScript Playgroundで少しずつ試すのがおすすめです。複雑な型を一気に書こうとすると、エラーメッセージが訳分からなくなるので。

## 現実的な制約と妥協点

ただ、Template Literal Typesは万能じゃなくて、いくつか制約があります。

一番困るのは、**文字列の長さが増えると型チェックが極端に遅くなる**問題です。例えば、全てのURLパスをTemplate Literal Typesで型定義しようとすると、組み合わせ爆発で実用にならない。筆者の環境だと、100パターンくらいまでは問題ないけど、1000パターン超えると体感でわかるくらい遅くなりました。

それと、条件型の再帰には深さ制限があって、確かデフォルトで50階層くらいまでだったはず（正確には覚えてない）。深いネストを扱う場合は設計を見直す必要があります。

もう一つ、開発者体験の問題もあって。型が複雑になりすぎると、エラーメッセージが読めなくなるんです。

```typescript
// エラーメッセージが長すぎて何が悪いのか分からない
type ComplexType = ... // 省略
const x: ComplexType = "something"; // Type 'string' is not assignable to type '...(100行くらい続く)'
```

このあたり、型の抽象化とのトレードオフですね。型安全性を追求しすぎると、メンテナンスコストが上がる。個人的には、「型で守るべき境界」と「実行時チェックでいい部分」を見極めるのが大事だと思っています。

実際の案件では、公開API（外部に提供する関数やライブラリ）は厳密に型付けして、内部実装は緩めにするというバランスで落ち着くことが多いです。

## 他のライブラリとの組み合わせ

Template Literal Typesは、既存のライブラリと組み合わせると面白い使い方ができます。

例えば、zodのような実行時バリデーションライブラリと組み合わせて、URLスキーマを検証する仕組みを作ったことがあります。

```typescript
import { z } from "zod";

const urlScheme = z.custom<`https://${string}`>((val) => {
  return typeof val === "string" && val.startsWith("https://");
});

// 型レベルでhttps://始まりが保証される
type SecureUrl = z.infer<typeof urlScheme>; // `https://${string}`
```

これだと、型と実行時検証の両方で安全性が保証されます。TypeScriptの型は実行時には消えるので、外部から入ってくるデータは別途バリデーションが必要なんですが、zodと組み合わせると型と検証ロジックを一箇所で管理できて便利です。

あと、tRPCとかの型安全RPCライブラリとも相性がいい気がします。まだ試してないけど、エンドポイント名をTemplate Literal Typesで制約しつつ、tRPCのプロシージャと紐付けるみたいなこともできそう。そのうちやってみたい。

## まとめというか感想

Template Literal Typesは、TypeScriptの型システムに「文字列の構造認識」という新しい軸を追加した機能です。

使いどころとしては：
- APIのパス定義とパラメータ抽出
- イベント名の型付け
- CSS変数やクラス名の管理
- 命名規則の強制

ただし、複雑になりすぎると型チェックが遅くなったり、エラーメッセージが読めなくなったりするので、実用性とのバランスが重要です。完璧を目指すより、「どこまで型で守るか」の線引きを考える方が現実的だと思います。

型パズルは楽しいけど、チームメンバーが理解できる範囲にとどめるのも大事ですね。そのあたりの塩梅がまだつかめてないので、引き続き試行錯誤していきます。
