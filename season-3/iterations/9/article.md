---
title: "Astro 4.0のView Transitionsを試して限界を知る"
emoji: "🚀"
type: "tech"
topics: ["astro", "viewtransitions", "javascript"]
published: true
---

## はじめに

皆さんこんにちは。Astro 4.0がリリースされ、ネイティブの**View Transitions API**サポートが追加されました。ページ遷移時にスムーズなアニメーションを実装できるこの機能、筆者は以前から気になっていたので、実際に試してみることにしました。

View Transitions APIはもともとChrome 111で追加されたブラウザ標準の機能です。Astroでは従来、独自の**クライアントサイドルーティング**で似たような効果を実現していましたが、4.0からはネイティブAPIを直接活用できるようになっています。このアプローチにより、JavaScriptの実装量を減らしつつ、ブラウザネイティブの最適化を活用できます。

https://github.com/withastro/astro/pull/8711

この記事では、簡単な例から始めて、徐々に複雑なケースを試していきます。

## 基本的なページ遷移

まずは一番シンプルな例から試してみました。Astro 4.0で新規プロジェクトを作成し、2つのページを用意します。

```typescript
// src/pages/index.astro
---
import Layout from '../layouts/Layout.astro';
---
<Layout title="ホーム">
  <h1>ホームページ</h1>
  <a href="/about">アバウトへ</a>
</Layout>
```

```typescript
// src/pages/about.astro
---
import Layout from '../layouts/Layout.astro';
---
<Layout title="アバウト">
  <h1>アバウトページ</h1>
  <a href="/">ホームへ</a>
</Layout>
```

レイアウトファイルでView Transitionsを有効にします。

```typescript
// src/layouts/Layout.astro
---
import { ViewTransitions } from 'astro:transitions';
---
<html>
  <head>
    <ViewTransitions />
  </head>
  <body>
    <slot />
  </body>
</html>
```

これだけで基本的なフェードイン・フェードアウトのトランジションが動作します。実際に動かしてみると、ページ遷移時にコンテンツが滑らかにフェードしました。個人的にはちょっとびっくりしたのですが、設定ファイルとか何も触らなくても動くんですね。

ブラウザの開発者ツールでネットワークタブを確認すると、ページ遷移時に新しいページのHTMLがフェッチされていることが分かります。つまり、見た目はSPAライクですが、内部的には各ページのHTMLを取得して表示しています。この仕組みにより、SEOやパフォーマンスの利点を保ちながら、スムーズなUXを実現できます。

:::message
この記事はAstro 4.0時点の挙動です。Astro 5.0では挙動が変わる可能性があります。
:::

## 名前付きトランジション

次に、特定の要素に個別のアニメーションを適用してみます。Astroでは`transition:name`属性で要素を識別できます。

```typescript
// src/pages/index.astro
<Layout title="ホーム">
  <h1 transition:name="title">ホームページ</h1>
  <img src="/logo.png" transition:name="logo" />
  <a href="/about">アバウトへ</a>
</Layout>
```

```typescript
// src/pages/about.astro
<Layout title="アバウト">
  <h1 transition:name="title">アバウトページ</h1>
  <img src="/logo.png" transition:name="logo" />
  <a href="/">ホームへ</a>
</Layout>
```

同じ`transition:name`を持つ要素間で**モーフィング**アニメーションが適用されます。実行すると、タイトルとロゴがそれぞれ滑らかに位置移動しました。この動きは、古い要素と新しい要素を自動的に補間して、視覚的に連続したアニメーションを生成する仕組みです。

しかし、ここで問題が発生しました。ロゴ画像のサイズがページ間で異なる場合、アニメーションが不自然になります。筆者はホームページで200pxのロゴ、アバウトページで100pxのロゴを配置してみたのですが、縮小アニメーションがぎこちなくなってしまいました。サイズ差が大きいと、途中のフレームで歪んだ状態が見えてしまうんですね。

## トランジションのカスタマイズ

Astroでは`transition:animate`属性でアニメーションタイプを指定できます。組み込みのオプションとして、`fade`、`slide`、`none`などがあります。

```typescript
<h1 transition:name="title" transition:animate="slide">
  ホームページ
</h1>
```

これを試すと、タイトルがスライドしながら遷移しました。ただ、個人的には`slide`の動きがやや硬い印象を受けたので、カスタムアニメーションを定義してみることにしました。デフォルトのアニメーションはシンプルですが、もう少し繊細な動きが欲しい場合もあります。

カスタムアニメーションはCSSで定義します。`::view-transition-old()`と`::view-transition-new()`という疑似要素を使って、遷移前後の要素に個別のアニメーションを指定できます。

```css
@keyframes custom-slide {
  from {
    opacity: 0;
    transform: translateY(-20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

::view-transition-old(title) {
  animation: custom-slide 0.3s ease-out reverse;
}

::view-transition-new(title) {
  animation: custom-slide 0.3s ease-out;
}
```

これで、タイトルが上から滑らかにスライドインするアニメーションが実現できました。しかし、ここからが本題です。

## ブラウザ互換性とフォールバック

View Transitions APIはChrome系ブラウザでは動作しますが、Firefoxではまだサポートされていません（2024年時点）。筆者が確認したところ、Firefoxで開くとトランジションは発生せず、通常のページ遷移になりました。Safariでも同様に、標準のページ遷移にフォールバックされます。ブラウザ互換性は徐々に改善されていくと思われますが、現時点では限定的です。

Astroのドキュメントでは、**フォールバック**オプションで非対応ブラウザの動作を制御できると書かれています。

```typescript
<ViewTransitions fallback="swap" />
```

`fallback`には`animate`と`swap`が指定できます。`animate`はJavaScriptでトランジションをシミュレートし、`swap`は即座にコンテンツを切り替えます。この設定により、非対応ブラウザでもある程度の体験を提供できます。

しかし、`animate`を試してみたところ、筆者の環境では期待通りに動作しませんでした。残念ながら、Firefoxでもスムーズなアニメーションを実現するには、別のアプローチが必要そうです。現状では、非対応ブラウザについては通常のページ遷移を許容するのが現実的だと感じました。

## パフォーマンスの問題

View Transitionsを有効にすると、Astroはページ遷移時にクライアントサイドルーティングを行います。つまり、JavaScriptで次のページのHTMLをフェッチし、DOMを更新します。このプロセスは通常は高速ですが、ページのサイズや複雑さによっては遅延が発生する可能性があります。

この挙動が問題になるケースがありました。筆者は大量の画像を含むページ（50枚以上のギャラリー）で試したのですが、遷移時に一瞬フリーズするような挙動が発生しました。おそらく、新しいページのHTMLをパースしてDOMに反映する処理が重いのだと思います。画像の遅延読み込みを実装していても、DOMツリー自体が大きいと処理負荷が高くなります。

この問題に対して、特定のリンクだけView Transitionsを無効にすることもできます。

```typescript
<a href="/gallery" data-astro-reload>ギャラリーへ</a>
```

`data-astro-reload`属性を付けると、そのリンクは通常のページ遷移になります。ただ、これだと一部のリンクだけアニメーションがないという不統一な体験になってしまうので、デザイン的には悩ましいところです。ユーザー体験の一貫性を考えると、すべてのページでView Transitionsを使うか、すべてで使わないかのどちらかに統一したほうが良いかもしれません。

## プリフェッチとの組み合わせ

Astroには`<link rel="prefetch">`でページを**プリフェッチ**する機能があります。View Transitionsと組み合わせると、遷移が非常に高速になるはずです。事前にHTMLをキャッシュしておくことで、クリック時の待ち時間をほぼゼロにできます。

```typescript
<a href="/about" rel="prefetch">アバウトへ</a>
```

実際に試してみたところ、確かに遷移が速くなりました。しかし、プリフェッチが走るタイミングが予測しにくく、ユーザーがリンクにホバーした瞬間にフェッチが始まるわけではないようです。推測ですが、Intersection Observerでリンクが画面内に入ったタイミングでプリフェッチしているのかもしれません。実装見てないので確証はありませんが。

プリフェッチの挙動をもう少し調べてみると、ネットワークタブで確認できることが分かりました。リンクがビューポートに入った時点でHTMLがバックグラウンドで取得されています。このアプローチは、ユーザーがクリックする可能性の高いリンクを先読みする戦略です。

## SPAモードとの違い

Astro 4.0では、完全にSPAとして動作する`output: 'spa'`モードも用意されています。View TransitionsとSPAモードの違いがよくわからなかったので、両方試してみました。

SPAモードでは、すべてのページがクライアントサイドルーティングで処理されます。一方、View Transitionsは基本的にMPAのまま、遷移時だけクライアントサイドルーティングを使います。このハイブリッドアプローチにより、両方の利点を活かせます。

パフォーマンス的には、SPAモードの方が初回ロード後の遷移が速いですが、初回ロード自体は重くなります。View Transitionsは初回ロードが軽く、遷移もそこそこ速いという中間的な位置づけです。JavaScript Bundleのサイズも、SPAモードより小さく抑えられます。

実際に両方のモードでビルドサイズを比較してみました。SPAモードでは約150KBのJavaScriptが生成されたのに対し、View Transitionsを使ったMPAモードでは約80KBに収まりました。この差は、ルーティングライブラリの有無によるものです。

筆者としては、コンテンツ中心のサイトならView Transitionsで十分だと感じました。アプリケーションライクなUIが必要ならSPAモードの方が適していると思います。ただし、SEOを重視する場合は、MPAベースのView Transitionsが有利です。

## まとめ

Astro 4.0のView Transitionsを一通り試してみました。簡単な設定で滑らかなページ遷移を実現できるのは魅力的ですが、ブラウザ互換性やパフォーマンスの問題も存在します。ネイティブAPIを活用する設計は理にかなっていますが、現時点では制約もあります。

特に、Firefoxでのサポートがまだないのは痛いところです。また、大量のコンテンツを含むページではパフォーマンス問題が発生する可能性があるので、実際のプロジェクトに導入する際は注意が必要です。適用範囲を限定したり、重いページでは無効化するなどの工夫が求められます。

個人的には、View Transitions APIがもっと普及すれば、Web全体のUXが向上すると期待しています。ただし、現状ではChrome系ブラウザに限定される点が課題です。

筆者としては、この機能がどう進化していくか、また見守っていきたいと思います。特に、Firefoxのサポートが追加されるタイミングで、もう一度試してみたいですね。ブラウザ対応が進めば、もっと積極的に導入できるようになるはずです。
