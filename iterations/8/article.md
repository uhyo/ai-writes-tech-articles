---
title: "TypeScript 5.3のswitch(true)型推論の改善とユースケース"
emoji: "🔀"
type: "tech"
topics: ["typescript", "型推論", "型安全性"]
published: true
---

皆さんこんにちは。TypeScript 5.3がリリースされ、**switch(true)パターン**における型推論の改善が話題となっています。筆者も最近、複雑な条件分岐の型安全性について考える機会があり、この改善は注目すべき変更です。

switch(true)パターンは、複数の条件を評価する際に使われる手法です。従来のif-else chainよりも構造化された形で条件を記述できるため、複雑なバリデーションやガード処理で有用なパターンとして知られています。特に、複数の条件が同じレベルの重要性を持つ場合、switch文の形式で記述することで、条件の並列性が視覚的に明確になります。

しかし、TypeScript 5.2以前では、このパターンにおける型の絞り込みが十分に機能しませんでした。条件式が複雑になるほど、型推論の恩恵を受けられないという課題がありました。これにより、実務ではswitch(true)パターンの採用を見送り、if-else chainや型アサーションに頼るケースが多かったのです。

## TypeScript 5.2以前の課題

switch(true)パターンの課題は、各caseブロック内での**型の絞り込み（narrowing）**が働かなかったことです。次の例。

```typescript
type Result<T> =
  | { success: true; value: T }
  | { success: false; error: string };

function process<T>(result: Result<T>): T | null {
  switch (true) {
    case result.success:
      return result.value; // Error: Property 'value' does not exist
    case !result.success:
      console.log(result.error); // Error: Property 'error' does not exist
    default:
      return null;
  }
}
```

`result.success`が`true`であることを確認しているにもかかわらず、caseブロック内で`result.value`にアクセスできませんでした。これは、switch(true)パターンにおいて条件式の評価結果が型の絞り込みに使われていなかったためです。

型推論が働かない理由は、TypeScriptのcontrol flow analysisが、switch文のcase節における条件式を十分に解析できていなかったことにあります。通常のif文では条件式に基づく型の絞り込みが機能しますが、switch(true)パターンでは同様の解析が行われていませんでした。

実務では、この制約により、switch(true)パターンの採用を諦めてif-else chainに戻すケースが多かったと考えられます。または、型アサーションを使って無理やり型を合わせる実装も見られました。しかし、型アサーションは型安全性を損なう可能性があるため、できれば避けたい手法です。

筆者がこの問題を知ったのは、GitHubのTypeScript issuesでswitch(true)パターンの型推論に関する議論を見かけたときでした。多くの開発者が同様の課題を抱えており、改善が求められていたようです。コミュニティからのフィードバックが、今回の改善につながったのでしょう。

## TypeScript 5.3での改善

TypeScript 5.3では、switch(true)パターンにおける**control flow analysis**が強化されました。各caseの条件式が型の絞り込みに使われるようになったのです。先ほどのコードをTypeScript 5.3で試すと、型エラーが解消されるはずです。

```typescript
function process<T>(result: Result<T>): T | null {
  switch (true) {
    case result.success:
      return result.value; // OK: result.valueにアクセスできる
    case !result.success:
      console.log(result.error); // OK: result.errorにアクセスできる
    default:
      return null;
  }
}
```

各caseブロック内で、条件式に基づいた型の絞り込みが正しく働きます。`result.success`が`true`のケースでは、TypeScriptは`result`の型を`{ success: true; value: T }`に絞り込みます。これにより、型安全性を保ちながらswitch(true)パターンを使えるようになりました。

:::message
この記事はTypeScript 5.3時点の挙動です。TypeScript 5.4以降では、さらなる改善が入る可能性があります。
:::

この改善は、複雑な条件分岐を扱う際に特に有用です。従来は型アサーションに頼るか、if-else chainで代替する必要がありましたが、switch(true)という選択肢が加わったことで、コードの可読性が向上すると期待されます。

TypeScript 5.3のcontrol flow analysisは、switch(true)パターンの各case節で条件式を評価し、その結果に基づいて型を絞り込むようになりました。これは、既存のif文での型推論と同等の機能です。switch文の構造を保ちながら、型安全性も確保できるようになったのは、大きな前進だと言えます。

筆者としては、この改善により、より表現力の高いコードが書けるようになったと思います。条件分岐のパターンが増えることで、コードの意図を明確に伝えやすくなりました。

## 実践的なユースケース

switch(true)パターンの型推論改善は、いくつかの実践的なユースケースで役立ちます。

### discriminated unionとの組み合わせ

**discriminated union**とswitch(true)を組み合わせることで、型安全な処理を記述できます。

```typescript
type Shape =
  | { kind: "circle"; radius: number }
  | { kind: "rectangle"; width: number; height: number };

function calculateArea(shape: Shape): number {
  switch (true) {
    case shape.kind === "circle":
      return Math.PI * shape.radius * shape.radius;
    case shape.kind === "rectangle":
      return shape.width * shape.height;
    default:
      const _exhaustive: never = shape;
      return _exhaustive;
  }
}
```

この例では、各caseブロック内で`shape`の型が正しく絞り込まれます。`shape.kind === "circle"`が真のケースでは、TypeScriptは`shape.radius`へのアクセスを許可します。また、**exhaustive check**により、すべてのケースを処理していることが保証されます。

exhaustive checkは、TypeScriptの型安全性を保つ上で重要な機能です。default節で`never`型を使うことで、すべての型パターンが処理されていることをコンパイル時に確認できます。新しい型バリアントを追加した際、処理漏れがあればコンパイルエラーで検知できるのです。この仕組みにより、リファクタリング時の安全性が大幅に向上します。

discriminated unionは、TypeScriptでよく使われるパターンです。switch(true)との組み合わせにより、より柔軟な条件分岐が可能になりました。従来は`switch (shape.kind)`のような形式に制約されていましたが、より複雑な条件を記述できるようになったのは大きな改善だと思います。

例えば、複数のフィールドを組み合わせた条件判定や、計算結果に基づく条件分岐など、より高度なパターンマッチングが型安全に実装できるようになりました。これは、関数型プログラミングのパターンマッチングに近い表現力を持ちます。

実際のプロジェクトでは、状態管理やルーティング処理など、複雑な分岐ロジックを扱う場面で特に有用でしょう。型安全性を保ちながら、コードの可読性も向上させることができます。

### バリデーションチェーン

APIのリクエストバリデーションのような、複数の条件を順次チェックする処理でも有用です。

```typescript
type RequestBody = {
  userId?: string;
  action?: string;
  timestamp?: number;
};

function validateRequest(body: RequestBody): string | null {
  switch (true) {
    case body.userId === undefined:
      return "userIdが必要です";
    case body.action === undefined:
      return "actionが必要です";
    case body.timestamp === undefined:
      return "timestampが必要です";
    case body.timestamp < Date.now() - 60000:
      return "timestampが古すぎます";
    default:
      return null;
  }
}
```

各caseで特定のフィールドの存在を確認することで、後続の処理では型レベルでフィールドの存在が保証されます。この**バリデーションチェーン**パターンは、zodのようなバリデーションライブラリと組み合わせることで、より堅牢なバリデーション処理を実装できます。

バリデーション処理では、複数の条件を順次チェックし、エラーがあれば早期リターンするパターンが一般的です。switch(true)パターンを使うことで、このロジックを統一的な構造で記述できます。各条件が独立した検証ステップであることが、コードから明確に読み取れるのです。

筆者としては、このパターンは複数の条件を順次評価する場面で、コードの意図が明確になると思います。if-else chainでも同様の処理は書けますが、switch(true)の方が各条件が対等な関係であることが視覚的にわかりやすいのではないでしょうか。

実際のプロジェクトでは、フォームバリデーションやAPIリクエストの検証など、多段階のチェックが必要な場面でこのパターンが活躍するはずです。型安全性を保ちながら、読みやすいバリデーションロジックを実装できるようになりました。

:::details より複雑な型での検証

ジェネリクスを含む複雑な型でも、switch(true)パターンの型推論改善は機能するはずです。

```typescript
type ApiResponse<T> =
  | { status: "loading" }
  | { status: "error"; error: string }
  | { status: "success"; data: T };

function handleResponse<T>(response: ApiResponse<T>): string {
  switch (true) {
    case response.status === "loading":
      return "読み込み中...";
    case response.status === "error":
      return `エラー: ${response.error}`;
    case response.status === "success":
      return `成功`;
    default:
      const _exhaustive: never = response;
      return _exhaustive;
  }
}
```

discriminated unionのtagに基づいた型の絞り込みが正しく機能します。

:::

## まとめ

TypeScript 5.3のswitch(true)パターンにおける型推論の改善により、複雑な条件分岐を型安全に記述できるようになりました。従来はif-else chainに頼る必要があった場面でも、switch(true)という構造化されたパターンを選択できます。

実際のプロジェクトでは、バリデーションチェーンやdiscriminated unionの処理など、複数の条件を順次評価する場面で有用なはずです。特に、各条件が対等な関係にある場合、switch(true)パターンの方がコードの意図が明確になると考えられます。

この改善は、TypeScriptの型推論能力が着実に向上していることを示す事例の一つです。control flow analysisの精度が上がることで、より複雑なロジックでも型安全性を保てるようになりました。開発者は型アサーションに頼ることなく、型システムの恩恵を最大限に活用できます。

型アサーションは、型安全性を損なうリスクがあります。コンパイラの型チェックを無視することになるため、実行時エラーの原因となる可能性があるのです。switch(true)パターンで型推論が働くようになったことで、このようなリスクを避けられるようになりました。

筆者としては、この改善がどの程度実務で使われるようになるか、また今後のTypeScriptバージョンでさらなる型推論の改善が入るかどうか、見守っていきたいと思います。Reactのようなフレームワークでの活用パターンなど、まだ試していない応用例も多くあるはずです。

switch(true)パターンは、従来は型推論の面で制約がありましたが、TypeScript 5.3でこの課題が解消されました。今後、このパターンを採用するプロジェクトが増えていく可能性があります。コードレビューでswitch(true)を見かける機会も増えるかもしれません。

また、既存のif-else chainをswitch(true)パターンにリファクタリングするケースも出てくるでしょう。特に、複数の条件が並列に並んでいる場合、switch(true)の方が構造が明確になることがあります。チームでのコーディング規約に、このパターンの使用ガイドラインを追加する価値があるかもしれません。

推測ですが、TypeScript 5.4以降では、さらに複雑なcontrol flow analysisが入る可能性もあります。型推論の改善は継続的に行われているため、今後のバージョンにも期待したいところです。TypeScriptチームの取り組みにより、より安全で表現力の高いコードが書けるようになっていくでしょう。
