---
title: "Bun 1.0のNode.js互換性を試して限界を調べる"
emoji: "🥟"
type: "tech"
topics: ["bun", "nodejs", "javascript"]
published: true
---

皆さんこんにちは。2023年9月に**Bun 1.0**がリリースされ、「Node.jsのドロップイン代替」という触れ込みで大きな話題になりました。筆者も発表を見てすぐ試してみたのですが、実際にどこまでNode.jsと互換性があるのか、逆にどこで躓くのかを体系的に調べてみることにしました。

## Bunの互換性レイヤーの仕組み

Bunは内部的に**JavaScriptCore**エンジンを使っており、V8を使うNode.jsとは異なります。それにもかかわらず、BunはNode.jsの標準APIを独自に実装することで互換性を実現しています。

公式ドキュメントによると、`node:fs`、`node:path`、`node:http`といった主要なモジュールは実装されており、npmパッケージの大半が動作すると謳われています。しかし、「大半が動作する」というのは裏を返せば「一部は動かない」ということです。

筆者は実際にいくつかのケースを試して、どこまで互換性があるのか確かめてみました。

## 簡単なケース：基本的なAPIの互換性

まずは基本的なファイル操作から試します。

```javascript
import fs from 'node:fs';
import path from 'node:path';

const filePath = path.join(process.cwd(), 'test.txt');
fs.writeFileSync(filePath, 'Hello from Bun!');
const content = fs.readFileSync(filePath, 'utf-8');
console.log(content);
```

これを実行すると、何の問題もなく動作しました。`node:fs`と`node:path`は完全に実装されているようです。

次にHTTPサーバーを立ててみます。

```javascript
import http from 'node:http';

const server = http.createServer((req, res) => {
  res.writeHead(200, { 'Content-Type': 'text/plain' });
  res.end('Hello World\n');
});

server.listen(3000);
console.log('Server running at http://localhost:3000/');
```

これも期待通り動作します。Node.jsで書いた簡単なHTTPサーバーはBunでそのまま動くということです。

次に、実際のアプリケーションで使われるフレームワークを試してみます。Expressは最も人気のあるNode.jsフレームワークの一つです。

```javascript
import express from 'express';

const app = express();

app.get('/', (req, res) => {
  res.send('Hello from Express on Bun!');
});

app.listen(3000, () => {
  console.log('Express server running on port 3000');
});
```

Bunで実行してみると、これも問題なく動作しました。公式ドキュメントにも書かれている通り、Express、Koa、Honoといった主要なフレームワークは基本的に動作するようです。

ただし、ここで一つ気になったのは、Expressのミドルウェアの中には動かないものがあるかもしれないという点です。筆者が試した範囲では問題ありませんでしたが、より複雑なミドルウェアを使う場合は注意が必要かもしれません。

## 難しいケース：worker_threadsを試す

ここからが本題です。Node.jsの**worker_threads**モジュールは、マルチスレッド処理を実現するための重要な機能ですが、Bunでどこまで動くのでしょうか。

```javascript
import { Worker } from 'node:worker_threads';

const worker = new Worker(`
  const { parentPort } = require('worker_threads');
  parentPort.postMessage('Hello from worker!');
`, { eval: true });

worker.on('message', (msg) => {
  console.log('Received:', msg);
});
```

実行してみると、基本的な機能は動作しました。しかし、公式ドキュメントを見ると、`stdin`、`stdout`、`stderr`のオプションがサポートされていないことが分かります。

さらに調べてみると、GitHubのissue #901で`worker_threads`のサポート状況について議論されていました。基本的な機能は実装されているものの、一部のAPIが未実装であることが明記されています。

試しに、より複雑なワーカーを使ってみます。

```javascript
import { Worker } from 'node:worker_threads';
import path from 'node:path';

const workerPath = path.join(process.cwd(), 'worker.js');
const worker = new Worker(workerPath, {
  stdout: true // このオプションはBunでサポートされていない
});
```

残念ながら、これは期待通りには動作しませんでした。`stdout`オプションを使おうとすると、Bunは警告を出すか無視するようです。

### node:vmの限界

`node:vm`モジュールも試してみます。このモジュールは動的なコード実行に使われますが、セキュリティ上の理由で完全な実装は難しいと言われています。

```javascript
import vm from 'node:vm';

const context = { x: 10, y: 20 };
vm.createContext(context);

const code = 'x + y';
const result = vm.runInContext(code, context);
console.log(result);
```

実行すると、`30`という結果が得られました。基本的な`vm.runInContext`は動作するようです。

しかし、より高度な機能はどうでしょうか。`vm.Module`や`vm.SourceTextModule`といったES Modulesを扱う機能を試してみます。

```javascript
import vm from 'node:vm';

const module = new vm.SourceTextModule('export const value = 42;');
```

これを実行すると、エラーが発生しました。なんと、Bun 1.0時点では`vm.SourceTextModule`が実装されていないようです。公式ドキュメントにも「experimental features are not implemented」と記載されています。

筆者としては、この部分は今後のアップデートで改善されることを期待しています。

### node:clusterと完全に動かなかったケース

`node:cluster`モジュールは、複数のワーカープロセスを起動してCPUコアを最大限活用するための機能です。

```javascript
import cluster from 'node:cluster';
import http from 'node:http';
import { cpus } from 'node:os';

if (cluster.isPrimary) {
  const numCPUs = cpus().length;
  for (let i = 0; i < numCPUs; i++) {
    cluster.fork();
  }
} else {
  http.createServer((req, res) => {
    res.end('Hello from worker!');
  }).listen(3000);
}
```

Bun 1.0リリース時点では、`node:cluster`は実装されていませんでした。しかし、調べてみると、Bun 1.1.25（2024年8月リリース）で追加されたようです。

:::message
この記事はBun 1.0時点の挙動を基にしています。Bun 1.1以降では機能が追加・改善されている可能性があります。
:::

筆者がBun 1.0を試した時点では、`node:cluster`を使おうとするとエラーが発生しましたが、現在のバージョンでは動作するかもしれません。まだ試してないけど、気になるところです。

また、`node:http2`のサーバー機能も完全には動作しませんでした。クライアントはサポートされていますが、サーバーは未実装です。

```javascript
import http2 from 'node:http2';

const server = http2.createSecureServer({
  // 設定...
});
```

これを実行すると、エラーが発生します。HTTP/2サーバーを使いたい場合は、現時点ではNode.jsを使うしかありません。

さらに、`node:v8`モジュールも部分的にしか実装されていません。V8エンジン固有の機能（**ヒープスナップショット**など）は、JavaScriptCoreを使うBunでは提供できないのです。

## 実践での課題

実際のプロジェクトでは、npmパッケージを大量に使うことになります。Bunがどれだけパッケージエコシステムと相性が良いかを確認してみました。

筆者は自分のプロジェクト（TypeScript + Express + PostgreSQL構成）でBunを試してみたのですが、いくつかの問題に遭遇しました。特に、**ネイティブモジュール**を使うパッケージ（例：`bcrypt`）は動作しないケースがありました。

```javascript
import bcrypt from 'bcrypt';

const hash = await bcrypt.hash('password', 10);
```

これを実行すると、ネイティブバインディングの読み込みに失敗しました。Bunは独自のモジュール解決システムを使っているため、Node.jsのネイティブアドオンをそのまま読み込むことができないのです。

代わりに`bcryptjs`（純粋なJavaScript実装）を使うと問題なく動作しました。ネイティブモジュールに依存しているパッケージを使う場合は、代替手段を探す必要があります。

### パフォーマンス

互換性だけでなく、パフォーマンスも重要です。簡単なベンチマークを取ってみました。

```javascript
const start = performance.now();

for (let i = 0; i < 1000000; i++) {
  JSON.parse('{"key": "value"}');
}

const end = performance.now();
console.log(`Time: ${end - start}ms`);
```

Node.js 18で実行すると約420msでしたが、Bun 1.0では約280msという結果になりました。確かにBunの方が速いようです。

ただし、これは単純な処理の例であり、実際のアプリケーションでどれだけ速度差が出るかは、使用するパッケージやワークロードに依存します。もっと複雑なケースでの検証も必要でしょう。

### 開発者体験

技術的な互換性とは別に、開発者体験も重要です。Bunを使ってみて感じたのは、エラーメッセージが分かりやすいということです。

Node.jsでは動くがBunでは動かない機能を使おうとすると、Bunは明確なエラーメッセージを出してくれます。「この機能はまだ実装されていません」といった形で、何が問題なのかが分かりやすいです。

一方で、ドキュメントはまだ発展途上という印象を受けました。どのAPIがサポートされていて、どれがサポートされていないのか、公式ドキュメントを見ても完全には分からないケースがありました。GitHubのissueを見に行く必要があることも多く、この点は今後の改善に期待したいところです。

## まとめと今後の展望

Bun 1.0のNode.js互換性を試してみた結果、以下のことが分かりました。

基本的なNode.js APIは概ね動作します。`node:fs`、`node:path`、`node:http`といったコアモジュールは問題なく使えますし、Expressのようなフレームワークもほぼそのまま動きます。

しかし、高度な機能（`worker_threads`の一部オプション、`node:cluster`、`node:http2`サーバー、`node:vm`の実験的機能など）には制限があります。また、ネイティブモジュールを使うパッケージは動作しないケースがあります。

筆者としては、Bun 1.0は「完全な代替」というよりも「多くのケースで使える高速な選択肢」という位置づけだと感じました。新規プロジェクトであれば、Bunの制限を理解した上で採用するのは良い選択かもしれません。一方、既存のNode.jsプロジェクトを移行する場合は、依存パッケージとの相性を慎重に確認する必要があるでしょう。

Bunはまだ1.0がリリースされたばかりです。今後のバージョンアップで互換性が向上していくことが期待されますし、実際にBun 1.1以降では多くの改善が加えられているようです。筆者としても、これからどこまで進化していくのか見守っていきたいと思います。
