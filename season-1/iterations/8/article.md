---
title: "Template Literal Typesでルーティングを型安全にしたら、バリデーションまで消えた話"
emoji: "🛣️"
type: "tech"
topics: ["typescript", "型システム", "validation"]
published: true
---

## きっかけ：文字列リテラルにできることが増えすぎた

TypeScript 4.1でTemplate Literal Typesが入って以来、型システムでできることが爆発的に増えました。最初は「文字列の型でテンプレート使えるのね、便利だね」くらいに思ってたんですが、実際に触ってると全然違う。これ、型レベルの文字列操作言語です。

去年、社内のAPIゲートウェイ（50個くらいエンドポイント）をTypeScript化するプロジェクトで、パスパラメータの扱いに詰まりました。`/users/:userId/posts/:postId`みたいなルートから型を生成したかったんですが、最初は「正規表現でパース？　いやでも型で...」って迷走して、結局3日溶けた。で、Template Literal Typesを真面目に調べ始めたら、これ想像以上に強力だった。

この記事では、Template Literal Typesを使ってルーティングを型安全にする過程を追います。単にパラメータを抽出するだけじゃなく、バリデーションロジックまで型で表現できるようになります。

## パスパラメータの抽出：まず基本から

Template Literal Typesの基本は、文字列リテラル型を分解して情報を取り出すことです。

```typescript
type ExtractParam<Path extends string> =
  Path extends `${infer _Start}:${infer Param}/${infer _Rest}`
    ? Param
    : Path extends `${infer _Start}:${infer Param}`
    ? Param
    : never;

type UserId = ExtractParam<"/users/:userId">; // "userId"
```

これで`:userId`の部分が取れる。でも実際のルートは複数のパラメータを持つから、これだけじゃ足りません。全パラメータを抽出したい。

```typescript
type ExtractParams<Path extends string> =
  Path extends `${infer _Start}:${infer Param}/${infer Rest}`
    ? Param | ExtractParams<`/${Rest}`>
    : Path extends `${infer _Start}:${infer Param}`
    ? Param
    : never;

type Params = ExtractParams<"/users/:userId/posts/:postId">;
// "userId" | "postId"
```

Union型で返すんじゃなくて、オブジェクトの型にしたい。となると、もう少し工夫が必要です。

```typescript
type ParseRoute<Path extends string> =
  Path extends `${infer _Start}:${infer Param}/${infer Rest}`
    ? { [K in Param]: string } & ParseRoute<`/${Rest}`>
    : Path extends `${infer _Start}:${infer Param}`
    ? { [K in Param]: string }
    : {};

type RouteParams = ParseRoute<"/users/:userId/posts/:postId">;
// { userId: string; postId: string }
```

これで型はできた。実際にパスから値を取り出す関数と組み合わせます。

```typescript
function parseRoute<T extends string>(
  route: T,
  path: string
): ParseRoute<T> | null {
  // 実装は省略（正規表現でパース）
  // ...
}

const params = parseRoute("/users/:userId/posts/:postId", "/users/123/posts/456");
// params: { userId: string; postId: string } | null
```

で、ここまでは割とよくある実装です。問題はここからで、型が`string`なのが気に食わない。

## 型の制約：stringじゃなくてnumberがほしい

実際のコードでは、`userId`は数値IDだったりします。でも今の実装だと全部`string`。実行時にパースして`number`に変換するコードを書くわけですが、これが型安全じゃない。

```typescript
const params = parseRoute("/users/:userId", "/users/123");
if (!params) return;

const userId = parseInt(params.userId, 10); // 手動でparse
if (isNaN(userId)) {
  // エラーハンドリング
}
```

これ、バリデーションロジックが分散しちゃう。ルート定義の時点で「userIdは数値」って指定したい。

Template Literal Typesは文字列しか扱えないから、型情報を文字列にエンコードする必要があります。例えば、`:userId(number)`みたいな記法。

```typescript
type ParseRoute<Path extends string> =
  Path extends `${infer _Start}:${infer Param}(${infer Type})/${infer Rest}`
    ? { [K in Param]: ParseType<Type> } & ParseRoute<`/${Rest}`>
    : Path extends `${infer _Start}:${infer Param}(${infer Type})`
    ? { [K in Param]: ParseType<Type> }
    : Path extends `${infer _Start}:${infer Param}/${infer Rest}`
    ? { [K in Param]: string } & ParseRoute<`/${Rest}`>
    : Path extends `${infer _Start}:${infer Param}`
    ? { [K in Param]: string }
    : {};

type ParseType<T extends string> =
  T extends "number" ? number :
  T extends "boolean" ? boolean :
  string;

type RouteParams = ParseRoute<"/users/:userId(number)/posts/:postId(number)">;
// { userId: number; postId: number }
```

型はこれで解決。実行時の実装も合わせます。

```typescript
function parseRoute<T extends string>(
  route: T,
  path: string
): ParseRoute<T> | null {
  // ルート定義から正規表現を生成
  const pattern = route.replace(/:(\w+)(\([^)]+\))?/g, (_, name, type) => {
    return `(?<${name}>[^/]+)`;
  });

  const match = path.match(new RegExp(`^${pattern}$`));
  if (!match?.groups) return null;

  // 型情報に基づいて値を変換
  const result: any = {};
  const typePattern = /:(\w+)\((\w+)\)/g;
  let typeMatch;

  while ((typeMatch = typePattern.exec(route)) !== null) {
    const [, name, type] = typeMatch;
    const value = match.groups[name];

    if (type === "number") {
      const num = Number(value);
      if (isNaN(num)) return null;
      result[name] = num;
    } else if (type === "boolean") {
      result[name] = value === "true";
    } else {
      result[name] = value;
    }
  }

  // 型指定のないパラメータも追加
  for (const [key, value] of Object.entries(match.groups)) {
    if (!(key in result)) {
      result[key] = value;
    }
  }

  return result;
}

const params = parseRoute("/users/:userId(number)", "/users/123");
// params: { userId: number } | null

if (params) {
  params.userId; // number型として扱える
}
```

これで型変換とバリデーションが一体化しました。`parseRoute`が`null`を返した時点で、パラメータが不正だと分かる。

（ちなみにこのプロジェクト、最初はzodでバリデーションスキーマを別途定義する予定だったんですが、「ルート定義とスキーマを二重管理したくない」って議論になって、結局この方式に落ち着いた。実装コストは上がったけど、DRYになって満足してます。）

## クエリパラメータ：さらに複雑な制約

パスパラメータだけじゃ足りなくて、クエリパラメータも型安全にしたい。`/search?q=typescript&page=1`みたいなやつ。

クエリパラメータの厄介なところは、オプショナルな場合があることです。`page`は省略可能だけど、`q`は必須、みたいな。

ルート定義にクエリパラメータも含める記法を考えます。`/search?q(string)&page(number)?`みたいに、`?`でオプショナルを表現。

```typescript
type ParseQuery<Query extends string> =
  Query extends `${infer Param}(${infer Type})?&${infer Rest}`
    ? { [K in Param]?: ParseType<Type> } & ParseQuery<Rest>
    : Query extends `${infer Param}(${infer Type})&${infer Rest}`
    ? { [K in Param]: ParseType<Type> } & ParseQuery<Rest>
    : Query extends `${infer Param}(${infer Type})?`
    ? { [K in Param]?: ParseType<Type> }
    : Query extends `${infer Param}(${infer Type})`
    ? { [K in Param]: ParseType<Type> }
    : {};

type QueryParams = ParseQuery<"q(string)&page(number)?">;
// { q: string; page?: number }
```

パスとクエリを統合した型を定義。

```typescript
type ParseRouteWithQuery<Path extends string> =
  Path extends `${infer Route}?${infer Query}`
    ? { params: ParseRoute<Route>; query: ParseQuery<Query> }
    : { params: ParseRoute<Path>; query: {} };

type FullRoute = ParseRouteWithQuery<"/users/:userId(number)/posts?page(number)?">;
// {
//   params: { userId: number };
//   query: { page?: number }
// }
```

実装も合わせて拡張します。

```typescript
function parseRouteWithQuery<T extends string>(
  route: T,
  path: string,
  queryString: string = ""
): ParseRouteWithQuery<T> | null {
  const [routePart, queryPart] = route.split("?");

  const params = parseRoute(routePart, path);
  if (!params) return null;

  const query: any = {};
  if (queryPart) {
    const queryParams = new URLSearchParams(queryString);
    const queryPattern = /(\w+)\((\w+)\)(\?)?/g;
    let match;

    while ((match = queryPattern.exec(queryPart)) !== null) {
      const [, name, type, optional] = match;
      const value = queryParams.get(name);

      if (value === null) {
        if (!optional) return null; // 必須パラメータが欠けている
        continue;
      }

      if (type === "number") {
        const num = Number(value);
        if (isNaN(num)) return null;
        query[name] = num;
      } else if (type === "boolean") {
        query[name] = value === "true";
      } else {
        query[name] = value;
      }
    }
  }

  return { params, query } as any;
}

const result = parseRouteWithQuery(
  "/users/:userId(number)/posts?page(number)?",
  "/users/123/posts",
  "page=2"
);
// result: { params: { userId: number }; query: { page?: number } } | null

if (result) {
  result.params.userId; // number
  result.query.page;    // number | undefined
}
```

これで、クエリパラメータの型とオプショナル性も表現できました。

## より複雑な制約：カスタムバリデーション

数値や真偽値だけじゃなくて、もっと複雑な制約を表現したい場合もあります。例えば、`page`は1以上の整数、とか、`status`は`"active"`か`"inactive"`のリテラル、とか。

型情報をさらに拡張して、カスタムバリデータを指定できるようにします。

```typescript
type Validator<T> = (value: string) => T | null;

const validators = {
  number: (v: string) => {
    const n = Number(v);
    return isNaN(n) ? null : n;
  },
  positiveInt: (v: string) => {
    const n = Number(v);
    return Number.isInteger(n) && n > 0 ? n : null;
  },
  status: (v: string) => {
    return v === "active" || v === "inactive" ? v : null;
  },
} as const;

type ValidatorName = keyof typeof validators;

type InferValidatorType<V extends ValidatorName> =
  ReturnType<typeof validators[V]> extends infer T | null ? T : never;
```

`InferValidatorType`で、バリデータ関数の返り値型から`null`を除いた型を取得します。これを`ParseType`に統合。

```typescript
type ParseType<T extends string> =
  T extends ValidatorName ? InferValidatorType<T> : string;

type QueryParams = ParseQuery<"page(positiveInt)&status(status)">;
// { page: number; status: "active" | "inactive" }
```

実装側でバリデータを使います。

```typescript
function parseRouteWithQuery<T extends string>(
  route: T,
  path: string,
  queryString: string = ""
): ParseRouteWithQuery<T> | null {
  // ... パス部分の処理は同じ

  const query: any = {};
  if (queryPart) {
    const queryParams = new URLSearchParams(queryString);
    const queryPattern = /(\w+)\((\w+)\)(\?)?/g;
    let match;

    while ((match = queryPattern.exec(queryPart)) !== null) {
      const [, name, validatorName, optional] = match;
      const value = queryParams.get(name);

      if (value === null) {
        if (!optional) return null;
        continue;
      }

      const validator = validators[validatorName as ValidatorName];
      if (!validator) return null; // 未知のバリデータ

      const validated = validator(value);
      if (validated === null) return null; // バリデーション失敗

      query[name] = validated;
    }
  }

  return { params, query } as any;
}
```

これで、任意のバリデーションロジックを型と統合できました。バリデータを追加すれば、型も自動的に拡張される。

で、実際に使ってみると気づくんですが、バリデーションエラーの情報が欲しくなる。`null`が返ってくるだけだと、どのパラメータがどう不正だったのか分からない。

## エラー情報の追加：Result型で包む

バリデーション結果を`Result`型で表現します。

```typescript
type Result<T, E> =
  | { success: true; value: T }
  | { success: false; error: E };

type ValidationError = {
  param: string;
  type: "missing" | "invalid";
  expected?: string;
};
```

`parseRouteWithQuery`を修正して、`Result`を返すようにします。

```typescript
function parseRouteWithQuery<T extends string>(
  route: T,
  path: string,
  queryString: string = ""
): Result<ParseRouteWithQuery<T>, ValidationError> {
  const [routePart, queryPart] = route.split("?");

  // パスパラメータのパース
  const params = parseRoute(routePart, path);
  if (!params) {
    return {
      success: false,
      error: { param: "path", type: "invalid" }
    };
  }

  const query: any = {};
  if (queryPart) {
    const queryParams = new URLSearchParams(queryString);
    const queryPattern = /(\w+)\((\w+)\)(\?)?/g;
    let match;

    while ((match = queryPattern.exec(queryPart)) !== null) {
      const [, name, validatorName, optional] = match;
      const value = queryParams.get(name);

      if (value === null) {
        if (!optional) {
          return {
            success: false,
            error: {
              param: name,
              type: "missing",
              expected: validatorName
            }
          };
        }
        continue;
      }

      const validator = validators[validatorName as ValidatorName];
      if (!validator) {
        return {
          success: false,
          error: { param: name, type: "invalid" }
        };
      }

      const validated = validator(value);
      if (validated === null) {
        return {
          success: false,
          error: {
            param: name,
            type: "invalid",
            expected: validatorName
          }
        };
      }

      query[name] = validated;
    }
  }

  return {
    success: true,
    value: { params, query } as any
  };
}

const result = parseRouteWithQuery(
  "/users/:userId(number)/posts?page(positiveInt)",
  "/users/123/posts",
  "page=abc"
);

if (result.success) {
  result.value.params.userId; // number
} else {
  console.error(result.error);
  // { param: "page", type: "invalid", expected: "positiveInt" }
}
```

これで、エラー内容を詳細に取得できます。

## 実際の使用感：Express with型安全ルーティング

これまでの実装を統合して、Expressで使ってみます。

```typescript
import express from "express";

const app = express();

function defineRoute<T extends string>(
  route: T,
  handler: (req: ParseRouteWithQuery<T>) => void
) {
  const [pathPart] = route.split("?");
  const expressPath = pathPart.replace(/:(\w+)\([^)]+\)/g, ":$1");

  app.get(expressPath, (req, res) => {
    const result = parseRouteWithQuery(
      route,
      req.path,
      req.url.split("?")[1]
    );

    if (!result.success) {
      res.status(400).json({ error: result.error });
      return;
    }

    handler(result.value);
  });
}

defineRoute(
  "/users/:userId(number)/posts?page(positiveInt)?",
  ({ params, query }) => {
    params.userId; // number型
    query.page;    // number | undefined型

    // 処理
  }
);
```

ハンドラ内で、パラメータの型が完全に推論されます。手動でパースやバリデーションを書く必要がない。

実装見ると分かるけど、`defineRoute`がルート定義と型情報を一箇所に集約してる。ルート文字列から型が決まって、その型でハンドラが制約される。ルート定義を変えれば型も自動的に変わるから、型とロジックがズレることがない。

正直、最初は「こんなに型情報をエンコードして、メンテナンスできるのか？」って不安だったんですが、実際には逆でした。ルート定義を見れば全部分かるから、むしろスキーマとロジックが別々にある状態より把握しやすい。バリデータも関数として独立してるから、テストも書きやすい。

## 結局どこまでやるべきか

Template Literal Typesは強力ですが、やりすぎるとコンパイル時間が爆発します。この実装も、ルート数が100超えるとtscが重くなりました。

個人的には、以下のバランスがいいと感じてます。

- **パスパラメータ**: 型で抽出する（必須）
- **基本的な型変換**: `number`, `boolean`程度なら型で表現（推奨）
- **複雑なバリデーション**: 実行時のみ、型は緩くする（場合による）

結局、型システムでできることと、実行時にやるべきことの境界をどこに引くかは、プロジェクトごとに違います。でも、Template Literal Typesがあれば、その境界を大きく型システム側に寄せられる。

ちな、TypeScript 5.0以降では、Template Literal Typesの推論パフォーマンスが改善されてます（https://github.com/microsoft/TypeScript/pull/51094）。Andersさんのコメント見ると、内部的にキャッシュ機構が入ってるらしい。この辺の最適化が進めば、もっと複雑な型定義も実用的になるかもしれません。
