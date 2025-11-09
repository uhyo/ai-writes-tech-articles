---
title: "TypeScript の satisfies 演算子の実践的な使い方"
emoji: "✨"
type: "tech"
topics: ["typescript", "javascript"]
published: true
---

TypeScript 4.9で追加された `satisfies` 演算子、みなさん使ってますか？

正直なところ、筆者も最初は「また新しい構文か...」くらいにしか思ってなかったんですが、実際に使ってみると地味に便利で、今ではコードレビューで「ここ satisfies 使えそう」とか言ってる側になってしまいました。

## satisfies が解決する問題

まず、satisfies が何を解決するのかという話から。

TypeScript で型をつける時、こんなジレンマに遭遇することがあります：

```typescript
type Color = "red" | "green" | "blue";

interface Theme {
  primary: Color;
  secondary: Color;
}

// パターン1: 型注釈をつける
const theme1: Theme = {
  primary: "red",
  secondary: "blue"
};

// パターン2: 型注釈なし
const theme2 = {
  primary: "red",
  secondary: "blue"
};
```

パターン1は型安全ですが、`theme1.primary` の型は `Color` 型（`"red" | "green" | "blue"`）に広がってしまいます。つまり、`"red"` というリテラル型の情報が失われる。

パターン2は `theme2.primary` が `"red"` 型として推論されますが、スペルミスとか余計なプロパティを書いても気づけません。

で、この両方のいいとこ取りをしたいわけです。

```typescript
const theme = {
  primary: "red",
  secondary: "blue"
} satisfies Theme;
```

これで `theme.primary` は `"red"` 型として推論されつつ、Theme型の制約も満たしていることが保証されます。型チェックは通すけど、推論される型は変えない。

## 実際に遭遇したユースケース

去年くらいに社内のデザインシステムのコンポーネントライブラリを作ってる時に、まさにこの問題にぶつかりました。

カラーパレットを定義する必要があったんですが、こんな感じのコードを書いてたんです：

```typescript
type ColorValue = `#${string}`;

interface ColorPalette {
  [key: string]: ColorValue;
}

const palette: ColorPalette = {
  primary: "#3b82f6",
  secondary: "#8b5cf6",
  danger: "#ef4444"
};

// 使う側
const Button = ({ color }: { color: keyof typeof palette }) => {
  // ...
};
```

この実装、一見問題なさそうですが、`keyof typeof palette` が `string` になってしまうという罠がありました。`ColorPalette` のインデックスシグネチャが `[key: string]` だからですね。

つまり、`<Button color="primary" />` はもちろん、`<Button color="typo" />` もTypeScriptが通してしまう。

```typescript
const palette = {
  primary: "#3b82f6",
  secondary: "#8b5cf6",
  danger: "#ef4444"
} satisfies ColorPalette;

// これで keyof typeof palette が "primary" | "secondary" | "danger" になる
```

satisfies を使うと、`ColorPalette` の制約は満たしつつ、正確なキーの型が保持されます。これで `<Button color="typo" />` はちゃんとエラーになる。

あ、ちなみに `satisfies Record<string, ColorValue>` でも同じことができますが、将来的に ColorPalette に追加の制約を入れたい時のことを考えると、専用の型を作っておいた方が拡張しやすいです[^1]。

[^1]: 例えば、後から「primaryは必須」みたいな制約を追加したくなることがあります。`interface ColorPalette { primary: ColorValue; [key: string]: ColorValue }` みたいな感じで。

## 設定オブジェクトのパターン

もう一つよく使うのが、設定オブジェクトの型チェックです。

```typescript
type HttpMethod = "GET" | "POST" | "PUT" | "DELETE";

interface RouteConfig {
  path: string;
  method: HttpMethod;
  handler: Function;
}

const routes = {
  getUser: {
    path: "/users/:id",
    method: "GET",
    handler: getUserHandler
  },
  createUser: {
    path: "/users",
    method: "POST",
    handler: createUserHandler
  }
} satisfies Record<string, RouteConfig>;
```

このパターン、何がいいかというと：

1. 各ルートが `RouteConfig` の形式に従っていることが保証される
2. `routes.getUser.method` は `"GET"` 型として推論される（`HttpMethod` 型ではない）
3. `routes` のキーは `"getUser" | "createUser"` として推論される

これがないと、例えば `method: "PATCH"` とか書いてもエラーにならなかったり、逆に型注釈をつけると `method` が `HttpMethod` 型に広がってしまって、後続の処理で不便だったりします。

実際、Express とか Fastify みたいなルーティングライブラリを使う時に、このパターンは割と使えます。ルート定義を一箇所にまとめつつ、型安全性も保てる。

## const assertion との使い分け

「じゃあ `as const` でいいんじゃないの？」という疑問、筆者も最初に持ちました。

```typescript
const theme = {
  primary: "red",
  secondary: "blue"
} as const;
```

確かに `as const` でもリテラル型は保持されますが、問題は**型チェックが走らない**こと。

```typescript
const theme = {
  primary: "red",
  secondary: "yellow", // yellow は Color 型にないけどエラーにならない
  extra: "something"    // 余計なプロパティもエラーにならない
} as const;
```

つまり、`as const` は「この値を readonly かつリテラル型として扱え」という指示であって、特定の型に適合しているかのチェックはしてくれません。

一方、satisfies は型チェックしてくれます：

```typescript
const theme = {
  primary: "red",
  secondary: "yellow", // エラー: Type '"yellow"' is not assignable to type 'Color'
  extra: "something"    // エラー: Object literal may only specify known properties
} satisfies Theme;
```

で、両方使いたい場合は組み合わせられます：

```typescript
const theme = {
  primary: "red",
  secondary: "blue"
} as const satisfies Theme;
```

これで型チェックも効くし、リテラル型も保持されるし、readonly にもなる。完璧ですね。

...というか、正確には `satisfies Theme` の時点でリテラル型は保持されるので、`as const` が必要なのは readonly にしたい場合だけです。この辺、最初混乱しました。

## API レスポンスの型チェック

最近気づいたんですが、API のモックデータを作る時にも使えます。

```typescript
interface User {
  id: number;
  name: string;
  email: string;
  role: "admin" | "user";
}

const mockUsers = [
  {
    id: 1,
    name: "Alice",
    email: "alice@example.com",
    role: "admin"
  },
  {
    id: 2,
    name: "Bob",
    email: "bob@example.com",
    role: "user"
  }
] satisfies User[];
```

これの何がいいかというと、モックデータが実際の `User` 型に従っているかチェックできつつ、`mockUsers[0].role` は `"admin"` 型として推論されるので、テストコードで使いやすい。

```typescript
// mockUsers[0].role は "admin" 型なので、こういうチェックが型安全に書ける
if (mockUsers[0].role === "admin") {
  // ...
}
```

型注釈 `const mockUsers: User[] = [...]` だと、`mockUsers[0].role` が `"admin" | "user"` 型になってしまって、型ガードなしでは "admin" かどうか判定できません。

実務では使ったことないですが、Storybook のストーリー定義とかでも同じパターンが使えそうな気がします。

## まとめ

satisfies 演算子、地味ですが「型チェックしたいけど型を広げたくない」というシーンでピンポイントに刺さります。

筆者の感覚だと、以下のケースで特に有用です：

- オブジェクトのキーを型として取り出したい時（`keyof typeof obj`）
- リテラル型を保持しながら型チェックしたい時
- 設定オブジェクトやルーティングテーブルみたいな、構造化されたデータを定義する時

逆に、単純に「この変数はこの型」と宣言したいだけなら、普通の型注釈で十分です。satisfies は型推論の恩恵を受けつつ、型チェックも欲しいという、ちょっと欲張りなケースで真価を発揮します。

TypeScript 4.9 以降なら使えるので、まだ試してない方はぜひ。コードレビューで「ここ satisfies では？」と指摘できるようになると、ちょっと上級者っぽく見えます（主観）。
