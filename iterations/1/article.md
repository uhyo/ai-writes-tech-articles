---
title: "React 19のuseフックとServer Componentsの実践的な使い分け"
emoji: "🎣"
type: "tech"
topics: ["react", "typescript", "servercomponents"]
published: true
---

皆さんこんにちは。React 19のCanary版で**useフック**という新しいフックが登場し、Server Componentsとの組み合わせが話題になっています。

筆者も最近、Server Componentsでの非同期データ処理について考える機会があったのですが、useフックはこの領域において興味深い位置づけにあるようです。従来のuseEffectやuseStateとは異なり、Promiseを直接扱える点が特徴的です。

:::message
この記事はReact 19 Canary版（2024年11月時点）の挙動に基づいています。正式リリース時には変更される可能性があります。
:::

本記事では、useフックの基本的な仕組みから、Server Componentsとの使い分けまで、実践的なパターンを探っていきます。

## useフックとServer Componentsの関係性

useフックは、**Promise**や**Context**を引数に取り、その値を解決して返すフックです。Promiseを渡した場合、そのPromiseがresolveされるまでコンポーネントはSuspendされます。

```tsx
import { use } from 'react';

function UserProfile({ userPromise }: { userPromise: Promise<User> }) {
  const user = use(userPromise);

  return <div>{user.name}</div>;
}
```

このコードを実行すると、`userPromise`が解決されるまでコンポーネントは表示されず、最も近い**Suspense境界**でfallbackが表示されるはずです。

従来、非同期データを扱う場合はuseEffectとuseStateの組み合わせが一般的でした。しかし、この方法だと初回レンダリング時にローディング状態を自分で管理する必要があります。useフックを使えば、Suspenseの仕組みに乗せられるため、コードがシンプルになると考えられます。

個人的には、この「Promiseを直接扱える」という点が新鮮でした。

一方で、Server Componentsの存在も重要です。Server Componentsは、サーバー側で実行されるコンポーネントであり、非同期関数として定義できます。

```tsx
// Server Component
async function UserProfile({ userId }: { userId: string }) {
  const user = await fetchUser(userId);

  return <div>{user.name}</div>;
}
```

このように、Server ComponentsではコンポーネントをAsync Functionとして定義し、直接awaitが使えます。これは非常に直感的です。

しかし、Client Componentsでは非同期コンポーネントは使えません。ここでuseフックの出番となります。useフックは、Client ComponentsでもPromiseを扱えるようにするための仕組みと言えます。

筆者はこの設計について、「なぜServer Componentsではasync/awaitが使えるのに、Client Componentsではuseフックが必要なのか？」と疑問に思っていました。これは、クライアント側では再レンダリングの概念があり、非同期処理をフックのライフサイクルに統合する必要があるためと考えられます。Server Componentsは1回しか実行されないため、async/awaitで十分なわけです。

この違いを理解することが、使い分けの第一歩になります。

## 簡単な使用例から始める

まずは、useフックの基本的な使い方を見ていきます。

```tsx
'use client';

import { use, Suspense } from 'react';

function Comments({ commentsPromise }: { commentsPromise: Promise<Comment[]> }) {
  const comments = use(commentsPromise);

  return (
    <ul>
      {comments.map(comment => (
        <li key={comment.id}>{comment.text}</li>
      ))}
    </ul>
  );
}

function CommentsSection() {
  const commentsPromise = fetchComments();

  return (
    <Suspense fallback={<div>読み込み中...</div>}>
      <Comments commentsPromise={commentsPromise} />
    </Suspense>
  );
}
```

このコードでは、`fetchComments()`が返すPromiseを`Comments`コンポーネントに渡し、その中でuseフックを使って値を取り出しています。

重要なのは、Promiseの生成とその消費が分離されている点です。`CommentsSection`でPromiseを作り、`Comments`で値を取り出す。この分離により、複数のコンポーネントで同じPromiseを共有することも可能になるはずです。

ただし、ここで気をつけるべきは、`fetchComments()`が毎回新しいPromiseを生成してしまうと、再レンダリングのたびにfetchが走る可能性があることです。推測ですが、useMemoなどでPromiseをメモ化する必要があるケースもあるかもしれません。

```tsx
function CommentsSection() {
  const commentsPromise = useMemo(() => fetchComments(), []);

  return (
    <Suspense fallback={<div>読み込み中...</div>}>
      <Comments commentsPromise={commentsPromise} />
    </Suspense>
  );
}
```

このようにuseMemoで囲むことで、Promiseが再生成されるのを防げるはずです。ただ、依存配列の管理が必要になるため、このあたりのベストプラクティスはまだ確立されていないように思います。

## より複雑なパターンを試す

次に、条件分岐を含むパターンを見てみます。

```tsx
function UserContent({ userId }: { userId: string | null }) {
  if (userId === null) {
    return <div>ログインしてください</div>;
  }

  const userPromise = fetchUser(userId);
  const user = use(userPromise);

  return <div>こんにちは、{user.name}さん</div>;
}
```

useフックは、条件分岐の中でも呼び出せる点が興味深いです。従来のフックの「フックは条件分岐の外で呼ぶべき」というルールとは異なる挙動となります。

Reactの公式ドキュメントによれば、useフックは条件分岐やループの中でも使用可能とされています。これは、useフックが内部的に異なる仕組みで実装されているためのようです。

筆者はここの挙動が一番驚きでした。通常のフックとは異なるルールを持つという点で、useフックは特殊な位置づけにあると言えます。

もう一つ、エラーハンドリングのパターンも見ておきます。

```tsx
function UserContent({ userPromise }: { userPromise: Promise<User> }) {
  const user = use(userPromise);

  return <div>{user.name}</div>;
}

function UserPage() {
  return (
    <ErrorBoundary fallback={<div>エラーが発生しました</div>}>
      <Suspense fallback={<div>読み込み中...</div>}>
        <UserContent userPromise={fetchUser()} />
      </Suspense>
    </ErrorBoundary>
  );
}
```

Promiseがrejectされた場合、Error Boundaryでキャッチされるはずです。これはSuspenseと同様の仕組みを使っていると考えられます。

:::details Contextでのuse使用について

useフックはPromiseだけでなく、Contextも引数に取れます。

```tsx
const theme = use(ThemeContext);
```

これはuseContextの代替として機能しますが、条件分岐の中でも使える点が異なります。ただし、筆者はまだこのパターンを実際の開発で使ったことがないため、実用性については評価できていません。

:::

## 実践的な使い分けの指針

では、useフックとServer Componentsをどう使い分けるべきでしょうか。筆者なりの考えをまとめてみます。

### Server Componentsを使うケース

以下のような場合は、Server Componentsで非同期処理を行うのが適切だと考えられます。

- 初期表示に必要なデータを取得する場合
- データベースやAPIへの直接アクセスが必要な場合
- SEOが重要なコンテンツの場合
- 機密情報を含むロジックを実行する場合

Server Componentsはサーバー側で実行されるため、データベースへの直接アクセスや環境変数の使用が安全に行えます。また、生成されたHTMLがクライアントに送られるため、SEOにも有利です。

パフォーマンス面でも、Server Componentsは初期表示が速いという利点があります。データ取得がサーバー側で完了してからHTMLが送られるため、クライアント側での待ち時間が短くなります。

### useフックを使うケース

一方、以下のケースではClient Componentsでuseフックを使うのが適しているはずです。

- ユーザーインタラクション後のデータ取得
- クライアント側でのみ利用可能なAPIを使う場合
- リアルタイムでデータを更新する必要がある場合
- ブラウザのローカルストレージなどを使う場合

例えば、ボタンをクリックしたらコメントを読み込む、といったケースではClient Componentが必要になります。この場合、useフックを使ってPromiseを処理するのが自然な実装となるでしょう。

また、WebSocketやServer-Sent Eventsのようなリアルタイム通信も、Client Componentsの領域です。これらはブラウザ固有のAPIを使うため、Server Componentsでは扱えません。

筆者としては、Server Componentsをデフォルトとし、インタラクティブな処理が必要な部分のみClient Componentsとuseフックを使う、という方針が良さそうに思います。

## まだ見えない可能性

useフックはまだ新しい機能であり、ベストプラクティスが確立されているとは言えません。

例えば、エラーハンドリングについてはまだ不明な点が多いです。useフックで処理中のPromiseがrejectされた場合、Error Boundaryでキャッチされるはずですが、その際の挙動やリトライ戦略については、実際のプロジェクトで試行錯誤が必要になると考えられます。

また、Server ComponentsとClient Componentsの境界でのデータの受け渡しについても、Promiseをpropsとして渡すパターンが推奨されるのか、それとも別の方法が確立されていくのか、まだ見守る必要があります。Promiseを直接propsで渡すのは少し不思議な感覚がありますが、これがReact 19の新しいパラダイムなのかもしれません。

React issuesでも、useフックの使い方やパフォーマンスについて活発に議論されているようです。今後、コミュニティからより実践的なパターンが出てくるのではないかと期待しています。

個人的に気になっているのは、Promiseのキャッシュ戦略です。同じデータを複数のコンポーネントで使う場合、Promiseをどう共有するのがベストなのか、まだ明確な答えがありません。状態管理ライブラリとの統合も含めて、今後の発展が楽しみな領域です。

筆者としては、まだ試していないパターンも多いため、実際のプロジェクトでの経験を積みながら、この新しいフックの可能性を探っていきたいと思います。

React 19のuseフックは、Client ComponentsでPromiseを扱うための新しい手段を提供します。Server Componentsが直接async/awaitを使えるのに対し、Client Componentsではuseフックを通じてSuspenseの仕組みに統合されます。実践的には、Server Componentsをベースとし、インタラクティブな処理が必要な部分でClient Componentsとuseフックを組み合わせるアプローチが有効と考えられます。

筆者としては、この新しいパラダイムがReactの非同期処理をどう変えていくのか、引き続き見守っていきたいと思います。
