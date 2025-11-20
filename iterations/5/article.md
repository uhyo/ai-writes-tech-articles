---
title: "React Server Componentsのストリーミングとサスペンスの実装パターン"
emoji: "🌊"
type: "tech"
topics: ["react", "nextjs", "typescript", "frontend"]
published: true
---

皆さんこんにちは。Next.js 13のApp Routerが登場してから、**Server Components**の**ストリーミング**機能が注目を集めています。筆者も最近、Server Componentsのレンダリング戦略について考える機会があり、ストリーミングと**Suspense**の組み合わせについて調べてみました。

Server Componentsでは、サーバー側でコンポーネントをレンダリングし、その結果を段階的にクライアントに送信できます。これにより、初期表示の高速化だけでなく、重い処理を含むコンポーネントを段階的に表示できるようになります。

この記事では、ストリーミングとSuspenseを組み合わせた実装パターンを試してみます。

## ストリーミングの基本

まずは、最もシンプルなストリーミングの例。

Server Componentsでは、`async`関数コンポーネントを定義できます。これをSuspenseで囲むと、データ取得中はfallbackが表示され、取得完了後に本来のコンテンツがストリーミングされるはずです。

```tsx
async function UserProfile({ userId }: { userId: string }) {
  const user = await fetchUser(userId);
  return <div>{user.name}</div>;
}

export default function Page() {
  return (
    <Suspense fallback={<div>Loading...</div>}>
      <UserProfile userId="123" />
    </Suspense>
  );
}
```

このコードでは、`UserProfile`がデータ取得を完了するまで`Loading...`が表示され、完了後に実際のユーザー名が表示されると考えられます。

:::message
この記事はNext.js 14時点の挙動です。Next.js 15ではストリーミングの動作が変わる可能性があります。
:::

## 複数のSuspense境界

次に、複数のコンポーネントがそれぞれ独立してストリーミングされるパターン。

```tsx
async function UserProfile({ userId }: { userId: string }) {
  await sleep(1000);
  const user = await fetchUser(userId);
  return <div>User: {user.name}</div>;
}

async function UserPosts({ userId }: { userId: string }) {
  await sleep(2000);
  const posts = await fetchPosts(userId);
  return <div>Posts: {posts.length}</div>;
}

export default function Page() {
  return (
    <div>
      <Suspense fallback={<div>Loading user...</div>}>
        <UserProfile userId="123" />
      </Suspense>
      <Suspense fallback={<div>Loading posts...</div>}>
        <UserPosts userId="123" />
      </Suspense>
    </div>
  );
}
```

このパターンでは、`UserProfile`と`UserPosts`がそれぞれ独立したSuspense境界で囲まれています。理論的には、`UserProfile`は1秒後にストリーミングされ、`UserPosts`は2秒後にストリーミングされるはずです。

コードを見る限り、最初に両方のfallbackが表示され、1秒後に`UserProfile`が表示され、2秒後に`UserPosts`が表示されると考えられます。これは、それぞれのコンポーネントが独立してストリーミングされていることを示しています。

個人的には少し驚いたのですが、Next.jsのApp Routerでは、Suspense境界を使うだけで自動的に並列でデータ取得が行われるようです。Reactコミュニティでも議論されている特徴で、従来のウォーターフォール型のデータ取得から大きく改善されています。

## ネストしたSuspense境界

ネストしたSuspense境界の挙動を確認してみます。

```tsx
async function UserDetails({ userId }: { userId: string }) {
  await sleep(1000);
  const user = await fetchUser(userId);
  return <div>Details: {user.email}</div>;
}

async function UserProfile({ userId }: { userId: string }) {
  await sleep(500);
  const user = await fetchUser(userId);

  return (
    <div>
      <div>Name: {user.name}</div>
      <Suspense fallback={<div>Loading details...</div>}>
        <UserDetails userId={userId} />
      </Suspense>
    </div>
  );
}

export default function Page() {
  return (
    <Suspense fallback={<div>Loading profile...</div>}>
      <UserProfile userId="123" />
    </Suspense>
  );
}
```

このコードでは、外側のSuspenseが`UserProfile`を囲み、内側のSuspenseが`UserDetails`を囲んでいます。

理論的には、まず外側のfallbackが表示され、500ms後に`UserProfile`のName部分と内側のfallbackが表示され、さらに1000ms後に`UserDetails`が表示されるはずです。つまり、ストリーミングが段階的に行われると考えられます。

:::details ネストしたSuspenseのエラーハンドリングについて
ネストしたSuspense境界では、エラーハンドリングも階層的に行われます。内側のコンポーネントでエラーが発生した場合、最も近いError Boundaryまでバブルアップするはずです。ただし、Server Componentsでのエラーハンドリングは通常のReactとは異なる挙動を示すことがあるため、注意が必要です。

GitHubで関連する議論があるようですが、エラーバウンダリの配置戦略についてはまだベストプラクティスが確立されていないように見えます。
:::

## `use`フックとの組み合わせ

React 19で導入予定の**useフック**を使うと、クライアントコンポーネントでもPromiseを扱えるようになります。

```tsx
'use client';

import { use } from 'react';

function UserProfile({ userPromise }: { userPromise: Promise<User> }) {
  const user = use(userPromise);
  return <div>{user.name}</div>;
}
```

このパターンでは、親のServer Componentでデータ取得を開始し、そのPromiseをクライアントコンポーネントに渡します。

```tsx
async function Page() {
  const userPromise = fetchUser('123');

  return (
    <Suspense fallback={<div>Loading...</div>}>
      <UserProfile userPromise={userPromise} />
    </Suspense>
  );
}
```

この実装で重要なのは、Promiseを親コンポーネントで作成し、子コンポーネントに渡している点です。子コンポーネント内でPromiseを作成すると、再レンダリングのたびに新しいPromiseが作られてしまい、無限ループに陥る可能性があります。

筆者はこのパターンを初めて見たとき、少し奇妙に感じました。通常、Reactではデータ取得ロジックをコンポーネント内に閉じ込める方がカプセル化の観点から良いとされているからです。しかし、ストリーミングの文脈では、Promiseを外部で管理する必要があるようです。

## ストリーミングの制限事項

ストリーミングには、いくつか制限事項があります。

まず、**動的レンダリング**が必要なコンポーネントでは、ストリーミングが効果的に機能しないことがあります。例えば、`cookies()`や`headers()`を使うコンポーネントは、リクエスト時にしか値が確定しないため、ストリーミングの恩恵を受けにくいはずです。

また、Next.jsのApp Routerでは、ルート全体が動的レンダリングになると、ストリーミングの挙動が変わることがあります。筆者はここの詳細をまだ完全には理解していないのですが、静的レンダリングと動的レンダリングの境界でストリーミングの動作が異なると考えられます。

:::message
ストリーミングを活用する際は、動的レンダリングの範囲を最小限に抑えることが重要です。`cookies()`や`headers()`の呼び出しは、必要な箇所だけに限定するとよいでしょう。
:::

さらに、ストリーミング中のエラーハンドリングも複雑です。サーバー側でエラーが発生した場合、すでにクライアントに送信済みのHTMLをどう扱うかという問題があります。Next.js 14では、エラーが発生するとストリーミングが中断され、Error Boundaryが表示されるはずですが、この挙動は将来変わる可能性があります。

筆者としては、ストリーミング中のエラーハンドリングについてはまだ試していない部分が多く、今後の実験が必要だと感じています。

## まとめ

Server Componentsのストリーミングは、初期表示の高速化と段階的なレンダリングを実現する強力な機能です。Suspense境界を適切に配置することで、ユーザー体験を大幅に改善できると考えられます。

筆者としては、今後もストリーミングの活用パターンが進化していくと考えられます。特に、React 19の`use`フックとの組み合わせは、クライアントコンポーネントでのデータ取得パターンを変える可能性があり、注目しています。

ただし、ストリーミングには制限事項もあり、すべてのケースで有効というわけではありません。動的レンダリングとの兼ね合いや、エラーハンドリングの複雑さなど、考慮すべき点は多いです。

これからどう実装パターンが洗練されていくか、見守っていきたいと思います。
