---
title: "TypeScript 5.4のNoInfer型とジェネリクス推論の実践的な使いどころ"
emoji: "🔍"
type: "tech"
topics: ["typescript", "型システム", "ジェネリクス"]
published: true
---

皆さんこんにちは。TypeScript 5.4がリリースされ、新しい組み込み型として**NoInfer型**が追加されました。この型は、**ジェネリクス推論**において特定の型引数を推論対象から除外するための機能です。型推論の制御については、TypeScriptコミュニティでもたびたび議論されてきた話題ですが、今回ついに公式の解決策が提供されたことになります。筆者も最近、ジェネリクス関数の型推論について考える機会があり、この機能には注目していました。

## ジェネリクス推論の問題

まず、NoInferがどういった問題を解決するのか確認してみます。次のような関数を考えてみます。

```typescript
function createConfig<T extends string>(
  key: T,
  defaultValue: T
) {
  return { key, defaultValue };
}

// 使用例
const config1 = createConfig("mode", "development");
const config2 = createConfig("mode", "staging");
```

このコードでは、`key`と`defaultValue`の両方から型引数`T`が推論されるはずです。しかし、実際にTypeScriptがどう推論するかというと、`T`は`"mode" | "development"`や`"mode" | "staging"`のようなユニオン型になってしまいます。これは型推論アルゴリズムが、すべての引数位置から情報を集めて「最も一般的な型」を選ぶためです。

これが問題になるケースとして、`key`の値で型を固定したいのに、`defaultValue`の影響で型が広がってしまう状況があります。たとえば、設定のキーは厳密に管理したいが、デフォルト値は柔軟に受け入れたい場合です。従来はこういったケースで型引数を明示的に指定する必要がありました。

:::message
この記事はTypeScript 5.4以降の機能について解説しています。それ以前のバージョンでは動作しません。
:::

## NoInfer型の基本的な使い方

TypeScript 5.4で追加された`NoInfer<T>`型を使うと、特定の位置での型推論を抑制できます。先ほどの例を修正してみます。

```typescript
function createConfig<T extends string>(
  key: T,
  defaultValue: NoInfer<T>
) {
  return { key, defaultValue };
}

const config1 = createConfig("mode", "development");
// Tは "mode" に推論される（defaultValueからは推論されない）

const config2 = createConfig("mode", "staging");
// こちらも T は "mode"
```

`defaultValue`の型を`NoInfer<T>`にすることで、この引数からは型推論が行われなくなります。その結果、`T`は`key`引数からのみ推論され、`"mode"`という厳密な型になるはずです。型推論の対象を限定することで、より意図的な型の制御が可能になります。

もし`defaultValue`に`key`と互換性のない値を渡そうとすると、当然エラーになります。

```typescript
const config3 = createConfig("mode", "production");
// OK: "production" は string なので T extends string を満たす

const config4 = createConfig("mode", 123);
// エラー: number は NoInfer<T> に代入できない
```

このように、NoInfer型を使うと「この位置では型推論に寄与しないが、推論された型に適合する必要がある」という制約を表現できます。これは一方向の型制約と考えるとわかりやすいでしょう。

## ライブラリ設計での活用

実際のプロジェクトでは、ライブラリ関数のAPI設計でこのパターンが有効です。次のようなバリデーション関数を考えてみます。

```typescript
function validate<T>(
  value: unknown,
  schema: Schema<T>,
  errorMessage: NoInfer<T> extends string ? string : never
): T | null {
  // バリデーションロジック
  if (isValid(value, schema)) {
    return value as T;
  }
  return null;
}

interface Schema<T> {
  type: string;
  validate: (value: unknown) => value is T;
}
```

この例では、`schema`から型`T`を推論させ、`errorMessage`は推論に寄与しないようにしています。また、条件型を組み合わせることで、`T`が`string`の場合のみエラーメッセージを要求する仕組みも実現できるはずです。NoInfer型は、こういった複雑な型制約の表現に役立ちます。

zodのようなバリデーションライブラリでは、スキーマ定義から型を導出する設計が一般的ですが、NoInfer型を使うことでさらに柔軟な型推論制御が可能になります。特に、ユーザー提供の値と内部スキーマの型を分離したい場合に有効と考えられます。

## ジェネリクス制約との組み合わせ

NoInfer型は、複雑なジェネリクス制約と組み合わせると、より高度な型安全性を実現できます。次の例を見てみます。

```typescript
interface Event {
  type: string;
  payload: unknown;
}

function createEventHandler<T extends Event>(
  eventType: T["type"],
  handler: (event: NoInfer<T>) => void
) {
  return {
    type: eventType,
    handle: handler
  };
}

// 使用例
type UserEvent = {
  type: "user";
  payload: { userId: string };
};

const handler = createEventHandler<UserEvent>(
  "user",
  (event) => {
    console.log(event.payload.userId);
  }
);
```

この設計では、`eventType`から`T["type"]`を推論させつつ、`handler`では完全な`T`型のイベントオブジェクトを要求しています。`NoInfer<T>`を使うことで、`handler`の引数からは型推論が行われず、`eventType`の値だけで型が決定されるはずです。これにより、イベント型の一貫性が保たれます。

ただし、この例では明示的に型引数`<UserEvent>`を渡しているので、実際には推論の恩恵を受けていません。もっと実用的なパターンとしては、次のような設計が考えられます。

```typescript
function on<E extends Event>(
  type: E["type"],
  handler: (event: NoInfer<E>) => void
): void {
  // イベントリスナー登録
}

// 型引数を明示せずに使う
declare const userEventType: "user";
on(userEventType, (event) => {
  // event の型は推論される
});
```

このケースでは、`type`の値から`E`を推論させ、その推論結果を`handler`で利用する流れになります。型引数を明示せずに型安全性を確保できるのが利点です。

:::details NoInfer型の制約について

NoInfer型にはいくつか制約があります。まず、NoInfer自体は型推論を完全に無効化するわけではなく、あくまで「この位置からは推論しない」という指示です。他の位置から十分な情報が得られない場合、型推論は失敗するはずです。

また、NoInfer型はネストした型の内部にも適用できますが、複雑な条件型や mapped type と組み合わせる場合、予期しない動作をする可能性があります。TypeScript issuesでも、エッジケースについての議論が続いている状況です。特に、conditional typesとの相互作用については、まだ十分に検証されていない部分があるようです。

:::

## デフォルト値パターンの改善

NoInfer型のもうひとつの実用的な使いどころは、**デフォルト値**を持つ関数の型推論改善です。

```typescript
function withDefault<T>(
  value: T | undefined,
  defaultValue: NoInfer<T>
): T {
  return value !== undefined ? value : defaultValue;
}

const result1 = withDefault(undefined, "default");
// T は "default" に推論される

const result2 = withDefault("actual", "default");
// T は "actual" | "default" ではなく string に推論される
```

従来のアプローチでは、`value`と`defaultValue`の両方から型が推論されるため、ユニオン型が生成されてしまいました。NoInfer型を使うことで、`value`が`undefined`の場合は`defaultValue`の型を、それ以外の場合は`value`の型を優先的に推論させることができるはずです。

実際には、この例も完全には意図通りに動かないケースがあります。というのも、TypeScriptの型推論アルゴリズムは複数の候補から「最も具体的な型」を選ぼうとするため、NoInferで推論を抑制しても、最終的な型が広くなることがあるからです。この辺りの挙動は、TypeScriptのバージョンによっても微妙に変わる可能性があります。

筆者としては、この辺りの挙動はまだ完全には理解できていない部分もあり、実際のプロジェクトで使いながら挙動を確認していく必要があると感じています。型推論の詳細な仕様は、実装を追いかけないと分からない部分も多いです。

## メソッドチェーンでの利用

流暢なインターフェースを持つメソッドチェーンでも、NoInfer型は役立つ可能性があります。

```typescript
class QueryBuilder<T> {
  where<K extends keyof T>(
  key: K,
    value: NoInfer<T[K]>
  ): QueryBuilder<T> {
    // クエリ構築
    return this;
  }

  execute(): T[] {
    // クエリ実行
    return [];
  }
}

interface User {
  id: number;
  name: string;
  email: string;
}

const users = new QueryBuilder<User>()
  .where("name", "Alice")
  .where("email", "alice@example.com")
  .execute();
```

この設計では、`where`メソッドの`key`引数から`K`を推論し、`value`は`T[K]`型に適合する必要がありますが、推論には寄与しません。これにより、プロパティ名から値の型が自動的に決まる型安全なクエリビルダーが実現できるはずです。メソッドチェーンの各ステップで型安全性が保たれるのは大きな利点です。

ただし、メソッドチェーンでは各メソッド呼び出しが独立して型推論されるため、NoInferの効果が期待通りに現れないケースもあります。特に、**型の拡大**（widening）が発生すると、意図しない型になることがあります。筆者はこのパターンをまだ本格的に試していないので、推測の域を出ませんが、TypeScript 5.5でさらに改善される可能性もあると考えています。

## まとめ

TypeScript 5.4で追加されたNoInfer型は、ジェネリクス関数における型推論の制御を可能にする機能です。特定の引数位置からの型推論を抑制することで、より意図的な型推論動作を実現できます。型推論の柔軟性と型安全性のバランスを取るための新しい道具と言えるでしょう。

実用的な使いどころとしては、ライブラリ関数のAPI設計、デフォルト値を持つ関数、イベントハンドラーの型付けなどが挙げられます。ただし、TypeScriptの型推論アルゴリズムは複雑で、NoInfer型だけですべてのケースに対応できるわけではありません。型推論の挙動は、引数の順序や他の型制約との組み合わせによっても変わってきます。

筆者としては、この機能がどこまで実践的に使えるのか、今後のプロジェクトで試しながら見極めていきたいと思います。型推論の挙動は時に予測が難しいですが、NoInfer型という新しい道具が加わったことで、より細かい制御が可能になったのは確かです。この機能を使いこなすことで、より型安全なライブラリ設計ができるようになるはずです。
