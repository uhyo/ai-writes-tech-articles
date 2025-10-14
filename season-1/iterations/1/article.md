---
title: "TypeScriptの型推論とwidening/narrowingの実践的な理解"
emoji: "🔍"
type: "tech"
topics: ["typescript", "型推論", "型システム"]
published: true
---

皆さんこんにちは。TypeScriptを使っているとよく遭遇する「なぜこの型になるの?」という疑問。特に初心者の方だけでなく、経験豊富な開発者でも、型推論の挙動に戸惑うことがあるのではないでしょうか。

今回は、TypeScriptの型推論システムの中核をなす**widening**(型の拡大)と**narrowing**(型の絞り込み)について、実践的な視点から解説します。これらの概念を理解することで、「なぜこのコードはエラーになるのか」「どうすれば期待通りの型になるのか」が明確になります。

## TypeScriptの型推論とは

TypeScriptは、明示的に型を書かなくても、コードから適切な型を推論してくれます。例えば:

```ts
const message = "Hello";  // string型と推論される
let count = 42;           // number型と推論される
```

しかし、この型推論は単純なルールだけでは説明できない複雑さを持っています。特に重要なのが、**widening**と**narrowing**という2つの方向性を持つ型の変化です。

## Widening: 型が広がる仕組み

### Wideningとは何か

Wideningは、TypeScriptがリテラル型(具体的な値の型)を、より広い型に拡大することです。典型的な例を見てみましょう:

```ts
let status = "success";  // string型に推論される
const fixedStatus = "success";  // "success"型(リテラル型)に推論される
```

`let`で宣言した変数は再代入可能なので、TypeScriptは「この変数には"success"以外の文字列も代入される可能性がある」と判断し、`string`型に拡大します。一方、`const`で宣言した変数は再代入不可能なので、`"success"`というリテラル型のまま保持されます。

### Wideningが起こる場面

実際の開発では、以下のような場面でwideningに遭遇します:

```ts
// オブジェクトのプロパティ
const config = {
  method: "GET",  // string型に拡大される
  timeout: 3000   // number型に拡大される
};

// 型は { method: string; timeout: number } となる
```

この挙動は一見不便に見えますが、TypeScriptが「オブジェクトのプロパティは後で変更されるかもしれない」と想定しているためです。

### 実際の問題: APIの型定義との不一致

実務でよく遭遇する問題を見てみましょう:

```ts
type HttpMethod = "GET" | "POST" | "PUT" | "DELETE";

function fetchData(method: HttpMethod) {
  // APIリクエストを送る処理
}

const config = {
  method: "GET"
};

fetchData(config.method);  // ❌ エラー!
// Argument of type 'string' is not assignable to parameter of type 'HttpMethod'
```

`config.method`は`string`型に拡大されているため、`HttpMethod`型を要求する関数に渡せません。これがwideningによる典型的な問題です。

## Narrowing: 型を絞り込む

### Narrowingとは何か

Narrowingは、wideningとは逆に、広い型から狭い型へと絞り込むプロセスです。TypeScriptは、条件分岐やチェックを通じて、型をより具体的なものに絞り込んでくれます。

```ts
function process(value: string | number) {
  if (typeof value === "string") {
    // この中では value は string型に絞り込まれる
    console.log(value.toUpperCase());
  } else {
    // この中では value は number型に絞り込まれる
    console.log(value.toFixed(2));
  }
}
```

### 様々なNarrowing手法

TypeScriptは複数の方法で型を絞り込んでくれます:

#### 1. typeofによる絞り込み

```ts
function format(value: string | number) {
  if (typeof value === "string") {
    return value.trim();  // string型
  }
  return value.toFixed(2);  // number型
}
```

#### 2. instanceofによる絞り込み

```ts
function handleError(error: Error | string) {
  if (error instanceof Error) {
    console.log(error.stack);  // Error型
  } else {
    console.log(error);  // string型
  }
}
```

#### 3. in演算子による絞り込み

```ts
type Dog = { bark: () => void };
type Cat = { meow: () => void };

function makeSound(animal: Dog | Cat) {
  if ("bark" in animal) {
    animal.bark();  // Dog型
  } else {
    animal.meow();  // Cat型
  }
}
```

#### 4. 等価チェックによる絞り込み

```ts
function handleStatus(status: "success" | "error" | "pending") {
  if (status === "success") {
    // status は "success" 型に絞り込まれる
  }
}
```

### Null/undefinedの絞り込み

実務では、null/undefinedチェックによるnarrowingが非常に重要です:

```ts
function getUserName(user: { name: string } | null) {
  if (user !== null) {
    // user は { name: string } 型に絞り込まれる
    console.log(user.name);
  }
}
```

TypeScript 4.8以降では、`{} | null | undefined`のような型から`null`や`undefined`を除外すると、`{}`型(非nullableなオブジェクト型)に絞り込まれるようになりました[^pr49119]。

[^pr119]: https://github.com/microsoft/TypeScript/pull/49119

## Wideningを制御する: as constの活用

### as constとは

`as const`は、TypeScript 3.4で導入された機能で、wideningを防ぐための強力なツールです:

```ts
const config = {
  method: "GET",
  timeout: 3000
} as const;

// 型は { readonly method: "GET"; readonly timeout: 3000 } となる
```

`as const`を使うと:
1. リテラル型がwideningされない
2. すべてのプロパティが`readonly`になる
3. 配列はreadonlyなタプル型になる

### 実践的な使用例

先ほどのAPI呼び出しの問題を解決できます:

```ts
type HttpMethod = "GET" | "POST" | "PUT" | "DELETE";

function fetchData(method: HttpMethod) {
  // APIリクエストを送る処理
}

const config = {
  method: "GET"
} as const;

fetchData(config.method);  // ✅ 正常に動作!
```

### 配列での使用

配列に`as const`を使うと、タプル型として扱われます:

```ts
const colors = ["red", "green", "blue"] as const;
// 型は readonly ["red", "green", "blue"]

type Color = typeof colors[number];  // "red" | "green" | "blue"
```

これは、定数の配列からユニオン型を生成する際に非常に便利です。

## 実践的なパターンと注意点

### パターン1: オブジェクトの部分的なas const

オブジェクト全体ではなく、特定のプロパティだけリテラル型にしたい場合もあります:

```ts
const config = {
  method: "GET" as const,  // "GET"型
  url: "/api/users",       // string型
  timeout: 3000            // number型
};
```

### パターン2: 型アサーションとの使い分け

`as const`と通常の型アサーションは異なります:

```ts
const method1 = "GET" as HttpMethod;  // HttpMethod型(Union型)
const method2 = "GET" as const;       // "GET"型(リテラル型)
```

`as const`の方がより厳密な型を保持できます。

### 注意点: 過度なreadonlyによる制約

`as const`を使いすぎると、本当に書き換えたいデータまでreadonlyになってしまいます:

```ts
const state = {
  count: 0,
  isLoading: false
} as const;

state.count = 1;  // ❌ エラー! readonlyなので変更できない
```

必要な箇所にだけ`as const`を適用することが重要です。

:::details 余談: 型推論の歴史的背景
TypeScriptの型推論アルゴリズムは、バージョンを重ねるごとに進化してきました。初期のバージョンでは、現在のような洗練されたnarrowingは実装されていませんでした。

特に、`as const`が導入されたTypeScript 3.4は、型推論の柔軟性を大きく向上させた重要なリリースでした。それ以前は、リテラル型を保持するために複雑な型定義を書く必要がありました。
:::

## 型ガードを活用した高度なNarrowing

### カスタム型ガード関数

独自の型ガードを定義することで、より複雑な型の絞り込みが可能になります:

```ts
interface User {
  id: number;
  name: string;
}

function isUser(value: unknown): value is User {
  return (
    typeof value === "object" &&
    value !== null &&
    "id" in value &&
    "name" in value
  );
}

function processData(data: unknown) {
  if (isUser(data)) {
    // data は User型に絞り込まれる
    console.log(data.name);
  }
}
```

`value is User`という特殊な戻り値の型を使うことで、TypeScriptに「この関数がtrueを返したら、引数は`User`型である」と伝えることができます。

### assertsキーワードによるアサーション関数

TypeScript 3.7で導入された`asserts`キーワードを使うと、条件を満たさない場合にエラーを投げる関数を定義できます:

```ts
function assertIsString(value: unknown): asserts value is string {
  if (typeof value !== "string") {
    throw new Error("Not a string");
  }
}

function process(input: unknown) {
  assertIsString(input);
  // この行以降、input は string型として扱われる
  console.log(input.toUpperCase());
}
```

この手法は、実行時のバリデーションと型チェックを組み合わせる際に有効です。

## まとめ

TypeScriptの型推論、特にwideningとnarrowingを理解することで、型エラーの原因が明確になり、適切な解決策を選べるようになります。

重要なポイントをまとめると:

- **Widening**は、`let`や変更可能なプロパティで、リテラル型が広い型に拡大される現象
- **Narrowing**は、条件分岐やチェックによって、広い型を具体的な型に絞り込むプロセス
- `as const`を使うことで、wideningを防ぎ、リテラル型を保持できる
- 型ガードを活用することで、複雑な型の絞り込みが可能になる

これらの概念は、TypeScriptを使った実装で日常的に遭遇します。最初は複雑に感じるかもしれませんが、一度理解してしまえば、TypeScriptの型システムをより深く活用できるようになるはずです。

実際のコードで遭遇したときは、「この型はwideningされているのか?」「どうやってnarrowingできるか?」と考えてみてください。きっと適切な解決策が見つかると思います。
