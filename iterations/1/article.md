---
title: "TypeScript 5.6のArbitrary Module Namespace Identifier Namesを試して挙動を調べる"
emoji: "📦"
type: "tech"
topics: ["typescript", "javascript", "es2022"]
published: true
---

皆さんこんにちは。2024年9月にTypeScript 5.6がリリースされ、ES2022の仕様である**Arbitrary Module Namespace Identifier Names**のサポートが追加されました。この機能により、**文字列リテラル**をモジュールのエクスポート・インポート名として使えるようになります。

地味な機能に見えますが、WebAssemblyとの連携やツールの実装において重要な意味を持つ機能です。この記事では実際にコードを書いて挙動を調べていきます。様々なパターンを試して理解を深めます。

:::message
この記事はTypeScript 5.6時点の挙動です。
:::

## この機能とは何か

従来、JavaScriptのimport/export文では、識別子として有効な名前しか使えませんでした。つまり、英数字とアンダースコアなどに制限されていました。

```typescript
// 従来: 識別子として有効な名前のみ
export { value as myExport };
import { myImport } from "./module";
```

しかし、**WebAssembly**のエクスポート名は任意のUnicode文字列を許容します。ハイフンやスペースを含む名前も使えます。これにより、JavaScriptとWebAssembly間の相互運用に制約が生まれていました。

ES2022で導入された**Arbitrary Module Namespace Identifier Names**は、文字列リテラルを使ったエクスポート・インポートを可能にします。これによりJavaScriptのモジュールシステムの表現力が向上しました。

```typescript
// ES2022以降: 文字列リテラルが使える
export { value as "my-export" };
import { "my-import" as myValue } from "./module";
```

TypeScript 5.6でこの機能がサポートされたことで、型定義を含めた形でこの構文を使えるようになりました。この実装はEvan Wallaceさんによる貢献です。esbuildの作者として知られる方です。型推論やエディタのサポートも充実しています。

## 基本的な使い方を試す

まず、シンプルなケースを試してみます。ハイフンを含む名前をエクスポート・インポートしてみます。

```typescript:module.ts
const greeting = "Hello";
const count = 42;

export { greeting as "greeting-message" };
export { count as "item-count" };
```

このモジュールをインポートしてみます。文字列名を使って各エクスポートを取り込みます。

```typescript:main.ts
import { "greeting-message" as msg, "item-count" as num } from "./module";

console.log(msg); // "Hello"
console.log(num); // 42
```

実行すると、問題なく動作しました。ハイフン付きの名前でエクスポート・インポートができています。従来の識別子では使えなかった記号が問題なく機能します。

**型推論**も正しく働いており、`msg`は`string`型、`num`は`number`型として認識されます。

```typescript
msg.toUpperCase(); // OK
num.toFixed(2);    // OK
msg.toFixed(2);    // エラー: Property 'toFixed' does not exist on type 'string'
```

型安全性が保たれているのは良いですね。文字列名を使っても型情報は一切失われません。

## 様々なケースを試す

次に、より複雑なケースを見ていきます。再エクスポートやnamespace exportなども試します。

### 再エクスポート

文字列名での再エクスポートはどうでしょうか。他のモジュールの文字列名を別のモジュールから公開してみます。

```typescript:reexport.ts
export { "greeting-message" as "greeting-message" } from "./module";
export { "item-count" as "count" } from "./module";
```

これも問題なく動作します。文字列名をそのまま再エクスポートしたり、通常の識別子に変換したりできます。柔軟な変換が可能です。

### namespace export

namespace exportでも試してみます。namespace全体を文字列名でエクスポートできるか確認します。

```typescript:namespace.ts
export * as "my-namespace" from "./module";
```

```typescript:use-namespace.ts
import { "my-namespace" as ns } from "./namespace";

console.log(ns["greeting-message"]); // "Hello"
console.log(ns["item-count"]);       // 42
```

実行してみると、namespaceとして正しく機能しました。`ns`は文字列プロパティを持つオブジェクトとして扱われます。ブラケット記法でプロパティにアクセスできます。

型定義を見ると、`ns`の型は次のようになっています。文字列リテラル型としてプロパティが定義されています。

```typescript
typeof ns: {
  "greeting-message": string;
  "item-count": number;
}
```

通常のnamespace exportと同じように型が推論されていますが、プロパティ名が文字列リテラルになっている点が面白いです。筆者はこの型表現を初めて見たので、ちょっと新鮮でした。TypeScriptの型システムは柔軟です。

### Unicode文字を使う

仕様では任意のUnicode文字列が使えることになっています。実際に様々な文字を試してみましょう。日本語や絵文字も使えるはずです。

```typescript:unicode.ts
const data = { value: 100 };

export { data as "データ" };
export { data as "🎯" };
export { data as "kebab-case-name" };
```

```typescript:use-unicode.ts
import { "データ" as japanese } from "./unicode";
import { "🎯" as emoji } from "./unicode";
import { "kebab-case-name" as kebab } from "./unicode";

console.log(japanese); // { value: 100 }
console.log(emoji);    // { value: 100 }
console.log(kebab);    // { value: 100 }
```

これも正常に動作します。日本語、絵文字、ケバブケースなど、あらゆる文字列が使えることが確認できました。仕様通りの実装になっています。

エディタの補完も正しく機能し、`"データ"`や`"🎯"`が候補として表示されます。VSCodeのTypeScript言語サービスがきちんとサポートしているようです。開発体験としては従来の識別子と変わりなく使えます。

## 型チェックの挙動を調べる

型定義ファイルではどうなるのか見てみます。`.d.ts`ファイルにどのように出力されるかを確認します。

```typescript:types.ts
export interface User {
  name: string;
  age: number;
}

export const user: User = { name: "Alice", age: 30 };

export { User as "user-type" };
export { user as "user-data" };
```

このファイルから生成される`.d.ts`を確認します。TypeScriptコンパイラに`--declaration`オプションを渡します。

```bash
tsc --declaration types.ts
```

生成された`types.d.ts`:

```typescript
export interface User {
    name: string;
    age: number;
}
export declare const user: User;
export { User as "user-type" };
export { user as "user-data" };
```

文字列リテラルがそのまま型定義に出力されています。宣言ファイルの生成も正しく機能していることが分かります。これを利用する側では、次のように書けます。

```typescript:use-types.ts
import { "user-type" as UserType, "user-data" as userData } from "./types";

const newUser: UserType = { name: "Bob", age: 25 }; // OK
console.log(userData.name); // "Alice"
```

型エイリアスとしても正しく機能しました。文字列名でも型の情報は完全に保持されます。

### defaultエクスポートとの組み合わせ

default exportと併用できるか試します。同じモジュールから両方のエクスポート方式を使えるか確認します。

```typescript:mixed.ts
export default function greet() {
  return "Hello";
}

export { greet as "greet-function" };
```

```typescript:use-mixed.ts
import defaultGreet from "./mixed";
import { "greet-function" as namedGreet } from "./mixed";

console.log(defaultGreet()); // "Hello"
console.log(namedGreet());   // "Hello"
```

両方とも正常に動作します。defaultエクスポートと文字列名のexportは問題なく共存できるようです。

ただし、同じ値を異なる名前でエクスポートしているため、実用上は避けた方が良いパターンかもしれません。この例は単なる動作確認です。TypeScriptのモジュールシステムはこのような組み合わせも柔軟に扱えます。

## 実用的な使いどころ

この機能が実際に役立つ場面を考えてみます。主にWebAssemblyやコード生成の文脈で活用されます。

### WebAssemblyとの連携

WebAssemblyモジュールは任意の文字列をエクスポート名として持てます。ハイフンやスペースを含む名前も普通に使われます。TypeScriptでこれらに型を付ける際、従来は間接的な方法が必要でした。

```typescript
// 従来の回避策
declare module "*.wasm" {
  const module: {
    ["my-wasm-func"]: (x: number) => number;
  };
  export default module;
}
```

Arbitrary Module Namespace Identifier Namesを使えば、より直接的な型定義が書けます。この書き方の方がWASMモジュールの構造を素直に表現できます。

```typescript
// TypeScript 5.6以降
declare module "*.wasm" {
  export { wasmFunc as "my-wasm-func" };
  function wasmFunc(x: number): number;
}
```

筆者はまだWebAssemblyプロジェクトで試していないのですが、型安全性が向上する可能性があります。

### ツールによるコード生成

esbuildの`inject`機能などでは、開発者が直接書かない名前を使うことがあります。そのような場面で、識別子の制約を受けずに任意の名前を使える点は便利です。ビルドツールの柔軟性が高まります。

また、GraphQLのスキーマからTypeScriptコードを生成する際、フィールド名がJavaScript識別子として無効な場合でも、文字列リテラルとしてエクスポートできます。コード生成ツールの実装の自由度が高まります。

実際のプロダクトでどれだけ活用されるかは未知数ですが、今後のエコシステムの進化に期待したいところです。

## まとめ

TypeScript 5.6で追加されたArbitrary Module Namespace Identifier Namesについて、実際にコードを書いて挙動を確認しました。

基本的な使い方から型定義の出力、Unicode文字の扱いまで一通り試した結果、仕様通りに動作することが分かりました。型推論も正しく機能し、エディタのサポートも良好です。実装の完成度は高いと言えます。

この機能の背景にはGitHubのissue #40594があり、WebAssemblyとの相互運用性向上を目的として実装されました。2020年から議論が続いていた機能です。実装までに時間がかかりましたが、ようやく利用可能になりました。地味ではありますが、JavaScriptエコシステム全体の柔軟性を高める重要な一歩だと思います。

筆者としては、まだこの機能を必要とする場面に遭遇していませんが、WebAssemblyやコード生成ツールが普及するにつれて、徐々に活用の場が増えていくのではないかと考えています。これからどのような使い方が生まれてくるか、また見守っていきたいと思います。
