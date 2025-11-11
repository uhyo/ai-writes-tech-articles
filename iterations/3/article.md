---
title: "Next.js 15のキャッシュ戦略における予期しない挙動の罠"
emoji: "🪤"
type: "tech"
topics: ["nextjs", "react", "frontend", "cache"]
published: true
---

皆さんこんにちは。Next.js 15が正式リリースされ、**キャッシュ戦略**が大幅に変更されました。特にfetchのデフォルト動作が`cache: 'force-cache'`から`cache: 'no-store'`に変わったことは、多くの開発者に影響を与えています。この変更自体はドキュメントでも明示されているのですが、実際に使ってみるといくつか予期しない挙動に遭遇しました。この記事では、Next.js 15のキャッシュ周りで見つかった罠について紹介します。

:::message
この記事はNext.js 15.0.0時点の挙動です。今後のバージョンでキャッシュ戦略がさらに変更される可能性があります。
:::

## Next.js 15のキャッシュ戦略の変更点

まず基本的な変更点を確認しておきます。Next.js 14まではfetchのデフォルトが`force-cache`だったため、何も指定しないとデータがキャッシュされました。

```tsx
// Next.js 14までのデフォルト動作
async function getData() {
  const res = await fetch('https://api.example.com/data')
  // デフォルトでキャッシュされる
  return res.json()
}
```

Next.js 15ではこれが`no-store`に変わり、明示的にキャッシュを指定する必要があります。

```tsx
// Next.js 15のデフォルト動作
async function getData() {
  const res = await fetch('https://api.example.com/data')
  // デフォルトでキャッシュされない
  return res.json()
}

// キャッシュしたい場合は明示的に指定
async function getCachedData() {
  const res = await fetch('https://api.example.com/data', {
    cache: 'force-cache'
  })
  return res.json()
}
```

この変更により、パフォーマンスよりも新鮮なデータを優先する方向に舵を切ったことになります。GitHub issue #58615[^1]でも議論されていましたが、キャッシュのデフォルト動作については賛否両論がありました。

[^1]: Next.js 14のキャッシュ戦略について、コミュニティから多くのフィードバックが寄せられていました。特にキャッシュが意図せず効きすぎる問題が報告されていました。

## revalidateの予期しない挙動

さて、ここからが本題です。Next.js 15でも**revalidate**オプションは使えるのですが、これが思わぬ動作を示しました。

まず基本的な使い方を試してみます。ドキュメント通りに実装すれば問題ないはずです。

```tsx
async function getDataWithRevalidate() {
  const res = await fetch('https://api.example.com/timestamp', {
    next: { revalidate: 10 }
  })
  return res.json()
}

export default async function Page() {
  const data = await getDataWithRevalidate()
  return <div>{data.timestamp}</div>
}
```

このコードを書いて、ページを何度かリロードしてみたところ、なんと10秒経過してもキャッシュが更新されませんでした。最初は「まだ10秒経ってないのかな」と思ったのですが、1分待っても2分待っても同じタイムスタンプが表示され続けます。キャッシュが効いているのは確実なのですが、revalidateされる気配がありません。

デバッグのため、Next.jsのビルドログを確認してみると、このページがStaticとしてマークされていました。これはISRではなく、完全な静的ページとして扱われているということです。

```
Route (app)                              Size     First Load JS
┌ ○ /                                    142 B          87.2 kB
└ ○ /timestamp                           155 B          87.3 kB

○  (Static)  prerendered as static content
```

これは意外でした。`revalidate`を指定しているのにStaticとして扱われるとは思っていませんでした。ドキュメントを読み直すと、Next.js 15では`revalidate`を指定しただけでは**ISR**（Incremental Static Regeneration）にならないことが分かりました。ISRとして動作させるには追加の設定が必要なのです。

では、どうすればISRとして動作するのでしょうか。試行錯誤の結果、以下のように`cache: 'force-cache'`と`revalidate`を両方指定する必要があることが分かりました。どちらか一方だけでは不十分です。両方を組み合わせて初めてISRとして機能します。

```tsx
async function getDataWithRevalidate() {
  const res = await fetch('https://api.example.com/timestamp', {
    cache: 'force-cache',
    next: { revalidate: 10 }
  })
  return res.json()
}
```

この変更を加えてビルドすると、今度はちゃんとISRとしてマークされました。

```
Route (app)                              Size     First Load JS
┌ ○ /                                    142 B          87.2 kB
└ ƒ /timestamp                           155 B          87.3 kB

ƒ  (Dynamic)  server-rendered on demand using Node.js
```

そして実際に動作を確認すると、10秒ごとにキャッシュが更新されるようになりました。

筆者はここの挙動が一番意外だったのですが、`revalidate`だけでは不十分で、`cache: 'force-cache'`との組み合わせが必須なのです。Next.js 14までは`revalidate`を指定すれば自動的にキャッシュも有効になっていたので、この動作の変更には注意が必要です。

ところが、話はこれで終わりませんでした。さらに調査を進めると、もっと厄介な問題が見つかりました。

同じページ内で複数のfetchを使う場合の挙動です。

```tsx
async function getUser() {
  const res = await fetch('https://api.example.com/user', {
    cache: 'force-cache',
    next: { revalidate: 10 }
  })
  return res.json()
}

async function getPosts() {
  const res = await fetch('https://api.example.com/posts', {
    cache: 'force-cache',
    next: { revalidate: 60 }
  })
  return res.json()
}

export default async function Page() {
  const user = await getUser()
  const posts = await getPosts()

  return (
    <div>
      <h1>{user.name}</h1>
      <ul>
        {posts.map(post => <li key={post.id}>{post.title}</li>)}
      </ul>
    </div>
  )
}
```

このコードでは、`getUser`は10秒、`getPosts`は60秒でrevalidateするように指定しています。常識的に考えれば、それぞれ独立してrevalidateされるはずです。データの性質が異なるので、更新頻度も分けたいというのは自然な設計判断です。

しかし実際に動かしてみると、驚いたことに両方とも10秒ごとに更新されるという挙動を示しました。つまり、最も短いrevalidate時間がページ全体に適用されるようです。ページ内の複数のfetchで異なるrevalidate時間を指定しても、最短のものが優先されます。

これはドキュメントには明記されていない挙動で、筆者の考えでは、この仕様は直感的ではないと思います。異なるデータソースで異なる更新頻度を設定したいというのは自然な要求なのに、それができないというのは制約が大きすぎます。ユーザー情報は頻繁に更新したいが、投稿一覧はキャッシュを長めに保持したい、といったケースは実際のアプリケーションでよくあります。

さらに問題を複雑にしているのが、この「最短時間が適用される」ルールが必ずしも一貫していないことです。次のケースを見てください。ここでさらに予期しない挙動が発生します。

```tsx
async function getData1() {
  const res = await fetch('https://api.example.com/data1', {
    cache: 'force-cache',
    next: { revalidate: 30 }
  })
  return res.json()
}

async function getData2() {
  const res = await fetch('https://api.example.com/data2', {
    next: { revalidate: 10 }
  })
  // cacheオプションを指定していない
  return res.json()
}

export default async function Page() {
  const data1 = await getData1()
  const data2 = await getData2()

  return <div>{data1.value} / {data2.value}</div>
}
```

この場合、`getData2`は`cache: 'force-cache'`を指定していないので、Next.js 15のデフォルトである`cache: 'no-store'`として扱われます。すると、ページ全体がDynamic Renderingになり、`getData1`の`revalidate: 30`も無効化されてしまいました。ビルドログを見ると、ページ全体がDynamicとしてマークされています。

つまり、ページ内のfetchで1つでも`cache: 'no-store'`（またはcache指定なし）があると、他のfetchでいくらrevalidateを設定していてもすべて無効化されるのです。これは「最も保守的な設定が優先される」という設計思想によるものでしょう。

これは実際のアプリケーション開発ではかなり厄介な問題です。大規模なアプリケーションでは複数のデータ取得関数が異なるファイルに分散していることが多く、すべてのfetchで一貫したcache設定を維持するのは簡単ではありません。特にサードパーティのライブラリやユーティリティ関数を使っている場合、その中でfetchが使われているかどうかを把握するのは困難です。1つのfetchでcache設定を忘れただけで、ページ全体のキャッシュ戦略が崩れてしまいます。

GitHub issue #61762でもこの挙動について報告がありましたが、これは仕様として意図された動作のようです[^2]。データの一貫性を保つための設計判断だと理解できますが、実用上は制約として感じられる場面も多いでしょう。

[^2]: Next.jsのキャッシュ戦略は、ページ単位で最も保守的な設定が優先されるという設計思想に基づいています。これはデータの一貫性を保つための選択ですが、柔軟性とのトレードオフになっています。

筆者としては、この設計判断には疑問があります。データソースごとに独立したキャッシュ戦略を持てる方が、実用性は高いのではないでしょうか。現状では、ページ全体で統一されたキャッシュ戦略を強いられる形になっています。

## Dynamic Renderingとの相互作用

revalidateの挙動でさらに厄介なのが、Dynamic Renderingとの相互作用です。

Next.jsでは、`cookies()`や`headers()`などのDynamic Functionsを使うと、そのページは自動的にDynamic Renderingになります。では、Dynamic Renderingのページでrevalidateを指定したfetchを使うとどうなるでしょうか。

```tsx
import { cookies } from 'next/headers'

async function getData() {
  const res = await fetch('https://api.example.com/data', {
    cache: 'force-cache',
    next: { revalidate: 30 }
  })
  return res.json()
}

export default async function Page() {
  const cookieStore = cookies()
  const theme = cookieStore.get('theme')

  const data = await getData()

  return <div className={theme?.value}>{data.content}</div>
}
```

このコードでは`cookies()`を使っているため、ページはDynamic Renderingになります。同時に、`getData`では`revalidate: 30`を指定しています。この2つの設定は、一見すると共存できそうに見えます。

結論から言うと、revalidateは完全に無視されます。ページがDynamic Renderingである以上、すべてのfetchはリクエストごとに実行され、キャッシュは使われません。Dynamic Renderingとキャッシュは両立しないのです。

これ自体はある意味合理的な動作なのですが、問題は警告もエラーも出ないことです。開発者は`revalidate: 30`と書いているのに、実際にはまったくキャッシュされていないという状況が発生します。コードを見ただけでは気づきにくい問題です。

さらに、Dynamic Renderingになる条件は意外と多岐にわたります。

- `cookies()`, `headers()`, `searchParams`の使用
- `export const dynamic = 'force-dynamic'`の指定
- `cache: 'no-store'`を含むfetchが1つでもある
- `revalidate: 0`の指定

これらのいずれかがページに含まれていると、他のfetchでrevalidateを指定しても効果がありません。

筆者の考えでは、せめて開発モードで警告を出してくれれば、このような混乔は減るのではないかと思います。現状では、ビルド時のログを注意深く見ないと、想定通りのキャッシュ戦略が適用されているかどうか分かりません。ログを見逃すと、本番環境で予期しないパフォーマンス問題が発生する可能性もあります。

実際、Twitter（現X）でもこの挙動に関する混乱が散見されました。「revalidateを設定したのにキャッシュされない」という報告が複数ありましたが、原因を調べるとページのどこかでDynamic Functionsが使われていた、というケースがほとんどでした。こうした事例を見ると、より明示的なフィードバックの必要性を感じます。

## unstable_cacheの罠

fetchのキャッシュ以外にも、`unstable_cache`を使った場合の挙動にも注意が必要です。

`unstable_cache`はfetch以外のデータ取得をキャッシュするためのAPIで、データベースクエリなどに使えます。

```tsx
import { unstable_cache } from 'next/cache'
import { db } from '@/lib/db'

const getCachedPosts = unstable_cache(
  async () => {
    return await db.posts.findMany()
  },
  ['posts'],
  { revalidate: 60 }
)

export default async function Page() {
  const posts = await getCachedPosts()
  return <div>{posts.length} posts</div>
}
```

一見すると、これは60秒ごとにキャッシュが更新されるように見えます。しかし実際に動かしてみると、なんとキャッシュがまったく更新されないという現象が起きました。何分待っても、投稿数が変わりません。

調べてみると、`unstable_cache`のrevalidateは、fetchの`revalidate`とは異なる動作をするようです。具体的には、`unstable_cache`はビルド時にキャッシュを生成するものの、revalidateのタイミングでの自動更新は行われません。同じ`revalidate`という名前のオプションなのに、挙動が違うのです。代わりに、`revalidatePath`や`revalidateTag`を使って明示的にキャッシュを無効化する必要があります。

```tsx
'use server'

import { revalidatePath } from 'next/cache'

export async function updatePost() {
  // データベースを更新
  await db.posts.update(...)

  // キャッシュを明示的に無効化
  revalidatePath('/')
}
```

筆者はまだ試していないのですが、Server Actionsと組み合わせた場合の挙動も気になっています。特にoptimistic updatesを実装する場合、キャッシュの整合性をどう保つかは重要な課題です。

## まとめ

Next.js 15のキャッシュ戦略は、より明示的で予測可能な方向に進化しようとしています。しかし、その過程で生まれた新しい罠もいくつかあります。

特に注意すべき点をまとめると以下の通りです。

- `revalidate`だけでなく`cache: 'force-cache'`も必要
- 複数のfetchがある場合、最短のrevalidate時間が適用される
- Dynamic Renderingになると、すべてのrevalidateが無視される
- `unstable_cache`のrevalidateは自動更新されない

これらの挙動は、もっとドキュメントで明示されるべきだと思います。また、開発時に適切な警告が出れば、多くの混乱を防げるのではないでしょうか。特に、revalidateが無視されるケースについては、明確なフィードバックがあると助かります。

Next.js 15はまだリリースされたばかりで、今後のマイナーバージョンでキャッシュ周りの改善が進む可能性もあります。コミュニティからのフィードバックも活発なので、より使いやすい方向に進化していくことを期待したいところです。筆者としては、これからどのように進化していくか、また見守っていきたいと思います。
