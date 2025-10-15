---
title: "Next.js の App Router とデータフェッチングパターン"
emoji: "🚀"
type: "tech"
topics: ["nextjs", "react", "typescript"]
published: true
---

皆さんこんにちは。Next.js 13で導入されたApp Routerなんですが、最初見たとき「Pages Routerと何が違うん？」って思った。特にデータフェッチング周りが大きく変わっていて、Server Componentsの概念が入ってきたことで、従来のgetServerSidePropsやgetStaticPropsとは全く異なるパターンになっています。

この記事では、App Routerにおけるデータフェッチングの実践的なパターンをいくつか紹介します。

## Server Componentsでの直接fetch

App Routerの最大の特徴は、コンポーネント内で直接データフェッチできることです。これがめちゃくちゃ楽。

```typescript
// app/posts/page.tsx
async function getPosts() {
  const res = await fetch('https://api.example.com/posts');
  return res.json();
}

export default async function PostsPage() {
  const posts = await getPosts();

  return (
    <div>
      {posts.map(post => (
        <article key={post.id}>
          <h2>{post.title}</h2>
        </article>
      ))}
    </div>
  );
}
```

Pages RouterだとgetServerSidePropsを書いて、propsとして渡して...という手順が必要だったのが、コンポーネントを`async`にするだけで完結します。コードの見通しがかなり良くなりました。

ちなみに、Server Componentsの仕様自体はReact本体のRFC（react-server-dom-webpack関連のPRとか）で定義されてるもので、Next.jsが独自に作ったわけじゃない。React 18のサーバーコンポーネント実装を先行採用してる形です。

## デフォルトでキャッシュされる問題

ところで、App Routerの`fetch`はデフォルトでキャッシュされます。これ、最初知らなくて「なんでデータが更新されないんだ？」って3時間くらい悩んだ記憶がある。

```typescript
// これはキャッシュされる
const res = await fetch('https://api.example.com/posts');

// キャッシュしたくない場合
const res = await fetch('https://api.example.com/posts', {
  cache: 'no-store'
});

// 再検証期間を設定
const res = await fetch('https://api.example.com/posts', {
  next: { revalidate: 60 } // 60秒ごとに再検証
});
```

この挙動、個人的には「デフォルトでキャッシュ」より「デフォルトでno-store」の方が直感的だと思うんですが、まあNext.jsとしては静的最適化を優先したんでしょう。

`next.revalidate`オプションはNext.js独自の拡張で、標準のFetch APIにはない機能です。ISR（Incremental Static Regeneration）的な動きを実現できます。Pages Routerの`getStaticProps`に慣れてる人なら、revalidateの概念自体は馴染みがあるかな。

## 並列データフェッチ

複数のAPIからデータを取得する場合、直列にawaitすると遅くなります。Promise.allを使って並列化するのが定石です。

```typescript
export default async function PostDetailPage({ params }: { params: { id: string } }) {
  // これだと遅い
  // const post = await getPost(params.id);
  // const comments = await getComments(params.id);
  // const author = await getAuthor(post.authorId);

  // 並列化
  const [post, comments] = await Promise.all([
    getPost(params.id),
    getComments(params.id)
  ]);

  // authorは後から
  const author = await getAuthor(post.authorId);

  return (
    <div>
      <h1>{post.title}</h1>
      <p>by {author.name}</p>
      {/* ... */}
    </div>
  );
}
```

あ、authorのfetchはpostに依存してるので並列化できないんですが、postとcommentsは独立してるから並列にできる。こういう依存関係を考えながら最適化していくと、体感速度がかなり改善されます。

ちなみに、Next.jsはfetchリクエストを自動で重複排除してくれる（Request Deduplication）ので、同じURLを複数箇所でfetchしても実際のネットワークリクエストは1回だけになります。これはPages Routerにはなかった機能で、コンポーネント単位でデータを取得しやすくなった理由の一つです。

## Suspenseとストリーミング

App Routerでは、Suspenseを使ったストリーミングレンダリングが可能です。データフェッチが遅い部分だけローディング状態にして、他の部分は先に表示できます。

```typescript
// app/dashboard/page.tsx
import { Suspense } from 'react';

async function Analytics() {
  // 重い処理
  const data = await fetch('https://api.example.com/analytics', {
    cache: 'no-store'
  });
  const analytics = await data.json();

  return <div>{/* analytics表示 */}</div>;
}

async function RecentPosts() {
  const data = await fetch('https://api.example.com/recent-posts');
  const posts = await data.json();

  return <div>{/* posts表示 */}</div>;
}

export default function DashboardPage() {
  return (
    <div>
      <h1>Dashboard</h1>
      <Suspense fallback={<div>Loading recent posts...</div>}>
        <RecentPosts />
      </Suspense>
      <Suspense fallback={<div>Loading analytics...</div>}>
        <Analytics />
      </Suspense>
    </div>
  );
}
```

RecentPostsは軽いからすぐ表示されて、Analyticsは重いから後から表示される。ユーザー体験としては、真っ白な画面で待たされるより、使える部分から徐々に表示される方が圧倒的に良いです。

Suspenseの粒度をどうするかは結構悩ましい問題で、細かくしすぎるとローディングスピナーだらけになって逆に見づらくなる。この辺のバランス感覚は実際に触ってみて調整するしかないかな。あと、ストリーミングSSRの恩恵を受けるには、ホスティング環境がストリーミングレスポンスに対応してる必要があります。Vercelなら問題ないけど、古いNode.jsサーバーだと注意が必要。

## Client Componentでのデータフェッチ

Server Componentsだけで全部完結できれば理想的ですが、インタラクティブな機能が必要な場合はClient Componentになります。その場合のデータフェッチパターンはいくつかあります。

### useEffectでフェッチ（非推奨）

```typescript
'use client';

import { useEffect, useState } from 'react';

export function UserProfile({ userId }: { userId: string }) {
  const [user, setUser] = useState(null);

  useEffect(() => {
    fetch(`/api/users/${userId}`)
      .then(res => res.json())
      .then(data => setUser(data));
  }, [userId]);

  if (!user) return <div>Loading...</div>;

  return <div>{user.name}</div>;
}
```

これ、動くには動くんですが、競合状態（race condition）のリスクがあるし、サーバー側でレンダリングされないので初期表示が遅くなります。できれば避けたいパターン。

あ、そういえばuseEffectのcleanupを書いてないな。userIdが変わったときに、前のリクエストをキャンセルする処理が必要です。

```typescript
useEffect(() => {
  let cancelled = false;

  fetch(`/api/users/${userId}`)
    .then(res => res.json())
    .then(data => {
      if (!cancelled) setUser(data);
    });

  return () => {
    cancelled = true;
  };
}, [userId]);
```

こういう細かいハンドリング、毎回書くのはめんどくさい。だからデータフェッチライブラリを使う方が現実的です。

### SWRやReact Queryを使う

実践的には、データフェッチライブラリを使うのが良いです。

```typescript
'use client';

import useSWR from 'swr';

const fetcher = (url: string) => fetch(url).then(res => res.json());

export function UserProfile({ userId }: { userId: string }) {
  const { data: user, error, isLoading } = useSWR(
    `/api/users/${userId}`,
    fetcher
  );

  if (error) return <div>Failed to load</div>;
  if (isLoading) return <div>Loading...</div>;

  return <div>{user.name}</div>;
}
```

SWRはキャッシュ、再検証、エラーハンドリングなどを自動でやってくれるので便利です。React Queryも同様に強力なライブラリで、どちらを選ぶかは好みの問題かな。個人的にはSWRの方がシンプルで好きですが、複雑な要件があるならReact Queryの方が柔軟性があります。

### Server ComponentからPropsで渡す

もっと良いのは、親のServer Componentでデータを取得してPropsで渡すパターンです。

```typescript
// app/users/[id]/page.tsx (Server Component)
async function getUser(id: string) {
  const res = await fetch(`https://api.example.com/users/${id}`);
  return res.json();
}

export default async function UserPage({ params }: { params: { id: string } }) {
  const user = await getUser(params.id);

  return (
    <div>
      <UserProfile user={user} />
    </div>
  );
}

// components/UserProfile.tsx (Client Component)
'use client';

export function UserProfile({ user }: { user: User }) {
  const [isFollowing, setIsFollowing] = useState(false);

  return (
    <div>
      <h1>{user.name}</h1>
      <button onClick={() => setIsFollowing(!isFollowing)}>
        {isFollowing ? 'Unfollow' : 'Follow'}
      </button>
    </div>
  );
}
```

こうすることで、初期データはサーバー側で取得してSSRできるし、Client Componentではインタラクティブな部分だけを扱えます。Server ComponentsとClient Componentsの責任分離ができて、コードがすっきりします。

このパターンが推奨されてる理由は、初期表示のパフォーマンスとSEOです。Server Componentでデータを取ってくれば、HTMLに既にデータが埋め込まれた状態でクライアントに送られます。Client Componentでfetchする場合、JavaScriptがロードされて実行されるまでデータが取得されないので、その分遅くなります。

## Route Handlersでのキャッシュ制御

APIルートを自分で実装する場合、Route Handlers（app/api）を使います。Pages RouterのAPI Routesと似てますが、微妙に違う。

```typescript
// app/api/posts/route.ts
export async function GET() {
  const posts = await db.post.findMany();

  return Response.json(posts);
}

// キャッシュしない場合
export const dynamic = 'force-dynamic';

export async function GET() {
  const posts = await db.post.findMany();

  return Response.json(posts);
}

// 再検証期間を設定
export const revalidate = 60; // 60秒

export async function GET() {
  const posts = await db.post.findMany();

  return Response.json(posts);
}
```

`dynamic`や`revalidate`といったRoute Segment Configを使ってキャッシュを制御できます。この辺の設定、ドキュメント読まないと分かりにくいんですが、理解すると強力です。

Route Handlersは標準の`Request`/`Response` APIを使うので、Pages RouterのNextApiRequestとかNextApiResponseとは全然違います。最初これに戸惑った。

```typescript
// Pages Router (古い)
export default function handler(req: NextApiRequest, res: NextApiResponse) {
  if (req.method === 'POST') {
    const { title } = req.body;
    res.status(200).json({ success: true });
  }
}

// App Router (新しい)
export async function POST(request: Request) {
  const { title } = await request.json();
  return Response.json({ success: true });
}
```

Pages Routerだと`req.body`に直接アクセスできたけど、App Routerでは`await request.json()`しないといけない。これは標準のFetch APIの仕様に従ってるからです。Web標準に寄せたのは良いことだと思うけど、移行時は戸惑います。

あと、`NextRequest`と`NextResponse`というNext.js独自の拡張版もあって、こっちを使うとcookiesやheadersへのアクセスが楽になります。

```typescript
import { NextRequest, NextResponse } from 'next/server';

export async function GET(request: NextRequest) {
  const token = request.cookies.get('token');

  if (!token) {
    return NextResponse.json({ error: 'Unauthorized' }, { status: 401 });
  }

  // ...
}
```

標準のRequestでもcookiesにアクセスできるけど、`request.headers.get('cookie')`をパースしないといけないので面倒です。NextRequestを使った方が楽。

## データベース直接アクセス

Server Componentsから直接データベースにアクセスすることもできます。これ、最初「え、フロントエンドのコードからDB叩くの？」って違和感あったんですが、Server Componentsはサーバーでしか実行されないので、むしろ自然なんですよね。

```typescript
// app/posts/page.tsx
import { db } from '@/lib/db';

async function getPosts() {
  return await db.post.findMany({
    orderBy: { createdAt: 'desc' },
    take: 10
  });
}

export default async function PostsPage() {
  const posts = await getPosts();

  return (
    <div>
      {posts.map(post => (
        <article key={post.id}>
          <h2>{post.title}</h2>
        </article>
      ))}
    </div>
  );
}
```

Prismaとかを使ってる場合、この方がAPI Routeを経由するより速いです。中間層を挟まない分、レイテンシが減ります。

ただし、Client Componentから使う場合は注意が必要で、DBクエリはServer Componentで実行してPropsで渡すか、API Routeを経由する必要があります。当たり前だけど、クライアント側で直接DB接続情報を持つわけにはいかないので。

このパターン、セキュリティ的に大丈夫なのか心配になるかもしれませんが、Server Componentsのコードはクライアントに送られません。ビルド時に完全に分離されるので、DB接続情報やシークレットがクライアントに漏れることはないです。まあ、それでも.envファイルの管理はちゃんとやる必要がありますが。

## Dynamic Rendering vs Static Rendering

App Routerでは、ページが動的レンダリングか静的レンダリングかをNext.jsが自動判定します。判定ロジックは複雑ですが、基本的には以下のパターンです。

- `cache: 'no-store'`を使ってる → Dynamic
- `cookies()`や`headers()`を使ってる → Dynamic
- `searchParams`を使ってる → Dynamic
- 上記以外 → Static

```typescript
import { cookies } from 'next/headers';

export default async function ProfilePage() {
  const cookieStore = cookies();
  const token = cookieStore.get('auth-token');

  // このページは動的レンダリングになる
}
```

最初これ知らなくて、「なんでビルドが速いんだろう？」って思ってたら、全部動的レンダリングになってて静的生成されてなかった。Vercelにデプロイしたときの Functions のログ見て気づいた。

静的にしたいページでは、cookiesやheadersを使わないように注意が必要です。でも実際のアプリケーションだと、認証が必要なページはほぼ動的になるので、静的生成できるのは公開ページくらいかな。

`generateStaticParams`を使うと、動的ルートでも事前生成できます。

```typescript
export async function generateStaticParams() {
  const posts = await db.post.findMany();

  return posts.map(post => ({
    id: post.id.toString()
  }));
}

export default async function PostPage({ params }: { params: { id: string } }) {
  const post = await getPost(params.id);
  return <article>{post.title}</article>;
}
```

Pages Routerの`getStaticPaths`に相当する機能です。ただ、大量のページがある場合、全部事前生成するのは現実的じゃないので、よくアクセスされるページだけ静的生成して、残りは動的にする、みたいな戦略になるかな。そのうち試してみたい。

## まとめというか雑感

App Routerのデータフェッチング、最初は戸惑うことが多かったんですが、慣れるとPages Routerより柔軟で強力だと感じます。特にServer Componentsとの組み合わせは、サーバーサイドのロジックとフロントエンドのロジックを自然に統合できて良いです。

キャッシュ周りの挙動が少し複雑なので、そこだけは注意が必要かな。`cache`オプションや`revalidate`の設定を理解しておくと、パフォーマンスチューニングがしやすくなります。デフォルトでキャッシュされる挙動は、静的最適化を重視するNext.jsの思想だと理解できますが、動的なデータを扱う場合は`cache: 'no-store'`を明示的に指定する癖をつけた方が良いです。

あと、Suspenseとストリーミングの組み合わせは、まだベストプラクティスが確立されてない感じがあって、コミュニティでの知見の蓄積を待ってる状態。そのうち「これが定番」みたいなパターンが出てくると思うんですが、今のところは試行錯誤してる段階です。

個人的には、Next.js 14でさらにApp Routerが改善されて、Partial Prerendering（PPR）みたいな新機能も入ってきてるので、そっちも試してみたいと思ってます。まだ本番では使ってないけど。PPRは静的な部分と動的な部分を組み合わせて最適化する機能らしいんですが、どれくらい実用的なのか興味がある。

あと、Server ActionsとかReact Cacheとか、まだ触ってない機能も多い。App Routerの機能はかなり広範囲なので、全部使いこなすには時間がかかりそうです。
