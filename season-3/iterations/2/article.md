---
title: "TypeScript 5.4のNoInfer型を試して使いどころを探る"
emoji: "🔍"
type: "tech"
topics: ["typescript", "型システム"]
published: true
---

皆さんこんにちは。TypeScript 5.4がリリースされ、**NoInfer型**という新しいユーティリティ型が追加されました。公式ドキュメントを見ると「型推論を抑制する」と書かれているのですが、正直なところ最初は「どういう場面で使うんだ？」という感じでした。そこで、実際に色々試して使いどころを探ってみることにしました。

## NoInferの基本的な動作

まず簡単な例から見ていきます。NoInferがない場合、こんなコードを書くとどうなるでしょうか。

```typescript
function createConfig<T>(defaults: T, overrides: T): T {
  return { ...defaults, ...overrides };
}

const config = createConfig(
  { port: 3000, host: "localhost" },
  { port: 8080 }
);
```

これを実行すると、TypeScriptは`overrides`の型を推論してしまって、`{ port: number }`という型になります。そして`defaults`の`{ port: number, host: string }`と合成しようとして型エラーになる。筆者は昔、この挙動で何度か詰まったことがあって、「なんで勝手に推論するんだよ」と思っていました。

NoInferを使うと、こう書けます。

```typescript
function createConfig<T>(defaults: T, overrides: NoInfer<T>): T {
  return { ...defaults, ...overrides };
}

const config = createConfig(
  { port: 3000, host: "localhost" },
  { port: 8080 } // エラー: Property 'host' is missing
);
```

なんと、`overrides`の型推論が抑制されて、`defaults`から推論された型`{ port: number, host: string }`に従うようになりました。これにより`host`がないとエラーを出してくれます。

## 具体的な使いどころ

実際のコードベースで使えそうな場面を考えてみました。筆者が最近作っている型安全なフォームライブラリで、こんな関数があったとします。

```typescript
function defineField<T>(
  schema: { type: string; defaultValue: T },
  validator: (value: T) => boolean
) {
  // フィールド定義を返す
}
```

この場合、`validator`の引数の型は`schema.defaultValue`から推論してほしいのですが、従来は逆に`validator`から推論されてしまうことがあった。

```typescript
// これだと validator から string が推論されて schema に影響する
defineField(
  { type: "text", defaultValue: "" },
  (value) => value.length > 0 // value: string と推論
);
```

NoInferを使えば、推論の方向を制御できます。

```typescript
function defineField<T>(
  schema: { type: string; defaultValue: T },
  validator: (value: NoInfer<T>) => boolean
) {
  // ...
}
```

これで`schema`から`T`が推論されて、`validator`はそれに従うようになりました。個人的には、この**推論の方向性を制御する**という発想がNoInferの核心だと思います。

## 複雑な型での検証

ここからもう少し複雑な例を試してみます。ジェネリクスが複数ある場合はどうなるのか。

```typescript
function merge<T, U>(
  base: T,
  extension: U,
  mapper: (item: NoInfer<T>) => U
): T & U {
  // 実装は省略
  return { ...base, ...extension };
}

const result = merge(
  { id: 1, name: "test" },
  { active: true },
  (item) => ({ active: item.id > 0 })
);
```

これを実行してみたところ、`mapper`の引数`item`が正しく`{ id: number, name: string }`と推論されました。`U`の推論には影響しないことも確認できました。

ただ、こんなコードを書いたらどうなるか気になって試してみました。

```typescript
function complexMerge<T>(
  base: T,
  update: NoInfer<T>,
  transform: (a: T, b: NoInfer<T>) => T
): T {
  return transform(base, update);
}
```

`transform`の第一引数は`T`で、第二引数は`NoInfer<T>`です。この場合、`base`から`T`が推論されて、`update`と`transform`の第二引数がそれに従う。実際に試すと、ちゃんと動きました。

```typescript
const merged = complexMerge(
  { x: 1, y: 2 },
  { x: 3, y: 4 }, // T に従う
  (a, b) => ({ x: a.x + b.x, y: a.y + b.y })
);
```

残念ながら、`transform`の第一引数から推論された型と第二引数のNoInferが競合する場合はうまくいかないこともあります。このあたり、まだ挙動を完全に把握しきれていない部分があるので、推測ですが実装を見ると最初に現れた`T`から推論するルールになってそうな気がします。

## ライブラリ設計での活用

筆者が一番「これは便利だ」と思ったのは、**ライブラリのAPI設計**での使いどころです。特にテストライブラリやモックライブラリで、「期待値を先に定義して、実際の値をそれに合わせてチェックする」みたいなパターンです。

```typescript
function assertEquals<T>(expected: T, actual: NoInfer<T>): void {
  if (JSON.stringify(expected) !== JSON.stringify(actual)) {
    throw new Error("Not equal");
  }
}

// expected から型が決まるので、actual はそれに従う
assertEquals({ id: 1, name: "test" }, { id: 1, name: "test", extra: 1 });
// エラー: extra は期待されていない
```

これ、実際に書いてみたらいくつか問題があって、最初`actual`に余計なプロパティがあってもエラーにならなかった。あ、これ`exactOptionalPropertyTypes`が必要なやつだと気づいて、tsconfig.jsonを修正しました。こういう細かいハマりポイント、実際に試さないと分からないですね。

Twitterでも「NoInferでモックの型チェックが楽になった」みたいな話を見かけました。具体的には、jestの`mockReturnValue`みたいな関数で、戻り値の型を関数定義から推論させつつ、渡す値はそれに従わせるという使い方です。

```typescript
function mockFunction<T extends (...args: any[]) => any>(
  fn: T,
  returnValue: NoInfer<ReturnType<T>>
): void {
  // モックの実装
}
```

GitHubのTypeScriptリポジトリを見ると、NoInferの追加に関する議論（#52968とか）がありました。コミュニティからの要望で実装された経緯があるみたいで、ライブラリ作者からのニーズが高かったんだなと納得しました。

:::message
TypeScript 5.4以降で使える機能です。それ以前のバージョンでは型エラーになります。
:::

## 制約と注意点

NoInferを使っていくつか気づいたことがあります。

まず、**条件型と組み合わせると複雑になる**という点です。こんなコードを書いてみました。

```typescript
type ExtractString<T> = T extends string ? T : never;

function process<T>(
  value: T,
  handler: (arg: NoInfer<ExtractString<T>>) => void
): void {
  // 実装
}
```

これ、実際に動かしてみたら型推論がうまくいかないケースがありました。`T`が`string | number`みたいなユニオン型の場合、`ExtractString<T>`が`string | never`になって、NoInferがどこに効くのか分からなくなる。このあたりは、もう少し調査が必要そうです。

それから、**NoInferを複数の引数に使うと混乱する**こともあります。

```typescript
function compare<T>(a: NoInfer<T>, b: NoInfer<T>, reference: T): boolean {
  // どこから T を推論するのか？
}
```

この場合、`reference`から`T`を推論することになるはずですが、`a`と`b`がそれに従うという挙動になります。読む人にとっては「なぜこの引数だけNoInferじゃないのか」と疑問に思うかもしれない。API設計としては、推論元を明確にするために最初の引数をNoInferなしにする方が分かりやすそうです。

## まとめ

NoInfer型を色々試してみた結果、筆者としては以下のような場面で有用だと感じました。

- 設定オブジェクトのマージ関数で、ベースの型を優先したい場合
- テストやモックで、期待値から型を決定したい場合
- ライブラリAPIで、推論の方向性を明示的に制御したい場合

一方で、条件型との組み合わせや、複数のNoInfer引数を持つ関数では、まだ挙動が掴みきれていない部分もあります。実際のプロジェクトで使うには、もう少し色々なパターンを試してみる必要がありそうです。

個人的には、この機能が広まっていくと、ライブラリの型定義がより直感的になる可能性があると思っています。ただ、NoInferを使いすぎると逆に「なぜここで推論が効かないのか」と混乱する場面も増えそうなので、バランスが重要かなと。筆者としては、これから実際のコードベースでどう活用されていくか、見守っていきたいと思います。
