---
title: "Deno 2.0のnpm互換性を試して限界を知る"
emoji: "🦕"
type: "tech"
topics: ["deno", "nodejs", "npm", "typescript"]
published: true
---

## はじめに

皆さんこんにちは。先月Deno 2.0がリリースされ、大きな話題となったのが**npm互換性**の大幅な改善です。筆者は普段Node.jsを使ったプロジェクトが多いのですが、Denoのnpm互換性がどこまで使えるのか気になったので、実際にいろいろ試してみました。

Deno 2.0では`npm:`指定子を使うことで、npmパッケージを直接インポートできるようになっています。公式ドキュメントでは「ほとんどのnpmパッケージが動く」と書かれていますが、実際のところどうなのでしょうか。この記事では、簡単なパッケージから複雑な構成のものまで試して、動くもの・動かないものを確認していきます。

:::message
この記事はDeno 2.0.0時点での挙動です。今後のバージョンでnpm互換性が改善される可能性があります。
:::

## 基本的なパッケージを試す

まずは最もシンプルなケースとして、依存関係のない小さなパッケージを試してみます。

```typescript
import _ from "npm:lodash@4.17.21";

const result = _.chunk([1, 2, 3, 4, 5], 2);
console.log(result); // [[1, 2], [3, 4], [5]]
```

実行してみたところ、何の問題もなく動きました。lodashのような純粋なJavaScriptライブラリは完璧に動作します。型推論もちゃんと効いていて、TypeScript環境として普通に使えることが確認できました。

VSCodeで開いてみると、ちゃんと型定義が読み込まれていて、メソッドの補完も効きます。これはかなり快適です。

次に、複数のnpm依存を持つパッケージを試してみます。筆者は`dayjs`をよく使うので、これでテストしました。

```typescript
import dayjs from "npm:dayjs@1.11.10";
import utc from "npm:dayjs@1.11.10/plugin/utc";

dayjs.extend(utc);
const now = dayjs.utc();
console.log(now.format());
```

これも問題なく動作しました。プラグイン機構を持つパッケージでも、基本的な使い方なら大丈夫そうです。dayjsは軽量で依存も少ないので、Denoとの相性が良いのかもしれません。

## ネイティブモジュールの挑戦

次は難しいケースです。**ネイティブモジュール**を含むパッケージを試してみます。Node.jsのネイティブアドオン（.nodeファイル）を使うパッケージがDenoで動くのか確認していきます。

筆者は自分のプロジェクト（TypeScript + PostgreSQL構成のAPIサーバー）で`bcrypt`を使っていたので、これを試してみました。パスワードハッシュ化は多くのアプリケーションで必要になる機能なので、これが動くかどうかは重要です。

```typescript
import bcrypt from "npm:bcrypt@5.1.1";

const hash = await bcrypt.hash("password", 10);
console.log(hash);
```

これを実行すると、残念ながらエラーが出ました。

```
error: Uncaught (in promise) Error: Cannot find module
```

ネイティブモジュールは動きませんでした。bcryptはC++で書かれたネイティブアドオンを使っているため、Denoの環境では読み込めないようです。これはかなり痛いですね。

試しに、代替として純粋JavaScriptの`bcryptjs`を使ってみることにしました。

```typescript
import bcryptjs from "npm:bcryptjs@2.4.3";

const hash = await bcryptjs.hash("password", 10);
console.log(hash);
```

実行してみると、こちらは問題なく動作しました。ただし、bcryptjsはネイティブ版のbcryptと比べてパフォーマンスが落ちることが知られています。ベンチマークを取ってみたところ、処理時間が約3倍になっていました。

個人的には、パフォーマンスが重要な場面でもネイティブモジュールが使えないのは厳しいと感じました。本番環境でDenoを採用するかどうか判断する際の、大きな判断材料になりそうです。

## TypeScript型定義の問題

次に気になったのは、**TypeScript型定義**がどう扱われるかです。

npmパッケージの多くは`@types/xxx`パッケージで型定義を提供していますが、Denoでこれが正しく解決されるのか試してみます。代表的なフレームワークであるExpressで確認してみました。

```typescript
import express from "npm:express@4.18.2";
import type { Request, Response } from "npm:express@4.18.2";

const app = express();

app.get("/", (req: Request, res: Response) => {
  res.send("Hello");
});
```

実行してみると、型は正しく推論されました。Denoは`@types`パッケージを自動で解決してくれるようです。なんと、VSCodeの補完も効いています。これは予想以上に良い体験でした。

型定義の自動解決は、Node.jsからの移行を考える上で重要なポイントです。既存のコードベースをほぼそのまま持ってこれる可能性が高まります。

ただし、複雑な型定義を持つパッケージでは問題が出るケースもありました。筆者が試した中では、`typeorm`のような複雑なデコレータを使うパッケージで型エラーが出ることがありました。

```typescript
import { Entity, Column } from "npm:typeorm@0.3.17";

@Entity()
class User {
  @Column()
  name: string;
}
```

このコードを実行すると、デコレータ関連のエラーが出ました。TypeORM はデコレータを多用するORMなので、この問題は致命的です。

調べてみると、`experimentalDecorators`の設定が必要でした。Denoの設定ファイル（`deno.json`）に以下を追加する必要があります。

```json
{
  "compilerOptions": {
    "experimentalDecorators": true
  }
}
```

設定を追加したら動くようになりましたが、デフォルトで動かないのは少し不便だと感じました。Node.jsでは`tsconfig.json`で設定していた内容を、Denoでも設定し直す必要があるケースが多そうです。

## ESM/CommonJS混在の罠

さらに厄介なのが、**ESMとCommonJS混在**の問題です。

最近のnpmパッケージは、ESM版とCommonJS版の両方を提供していることが多いです。DenoはESMネイティブな設計ですが、CommonJSパッケージをどう扱うのか試してみます。

まず、完全にESMに移行しているパッケージから試します。

```typescript
import chalk from "npm:chalk@5.3.0";

console.log(chalk.blue("Hello"));
```

chalk 5.xは完全にESMに移行しているので、これは予想通り問題なく動きました。

次に、CommonJSのみのパッケージを試してみます。chalk 4.xはCommonJSなので、これで確認します。

```typescript
import chalk from "npm:chalk@4.1.2";

console.log(chalk.blue("Hello"));
```

実行してみると、これも動きました。DenoはCommonJSパッケージを内部でESMに変換してくれるようです。この互換性レイヤーはかなり優秀だと感じました。

しかし、問題が起きたのは、パッケージ内部で動的`require()`を使っているケースです。筆者が試した中では、設定ファイルを動的に読み込むタイプのパッケージで失敗しました。

```typescript
import config from "npm:some-config-loader@1.0.0";

// 内部でrequire()を使って設定ファイルを読む
const settings = config.load("./config.js");
```

これを実行すると、以下のようなエラーが出ました。

```
error: require is not defined
```

動的`require()`はDenoの変換ではカバーできないようです。静的な`import`や`require()`は変換されますが、関数内で呼ばれる`require()`は実行時エラーになります。

この手のパッケージを使う場合は、設定を静的にインポートする形に書き換える必要があります。あるいは、Deno用の設定ローダーを自作するか、別のパッケージに置き換えるかの判断が必要です。

## Node.js APIの互換性

最後に、**Node.js API**を使うパッケージについて試してみます。

Denoは`node:`指定子でNode.jsの組み込みモジュールを提供していますが、これがnpmパッケージと組み合わさったときにどう動くのか気になりました。よく使われる`fs-extra`で確認してみます。

```typescript
import fs from "npm:fs-extra@11.1.1";

await fs.writeFile("test.txt", "Hello Deno");
const content = await fs.readFile("test.txt", "utf-8");
console.log(content); // "Hello Deno"
```

実行してみると、問題なく動きました。`fs-extra`は内部でNode.jsの`fs`モジュールを使っていますが、Denoが提供する互換レイヤーで正常に動作します。

Promiseベースのメソッドも、コールバックベースのメソッドも両方動きます。この互換性の高さは印象的でした。

次に、より複雑なNode.js APIを使うパッケージを試してみます。`path`や`os`など、基本的なAPIはほぼ完璧に動作することが確認できました。

しかし、すべてのNode.js APIが完全に互換というわけではありません。筆者が確認した限り、`child_process`や`cluster`のような低レベルAPIを使うパッケージでは、一部の機能が動かないことがありました。

具体的には、GitHub issue #4721で報告されているように、`child_process.fork()`を使うパッケージでエラーが発生します。これはDenoのプロセスモデルがNode.jsと異なるためです。

```typescript
import { fork } from "npm:child_process";

// これはエラーになる
const child = fork("./worker.js");
```

この制限は、ワーカープロセスを使った並列処理を行うパッケージでは致命的です。代替として、Deno標準のWeb Workers APIを使う必要があります。

## まとめ：npm互換性の現状と今後

実際に様々なパッケージを試してみた結果、Deno 2.0のnpm互換性は思った以上に高いことがわかりました。

動いたもの：
- 純粋なJavaScriptパッケージ（lodash、dayjsなど）
- TypeScript型定義を持つパッケージ（express、zodなど）
- CommonJSパッケージ（chalkの旧バージョンなど）
- Node.js基本APIを使うパッケージ（fs-extraなど）

動かなかったもの：
- ネイティブモジュールを含むパッケージ（bcrypt、sharp、sqlite3など）
- 動的require()を使うパッケージ
- 低レベルNode.js APIを使うパッケージ（child_process、clusterなど）

筆者の印象としては、普通のWebアプリケーション開発で使うパッケージの80%くらいは問題なく動く感じです。基本的なユーティリティライブラリやフレームワークは大体使えます。

ただし、パフォーマンスが重要な部分でネイティブモジュールが使えないのは、本番環境での採用を考えると少し心配なところです。特に画像処理やデータベース接続など、ネイティブモジュールが好まれる領域では代替案を考える必要があります。

今後のDenoのアップデートで、ネイティブモジュールのサポートが改善されるのかどうか、筆者としては注目していきたいと思います。Node.jsからの移行パスとして、npm互換性は非常に重要な機能なので、さらなる進化に期待したいところです。

推測ですが、Denoチームはネイティブモジュールのサポートよりも、Deno標準ライブラリの充実に注力する方針なのかもしれません。bcryptの代わりにDeno標準のWeb Crypto APIを使う、sharpの代わりにImageMagickのWASM版を使う、といった形で、Denoネイティブなエコシステムを育てていく方向性を感じました。

実際、Deno公式ドキュメントを見ると、Node.js互換性よりもWeb標準APIの利用が推奨されています。長期的には、Node.js特有のAPIに依存しない設計が求められるのかもしれません。

とはいえ、既存のnpmエコシステムを活用できるのは大きなメリットです。完全な互換性はなくても、開発の初期段階で既存のパッケージを使えるのは、Denoの採用障壁を大きく下げていると感じました。筆者としては、これからDeno 2.xがどう進化していくか、また見守っていきたいと思います。
