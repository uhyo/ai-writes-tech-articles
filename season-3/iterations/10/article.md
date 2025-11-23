---
title: "Remix 2.0のdefer APIを試してストリーミング動作を調べる"
emoji: "🌊"
type: "tech"
topics: ["remix", "react", "typescript", "streaming"]
published: true
---

# Remix 2.0のdefer APIを試してストリーミング動作を調べる

皆さんこんにちは。Remix 2.0がリリースされて以降、**defer API**を使ったストリーミングレスポンスの実装が話題になっています。筆者も最近、自分のプロジェクトでこの機能を試す機会があったので、実際の挙動を調べてみました。

Remixの`defer`関数は、loaderから返すデータの一部を後回しにして、先にHTMLをレンダリングする仕組みです。これにより**Time to First Byte**（TTFB）を改善しつつ、重いデータフェッチを並行して実行できます。公式ドキュメントには基本的な使い方が載っています。ただ実際に**ストリーミング**がどう動作するのか、特にSuspenseとの相互作用については試してみないとわからない部分が多いです。細かい挙動を確認するには実際に手を動かす必要があります。

この記事では、簡単な例から始めて、複数のdeferを組み合わせた場合や、エラーハンドリングの挙動まで順を追って調べていきます。

:::message
この記事はRemix 2.0.1時点の挙動です。Remix 2.1以降では一部の動作が変更される可能性があります。
:::

## defer APIの基本的な使い方

まず、最もシンプルなケースから見ていきます。商品詳細ページを想定して、以下のようなloaderを書きました。基本情報はすぐに表示したいのでawaitしますが、レビューはPromiseのまま返します。

```typescript
// app/routes/products.$id.tsx
import { defer } from "@remix-run/node";
import { Await, useLoaderData } from "@remix-run/react";
import { Suspense } from "react";

export async function loader({ params }: LoaderFunctionArgs) {
  const productId = params.id;

  // すぐに返すデータ
  const basicInfo = await fetchBasicProductInfo(productId);

  // 時間がかかるデータはPromiseのまま返す
  const reviews = fetchProductReviews(productId);

  return defer({
    basicInfo,
    reviews, // Promiseをそのまま渡す
  });
}

export default function ProductPage() {
  const { basicInfo, reviews } = useLoaderData<typeof loader>();

  return (
    <div>
      <h1>{basicInfo.name}</h1>
      <p>{basicInfo.description}</p>

      <Suspense fallback={<div>レビューを読み込み中...</div>}>
        <Await resolve={reviews}>
          {(reviewsData) => (
            <div>
              {reviewsData.map(review => (
                <div key={review.id}>{review.comment}</div>
              ))}
            </div>
          )}
        </Await>
      </Suspense>
    </div>
  );
}
```

このコードを実行すると、まず商品の基本情報が表示され、その後にレビューが読み込まれます。ブラウザのDevToolsでネットワークタブを見ると、HTMLレスポンスがストリーミングで送られてくることが確認できました。最初のチャンクには商品情報とSuspenseのfallbackが含まれています。reviewsのPromiseが解決した後に2つ目のチャンクが送られてきます。

## ストリーミングの挙動を詳しく見る

ここからが面白いところです。実際にどのタイミングでデータが送られるのか、もっと詳しく調べてみました。タイミングを可視化するため、各処理にログを仕込んでいきます。まずは以下のコードで確認してみます。

```typescript
export async function loader({ params }: LoaderFunctionArgs) {
  const start = Date.now();
  console.log(`[${Date.now() - start}ms] Loader開始`);

  const basicInfo = await fetchBasicProductInfo(params.id);
  console.log(`[${Date.now() - start}ms] Basic info取得完了`);

  const reviewsPromise = fetchProductReviews(params.id).then(data => {
    console.log(`[${Date.now() - start}ms] Reviews取得完了`);
    return data;
  });

  const recommendationsPromise = fetchRecommendations(params.id).then(data => {
    console.log(`[${Date.now() - start}ms] Recommendations取得完了`);
    return data;
  });

  console.log(`[${Date.now() - start}ms] defer返却`);
  return defer({
    basicInfo,
    reviews: reviewsPromise,
    recommendations: recommendationsPromise,
  });
}
```

このコードを実行した結果、タイムラインは以下のようになりました。

```
[0ms] Loader開始
[150ms] Basic info取得完了
[152ms] defer返却
[HTMLの最初のチャンク送信]
[800ms] Reviews取得完了
[レビュー部分のチャンク送信]
[1200ms] Recommendations取得完了
[おすすめ商品部分のチャンク送信]
```

つまり、deferで返した複数のPromiseは並行して実行され、それぞれ解決したタイミングで個別にストリーミング送信されることがわかります。筆者は最初、すべてのPromiseが解決してから一度にまとめて送られるのかと思っていました。しかし実際には各Promiseが独立してストリーミングされていることが確認できました。

この挙動は結構重要です。例えばreviewsが800msで完了した時点で、recommendationsの完了を待たずにユーザーにレビューを見せることができます。従来のSSRでは最も遅いデータ取得を待つ必要がありましたが、deferを使えば段階的にコンテンツを配信できるわけです。特に複数の重いデータソースがある場合に効果的な仕組みだと感じました。

## Suspenseとの組み合わせパターン

次に、Suspenseの配置を変えた場合の挙動を試してみます。Reactの**Suspense**は複数の使い方ができるので、deferとの組み合わせでどう動作するかが気になりました。以下の2つのパターンで違いがあるかを確認しました。

パターン1: 各データに個別のSuspense

```typescript
<Suspense fallback={<div>レビュー読み込み中...</div>}>
  <Await resolve={reviews}>
    {(data) => <ReviewList reviews={data} />}
  </Await>
</Suspense>

<Suspense fallback={<div>おすすめ読み込み中...</div>}>
  <Await resolve={recommendations}>
    {(data) => <RecommendationList items={data} />}
  </Await>
</Suspense>
```

パターン2: 1つのSuspenseで複数Await

```typescript
<Suspense fallback={<div>データ読み込み中...</div>}>
  <Await resolve={reviews}>
    {(data) => <ReviewList reviews={data} />}
  </Await>
  <Await resolve={recommendations}>
    {(data) => <RecommendationList items={data} />}
  </Await>
</Suspense>
```

実際に動かしてみると、パターン1の方が段階的にUIが更新されて気持ち良いです。reviewsが先に解決した場合、そこだけ先に表示されます。recommendationsはまだfallback状態のままです。一方パターン2では、どちらか一方が解決してももう片方が解決するまでfallbackが表示され続けるので、ユーザー体験としては微妙でした。

ただし、パターン2にも使いどころはあります。例えば「商品情報とレビューの両方が揃ってから表示したい」みたいなケースでは有効です。実装の意図に応じて使い分けることになりそうです。

## 複数のdeferとエラーハンドリング

ここまで順調に動いているように見えましたが、エラーが発生した場合の挙動も気になります。実際のプロダクションではAPIが失敗することも十分あり得ますからね。試しに、reviewsのPromiseが失敗するケースを作ってみました。

```typescript
export async function loader({ params }: LoaderFunctionArgs) {
  const basicInfo = await fetchBasicProductInfo(params.id);

  const reviews = fetchProductReviews(params.id); // これが失敗すると仮定
  const recommendations = fetchRecommendations(params.id);

  return defer({
    basicInfo,
    reviews,
    recommendations,
  });
}
```

UIではAwaitコンポーネントに`errorElement`を渡すことでエラーをキャッチできます。

```typescript
<Suspense fallback={<div>読み込み中...</div>}>
  <Await
    resolve={reviews}
    errorElement={<div>レビューの読み込みに失敗しました</div>}
  >
    {(data) => <ReviewList reviews={data} />}
  </Await>
</Suspense>
```

この状態で実行すると、reviewsのPromiseが失敗しても、recommendationsは正常に読み込まれて表示されました。つまり、各deferされたPromiseのエラーは独立して扱われることがわかります。これは便利な挙動です。一部のデータ取得が失敗しても他の部分は正常に表示できます。

ただ、実際のプロダクトでは、エラーログの記録やリトライ処理をどう実装するかが課題になりそうです。筆者の場合、loaderの中でPromiseにcatchを仕掛けてエラーログを送る実装を試してみました。しかしそうするとエラーがキャッチされて正常系として扱われてしまいました。結局、エラーモニタリングはクライアント側で`errorElement`内に仕込むことになりそうです。まだベストプラクティスが固まっていない感じがあります。このあたりは今後のRemixコミュニティの知見に期待したいと思います。

:::details deferとawaitの組み合わせで遭遇したバグ

余談ですが、筆者が開発中に遭遇したバグがあります。loaderで以下のように書いていました。

```typescript
const reviews = await fetchProductReviews(params.id); // awaitしている
return defer({
  basicInfo,
  reviews, // これは既に解決済みの値
});
```

この場合、reviewsは既にPromiseではなく値なので、deferの恩恵が受けられません。でも、エラーにはならないんです。ただ、ストリーミングされずに最初のレスポンスに全部含まれてしまいます。気づくまで「なんでストリーミングされないんだ？」と悩んでいました。TypeScriptの型チェックでも検出されません。注意が必要です。

GitHubのissue #4830で同じ問題が報告されていました。将来的には型レベルで検出できるようになるかもしれません。
:::

## まとめ

Remix 2.0の**defer API**を使うと、Time to First Byteを改善しつつ段階的にUIを構築できることがわかりました。今回の検証で確認できた特徴は以下の通りです。

まず、複数のdeferされたPromiseは並行実行され、各々が解決したタイミングでストリーミングされることが確認できました。これによりユーザーは早く利用可能なコンテンツから順次見ることができます。またSuspenseの配置によってユーザー体験を細かく制御できる点も実用的です。個別にSuspenseを配置するか、まとめて配置するかで、UIの更新タイミングを調整できます。さらにエラーハンドリングが独立しているため、部分的な障害に強いのも利点です。

一方で、エラーログの記録やリトライ処理のベストプラクティスはまだ確立されていない印象です。筆者の環境では、loader内でのエラーキャッチとクライアント側のerrorElementの使い分けに少し悩みました。このあたりは実装者によって考え方が分かれそうですし、まだ明確な解が見えていない領域だと感じます。筆者としては、この機能が実際のプロダクションでどう使われていくのか、特にエラーハンドリングの部分がどう洗練されていくか、見守っていきたいと思います。

Next.jsのStreaming SSRと比較してどうなのかも気になるところですが、それはまた別の機会に試してみたいです。
