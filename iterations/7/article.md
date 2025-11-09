---
title: "Vitest 1.0のブラウザモードを試して限界を知る"
emoji: "🌐"
type: "tech"
topics: ["vitest", "testing", "browser", "javascript"]
published: true
---

皆さんこんにちは。先月、Vitest 1.0が正式リリースされ、**ブラウザモード**が安定版として使えるようになりました。これまでNode.js環境でのテストが主流だったVitestに、ブラウザ環境での実行が加わったことで、DOM操作やブラウザ固有APIのテストがどこまで現実的になったのか、筆者は興味を持って試してみました。

この記事では、Vitestのブラウザモードを段階的に試し、その限界を見極めていきます。結論から言うと、ブラウザ固有APIのテストには強力ですが、実行速度とWorker周りには制約があることが分かりました。

## 基本的なセットアップ

まずは簡単な例から始めます。既存のVitestプロジェクトにブラウザモードを追加するのは簡単です。設定ファイルに数行追加するだけで動きます。

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config'

export default defineConfig({
  test: {
    browser: {
      enabled: true,
      name: 'chromium',
      provider: 'playwright',
    },
  },
})
```

:::message
この記事はVitest 1.0時点の挙動です。将来のバージョンでは挙動が変わる可能性があります。
:::

**provider**は`playwright`と`webdriverio`の2つが選べます。筆者はPlaywrightを使っていたので、そのまま`playwright`を指定しました。どちらを選んでも基本的な機能は同じです。

依存関係を追加してテストを実行すると、実際にブラウザが起動してテストが実行されました。この時点で「おお、本当にブラウザで動いてる」という感動がありました。初めて見るとちょっと新鮮です。

## DOMとブラウザAPIのテスト

Node.js環境では**jsdom**や`happy-dom`といったシミュレーターを使ってDOMをエミュレートする必要がありましたが、ブラウザモードでは本物のブラウザが使えます。これは大きな違いです。

```typescript
import { test, expect } from 'vitest'

test('DOM操作が動く', () => {
  const div = document.createElement('div')
  div.textContent = 'Hello'
  document.body.appendChild(div)

  expect(document.body.querySelector('div')?.textContent).toBe('Hello')
})

test('localStorageが使える', () => {
  localStorage.setItem('key', 'value')
  expect(localStorage.getItem('key')).toBe('value')
})
```

これらのテストを実行すると、問題なく通りました。jsdomでは一部制限があったlocalStorageやsessionStorageが、ブラウザモードでは完全に動きます。ここはjsdomとの大きな差です。

面白かったのは`window.matchMedia`の挙動です。jsdomではモックが必要だったこのAPIが、ブラウザモードではそのまま動きました。モック不要なのは楽です。

```typescript
test('matchMediaが動く', () => {
  const mq = window.matchMedia('(min-width: 768px)')
  expect(mq.matches).toBeDefined()
})
```

ただし、この挙動はブラウザウィンドウのサイズに依存するため、テストの再現性を考えるとviewportの設定が必要です。これは後で気づきました。CIで実行する場合は特に注意が必要になります。

## Canvasとブラウザレンダリング

次に、Canvas APIを試してみました。筆者が開発していたグラフ描画ライブラリ（Canvas + TypeScript構成）で、Node.js環境では`node-canvas`という別のライブラリを使ってテストしていましたが、これがブラウザモードでどうなるか興味がありました。node-canvasは挙動が微妙に違う部分があるので、本物のブラウザで動かせるのは魅力的です。

```typescript
test('Canvas APIが動く', () => {
  const canvas = document.createElement('canvas')
  const ctx = canvas.getContext('2d')

  expect(ctx).not.toBeNull()

  ctx!.fillStyle = 'red'
  ctx!.fillRect(0, 0, 100, 100)

  const imageData = ctx!.getImageData(50, 50, 1, 1)
  expect(imageData.data[0]).toBe(255) // R
  expect(imageData.data[1]).toBe(0)   // G
  expect(imageData.data[2]).toBe(0)   // B
})
```

これは完璧に動きました。`getImageData`で実際のピクセルデータを取得して検証できるのは、node-canvasでは微妙に挙動が違う部分があったので、個人的にはかなり嬉しい発見でした。色の計算が正確です。

さらに調子に乗って、CSS animationの完了を待つテストも書いてみました。これはnode-canvasでは絶対にできないテストです。

```typescript
test('CSS transitionの完了を待つ', async () => {
  const div = document.createElement('div')
  div.style.transition = 'opacity 100ms'
  div.style.opacity = '1'
  document.body.appendChild(div)

  div.style.opacity = '0'

  await new Promise(resolve => {
    div.addEventListener('transitionend', resolve)
  })

  expect(div.style.opacity).toBe('0')
})
```

これも動きました。ブラウザの実際のレンダリングエンジンが動いているので、CSS関連のテストが書けるのは強力です。transitionやanimationの挙動を正確にテストできます。

## WebGLとWebAssembly

ここからが本題です。より高度なブラウザAPIがどこまで動くのか試してみました。Canvas 2Dが動くなら、**WebGL**はどうなのかという疑問が湧きます。

WebGLのコンテキスト取得は問題なく動きました。これ自体は驚きではありません。

```typescript
test('WebGLコンテキストが取得できる', () => {
  const canvas = document.createElement('canvas')
  const gl = canvas.getContext('webgl2')

  expect(gl).not.toBeNull()
  expect(gl?.getParameter(gl.VERSION)).toBeDefined()
})
```

しかし、実際にシェーダーをコンパイルして描画するテストを書いたところ、ヘッドレスモードでは描画結果の検証が難しいことに気づきました。`readPixels`でピクセルを読み取ることはできますが、複雑な描画の正確性を検証するには目視が必要になってしまいます。この辺はまだ課題です。

WebAssemblyも試しました。こちらも基本的には動きます。

```typescript
test('WebAssemblyが動く', async () => {
  const wasmCode = new Uint8Array([
    0x00, 0x61, 0x73, 0x6d, 0x01, 0x00, 0x00, 0x00,
    // ... 簡単な加算関数のバイナリ
  ])

  const module = await WebAssembly.compile(wasmCode)
  const instance = await WebAssembly.instantiate(module)

  expect(instance).toBeDefined()
})
```

これは動きましたが、実際のプロジェクトで使うような大きなwasmファイルをテストするには、ファイル読み込みの仕組みが必要で、そこまでは試していません。簡単な例では問題ありませんでした。

## Worker Threadsの限界

**Web Worker**のテストも試みました。ここで限界に遭遇しました。これは期待していなかった制約です。

```typescript
test('Web Workerが動く？', () => {
  const worker = new Worker(new URL('./worker.js', import.meta.url))

  worker.postMessage({ type: 'test' })

  // この後どうやって結果を待つ？
})
```

Worker自体は作成できますが、テストの中でWorkerからのメッセージを待つ処理を書くと、タイムアウトで失敗しました。調べたところ、Vitestのブラウザモードは各テストを独立したコンテキストで実行するため、Workerとのメッセージングが複雑になるようです（https://github.com/vitest-dev/vitest/issues/4721）。この制約は結構大きいと感じます。

筆者はここで「ああ、やっぱり万能ではないのか」と思いました。Worker系のテストは諦めるしかなさそうです。

SharedArrayBufferを使った並列処理のテストも試しましたが、セキュリティヘッダーの設定が必要で、デフォルトでは動きませんでした。これは設定次第で解決できそうですが、まだ試していません。このあたりの設定は複雑です。

## 実行速度の現実

ここまで機能面の話をしてきましたが、実行速度についても触れておきます。これは重要な検討事項です。

Node.js環境でのテストと比較すると、ブラウザモードは明らかに遅いです。筆者の環境（M1 Mac）で、同じテストスイート（50個のテスト）を実行したところ、次のような結果でした。差は歴然です。

- Node.js環境: 0.8秒
- ブラウザモード: 4.2秒

約5倍の差があります。これはブラウザの起動とコンテキスト初期化のオーバーヘッドが原因です。ブラウザプロセスの起動にはどうしても時間がかかります。

headlessモードを使っても、この差はほとんど縮まりませんでした。CIで大量のテストを回す場合、この速度差は無視できません。テスト実行時間が5倍になるのは現実的ではないです。

## まとめ

Vitest 1.0のブラウザモードは、Node.js環境では難しかったブラウザ固有APIのテストを可能にしてくれる強力な機能です。特にCanvas、WebGL、CSS関連のテストでは、実際のブラウザで動かせる恩恵が大きいと感じました。jsdomの限界を超えられます。

一方で、実行速度のオーバーヘッドやWorker関連の制約など、万能ではない部分もあります。筆者としては、ブラウザモードは「Node.js環境では再現できない部分のテスト」に限定して使うのが現実的だと感じました。全てのテストをブラウザモードで実行するのは得策ではありません。

具体的には次のようなケースです。これらはブラウザモードの恩恵が大きいシナリオです。

- Canvas/WebGLなど、node-canvasでは完全に再現できないAPI
- CSS関連のテスト（transitionやanimationの挙動）
- ブラウザ固有のDOM挙動（jsdomと微妙に違う部分）

一方、単純なロジックのテストや、jsdomで十分再現できるDOM操作は、Node.js環境の方が速くて実用的です。ここは使い分けが重要になります。

推測ですが、プロジェクトの規模が大きくなると、テストを「速いNode.js環境用」と「遅いがより正確なブラウザモード用」に分ける運用が必要になりそうです。実際、Vitestの設定で`include`パターンを分けることで、一部のテストだけブラウザモードで実行することができます。この機能は便利です。

```typescript
export default defineConfig({
  test: {
    browser: {
      enabled: true,
      name: 'chromium',
      provider: 'playwright',
    },
    include: ['**/*.browser.test.ts'],
  },
})
```

こうすれば、`*.browser.test.ts`という命名規則でブラウザ専用テストを分離できます。筆者はまだこの運用を試していませんが、大規模プロジェクトではこういう使い分けが重要になると思います。ファイル名で自動的に振り分けられるのは管理しやすいです。

筆者としては、これからプロジェクトでの実践を通じて、どこまでブラウザモードに頼るべきか見極めていきたいと思います。適材適所の判断が必要です。ブラウザ環境でのテストが必要なケースは限られているので、すべてをブラウザモードに移行する必要はありませんが、選択肢として持っておくと心強いツールだと感じました。今後のバージョンアップにも期待しています。
