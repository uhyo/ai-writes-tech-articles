---
title: "TypeScript 5.4のNoInfer型で型推論を制御する"
emoji: "🔒"
type: "tech"
topics: ["typescript", "型システム", "generics"]
published: true
---

皆さんこんにちは。TypeScript 5.4がリリースされ、**NoInfer型**という新しいユーティリティ型が追加されました。型推論を意図的に制限するという、一見すると不思議な機能です。

TypeScriptプロジェクトでは、**ジェネリクス**を使った関数で**型推論**が過度に広がってしまう問題があります。筆者も最近、この問題について考える機会があったのですが、NoInfer型はまさにこの課題を解決するために導入されたようです。

:::message
この記事はTypeScript 5.4時点の挙動を扱っています。今後のバージョンで挙動が変わる可能性があります。
:::

## 型推論が広がりすぎる問題

まずは、NoInfer型が解決しようとしている問題を見ていきます。

```typescript
function createConfig<T>(initial: T, override: T): T {
  return { ...initial, ...override };
}

const config = createConfig(
  { mode: "development" as const },
  { mode: "production" }
);
```

このコード、一見問題なさそうに見えますが、実は`T`が`{ mode: "development" | "production" }`に推論されてしまいます。`initial`の`as const`で固定したつもりが、`override`の型推論によって広げられているわけです。

従来は、この問題を避けるために`initial`と`override`で別々の**型パラメータ**を使うか、明示的に型注釈を書く必要がありました。どちらも面倒です。

筆者はこういったケースに何度か遭遇したことがあり、型推論の制御は意外と厄介な問題だと感じていました。

## NoInferの基本

NoInfer型を使うと、特定の位置からの型推論を無効化できます。

```typescript
function createConfig<T>(initial: T, override: NoInfer<T>): T {
  return { ...initial, ...override };
}

const config = createConfig(
  { mode: "development" as const },
  { mode: "production" } // エラー！
);
```

`override`を`NoInfer<T>`でラップすることで、この引数からは`T`が推論されなくなります。結果として、`T`は`initial`の型のみから推論され、`{ mode: "development" }`という狭い型になるはずです。

`override`に`"production"`を渡そうとすると、TypeScriptは「`"development"`型に`"production"`は代入できない」というエラーを出します。これが狙い通りの挙動です。

個人的には、この「推論に参加しない」という概念が面白いと思いました。型レベルでの制御フローという感じがします。従来の型システムでは、すべての引数位置が平等に型推論に寄与していましたが、NoInferによって推論の優先度を明示的にコントロールできるようになりました。筆者はこの機能を知ったとき、型パラメータに「重み付け」ができるようになったと感じました。

## より複雑な例

関数の引数が複数ある場合、どこにNoInferを適用するかで挙動が変わります。

```typescript
function merge<T>(
  base: T,
  patch1: NoInfer<T>,
  patch2: NoInfer<T>
): T {
  return { ...base, ...patch1, ...patch2 };
}

const result = merge(
  { x: 1, y: 2 },
  { x: 10 },
  { z: 3 } // エラー！zはTに存在しない
);
```

`base`だけから`T`が推論されるので、`patch1`と`patch2`は`base`と同じ構造を持つ必要があります。新しいプロパティを追加しようとするとエラーになるはずです。

このパターンは、複数のオブジェクトをマージする関数で有用です。最初の引数が「正」として扱われ、後続の引数はそれに従う形になります。設定の上書きやパッチ適用といったユースケースで活躍するでしょう。

では、逆に最後の引数だけを推論に参加させたい場合はどうでしょうか。

```typescript
function mergeInto<T>(
  base: NoInfer<T>,
  patch: T
): T {
  return { ...base, ...patch };
}
```

こうすると、`patch`の型から`T`が決まり、`base`はその型に適合する必要があります。使いどころは限定的ですが、「拡張されたオブジェクト型」を受け取りたいときに有効でしょう。

## 配列とユニオン型のケース

NoInferは配列や**ユニオン型**との組み合わせでも力を発揮します。

```typescript
function findOrDefault<T>(
  items: T[],
  predicate: (item: T) => boolean,
  defaultValue: NoInfer<T>
): T {
  const found = items.find(predicate);
  return found ?? defaultValue;
}

const numbers = [1, 2, 3] as const;
const result = findOrDefault(
  numbers,
  (n) => n > 5,
  0 // エラー！0は1 | 2 | 3に代入できない
);
```

`items`が`readonly [1, 2, 3]`型なので、`T`は`1 | 2 | 3`と推論されます。`defaultValue`を`NoInfer<T>`にすることで、デフォルト値が配列の要素と同じ型を持つことを強制できます。

これは実用的ですね。筆者が特に気に入ったのは、このパターンです。従来なら「デフォルト値の型が合わない」というバグを実行時まで見逃す可能性がありました。配列がas constで定義されている場合、その厳密な型を保持しながら関数を設計できるのは便利です。リテラル型を活用した堅牢なAPIを作る上で、NoInferは重要な役割を果たします。

ユニオン型の**型の絞り込み**にも使えます。

```typescript
function validate<T extends string>(
  allowed: T[],
  value: NoInfer<T>
): boolean {
  return allowed.includes(value);
}

validate(["admin", "user"], "guest"); // エラー！
```

`allowed`配列から許可される文字列のユニオン型が推論され、`value`はその型でなければなりません。これにより、コンパイル時に不正な値を検出できます。

## 実践的な使いどころ

実際のプロジェクトでは、どういう場面でNoInferが役立つでしょうか。

**1. 設定オブジェクトのマージ**

フレームワークやライブラリで**デフォルト設定**とユーザー設定をマージする際、デフォルト設定の型を基準にしたいケースは多いです。

```typescript
function withDefaults<T>(defaults: T, userConfig: NoInfer<Partial<T>>): T {
  return { ...defaults, ...userConfig };
}

const defaults = { timeout: 3000, retry: true };
const config = withDefaults(defaults, {
  timeout: 5000,
  retries: 2 // エラー！typo検出
});
```

`userConfig`を`NoInfer<Partial<T>>`にすることで、typoを防げるはずです。これは地味ですが確実に役立つパターンだと考えられます。設定オブジェクトのマージは多くのライブラリで見られる処理なので、この型安全性の向上は実用的な価値があります。ユーザーがドキュメントを読まなくても、型エラーによって正しい使い方がわかるのは理想的です。

**2. ビルダーパターン**

**流暢なインターフェース**（fluent interface）で、**メソッドチェーン**の途中で型を固定したい場合にも使えます。

```typescript
class QueryBuilder<T> {
  constructor(private schema: T) {}

  where(condition: NoInfer<Partial<T>>): this {
    // 条件はスキーマに従う
    return this;
  }
}

const builder = new QueryBuilder({ id: 1, name: "test" });
builder.where({ id: 2 }); // OK
builder.where({ age: 30 }); // エラー！
```

スキーマに存在しないフィールドを指定しようとすると、コンパイル時にエラーになります。これにより、ビルダーパターンでの型安全性が大幅に向上するはずです。従来は実行時エラーになっていたケースを、コンパイル時に検出できるのは大きなメリットです。

**3. 型安全なイベントハンドラ**

イベント名とハンドラの対応を型安全にする際、NoInferで**型の拡大**を防げるかもしれません。

```typescript
type EventMap = {
  click: { x: number; y: number };
  focus: { element: string };
};

function on<K extends keyof EventMap>(
  event: K,
  handler: (data: NoInfer<EventMap[K]>) => void
) {
  // ...
}

on("click", (data) => {
  console.log(data.x); // OK
});
```

このパターンは、TypeScript issuesでも議論されている話題のようです。まだ試していませんが、Reactのカスタムフックとの組み合わせも面白そうだと感じています。イベント駆動のアーキテクチャでは、型安全性が特に重要になるので、NoInferの活用場面は多そうです。

## まとめと今後

NoInfer型は、型推論を「制限する」という一見ネガティブに見える機能ですが、実際には型安全性を高めるための重要なツールです。

特に、ライブラリやフレームワークを作る際、ユーザーに対して「この引数は基準となる型に従ってください」と型レベルで伝えられる点が価値だと思います。筆者自身、以前は型パラメータを分割するなど回りくどい方法に頼っていましたが、NoInferでシンプルに表現できるようになりました。

筆者としては、この機能がどこまで普及するか見守っていきたいと思います。推測ですが、既存のライブラリがNoInferを採用し始めると、より洗練されたユースケースが見えてくるはずです。TypeScript 5.5以降でさらなる改善があるかもしれません。

一方で、NoInferを使いすぎると型定義が複雑になりすぎる懸念もあります。適切なバランスを見つけるのは、今後のコミュニティの課題になるでしょう。
