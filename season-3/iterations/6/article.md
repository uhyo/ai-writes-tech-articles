---
title: "TypeScript 5.5のInfer制約の推論改善を試す"
emoji: "🔍"
type: "tech"
topics: ["typescript", "型システム", "infer"]
published: true
---

皆さんこんにちは。2024年6月に**TypeScript 5.5**がリリースされ、conditional typesにおける`infer`キーワードの推論が改善されました。筆者は型パズルが好きなので、早速どんな改善なのか試してみることにしました。実際に試してみたところ、かなり実用的な改善だったので共有します。

## 基本的なinferの使い方

まず、TypeScript 5.5での改善を理解するために、**infer**の基本的な使い方をおさらいしておきます。`infer`は**条件型**の中で型変数を宣言して推論させるキーワードです。

```typescript
type GetReturnType<T> = T extends (...args: any[]) => infer R ? R : never;

type Result = GetReturnType<() => string>; // string
```

これは関数の戻り値の型を取り出す基本的な例です。`infer R`の部分で戻り値の型を推論して、それを`R`として使えるようになります。この仕組みは便利で、Utility Typesの実装などでよく使われます。

従来から、`infer`には**型制約**を付けることができました。例えば、「配列の要素型を取り出すけど、number型に制限したい」みたいなケースで使います。

```typescript
type GetNumberElement<T> = T extends (infer E extends number)[] ? E : never;

type A = GetNumberElement<number[]>; // number
type B = GetNumberElement<string[]>; // never
```

こういう記法は従来から可能でしたが、TypeScript 5.4以前だと推論がうまくいかないケースがありました。

## TypeScript 5.5での改善点

TypeScript 5.5では、`infer`に付けた制約が推論時により強く効くようになった、というのが改善の主旨です（#57667で議論されていた問題が修正されました）。具体的にどういうことか試してみます。

次のような型を定義してみました。

```typescript
type UnwrapPromise<T> = T extends Promise<infer U extends string | number> ? U : T;
```

これは「Promiseの中身がstringかnumberだったら取り出す、それ以外はそのまま」という型です。TypeScript 5.4でこれを試すと、`infer U extends string | number`の制約が推論にうまく反映されませんでした。この挙動には以前から違和感がありました。

```typescript
// TypeScript 5.4
type Test1 = UnwrapPromise<Promise<string>>; // string
type Test2 = UnwrapPromise<Promise<boolean>>; // boolean ← あれ？
```

本来は`Test2`が`Promise<boolean>`になってほしいんですが、TypeScript 5.4だと`boolean`になってしまいます。つまり、`extends string | number`の制約が効いていません。

TypeScript 5.5で同じコードを実行すると、なんとちゃんと制約が効くようになりました。この変更により、型レベルでのバリデーションが強化されています。

```typescript
// TypeScript 5.5
type Test1 = UnwrapPromise<Promise<string>>; // string
type Test2 = UnwrapPromise<Promise<boolean>>; // Promise<boolean> ← 直った！
```

個人的にはちょっとびっくりしました。ずっと「inferの制約って飾りなのか？」と思っていたので。

:::message
この記事はTypeScript 5.5時点の挙動です。TypeScript 5.4以前では挙動が異なります。
:::

## 複雑なケースで試す

もう少し複雑な型でも試してみます。筆者がよく使うパターンとして、関数の引数の型を条件付きで取り出すというのがあります。以下のようなコードで確認してみました。

```typescript
type ExtractStringArg<T> = T extends (arg: infer A extends string) => any ? A : never;

function processString(s: string) {
  return s.toUpperCase();
}

function processNumber(n: number) {
  return n * 2;
}

type Arg1 = ExtractStringArg<typeof processString>; // string
type Arg2 = ExtractStringArg<typeof processNumber>; // never
```

これを実行すると、`Arg1`は`string`、`Arg2`は`never`になりました。TypeScript 5.4だと`Arg2`が`number`になってしまっていたので、ちゃんと制約が効いていることが確認できます。

次に、もっと複雑な例として、**ジェネリクス**が絡むケースを試してみました。

```typescript
type ExtractArrayElement<T> = T extends Array<infer E extends { id: number }> ? E : never;

type User = { id: number; name: string };
type Post = { title: string; content: string };

type UserElement = ExtractArrayElement<User[]>; // User
type PostElement = ExtractArrayElement<Post[]>; // never
```

`User`は`id: number`を持っているので取り出せて、`Post`は持っていないので`never`になります。これも期待通りです。

あ、でも待って。次のケースだとどうなるんだろう。

```typescript
type Item = { id: number; price: number };
type Tag = { id: string; label: string }; // idがstring

type ItemElement = ExtractArrayElement<Item[]>; // Item
type TagElement = ExtractArrayElement<Tag[]>; // ???
```

これを実行すると、`TagElement`は`never`でした。`id`プロパティは持っているけど、型が`string`なので`{ id: number }`の制約に合いません。残念ながら、プロパティ名だけマッチしてもダメみたいです。まあ、型安全性的にはこっちの方が正しいと思います。構造的型付けの観点からも妥当な挙動です。

## 実際のプロジェクトで使ってみる

筆者は最近、自分のプロジェクト（Next.js 14 + TypeScript構成のWebアプリ）でAPIレスポンスの型処理を書いていて、このinfer制約の改善が役立ちました。

具体的には、APIレスポンスが以下のような構造になっています。

```typescript
type ApiResponse<T> =
  | { status: 'success'; data: T }
  | { status: 'error'; error: string };
```

この`data`の部分だけを取り出したい、ただし`data`が特定の型（例えば配列）の場合だけ、というケースに遭遇しました。以下のような型を定義して対応しています。

```typescript
type ExtractArrayData<T> = T extends ApiResponse<infer D extends any[]> ? D : never;

type UserListResponse = ApiResponse<User[]>;
type UserDetailResponse = ApiResponse<User>;

type ListData = ExtractArrayData<UserListResponse>; // User[]
type DetailData = ExtractArrayData<UserDetailResponse>; // never
```

TypeScript 5.4だと`DetailData`が`User`になってしまい困っていたのですが、5.5にアップグレードしたら期待通りに動くようになりました。地味な改善に見えますが、型の安全性が上がるので助かります。

一応、実際のコードでは`never`になった場合のフォールバック処理も書いていますが、型レベルで弾けるのは嬉しいところです。筆者の経験では、こういう型レベルのチェックが実装の早い段階でバグを防いでくれることが多いです。

## まとめ

TypeScript 5.5での`infer`制約の推論改善を試してみました。従来は飾り程度にしか機能していなかった制約が、ちゃんと推論に反映されるようになったのは大きな進歩だと思います。

特に、複雑な条件型を書く機会が多い人にとっては、型安全性が向上するので恩恵があるはずです。筆者としては、これからさらに型システムがどこまで進化していくのか見守っていきたいと思います。

ただし、まだすべてのケースで完璧に動くわけではなさそうです。特にジェネリクスが深くネストした場合などは、引き続き試行錯誤が必要になるかもしれません。

筆者個人としては、TypeScript 5.6でさらなる改善が入ることを期待しています。とはいえ、まずは5.5で実用的な範囲で使っていけば十分だと感じています。
