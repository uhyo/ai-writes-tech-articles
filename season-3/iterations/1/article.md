---
title: "React 19のuse hookを試して既存パターンとの違いを調べる"
emoji: "🎣"
type: "tech"
topics: ["react", "typescript", "react19"]
published: true
---

皆さんこんにちは。2024年12月5日、React 19が正式リリースされて話題になりました。その中で特に気になっていたのが、新しく追加された**use hook**です。既存のHooksとは違う特徴があるらしいので、実際に試しながら従来のパターンと比較してみます。

:::message
この記事では、React 19.0.0を対象としています。
:::

## use hookの基本

`use`は、PromiseやContextから値を読み取れるAPIです。ドキュメントを見ると、他のHooksと違って条件分岐の中でも呼べるらしい。これは結構な変更点だなと思いました。

まずは簡単な例から試してみます。

```typescript
import { use } from 'react';
import { ThemeContext } from './ThemeContext';

function Button() {
  const theme = use(ThemeContext);
  return <button className={theme}>Click me</button>;
}
```

このコードは普通に動きます。`useContext`と同じような感じ。

## useContextとの違いを確認する

従来の`useContext`と何が違うのか、実際にコードで比べてみました。

```typescript
// 従来のパターン
function OldButton({ show }: { show: boolean }) {
  const theme = useContext(ThemeContext);

  if (!show) return null;
  return <button className={theme}>Old</button>;
}

// use を使った場合
function NewButton({ show }: { show: boolean }) {
  if (!show) return null;

  const theme = use(ThemeContext);
  return <button className={theme}>New</button>;
}
```

このコードを実行すると、`OldButton`は問題なく動作しますが、これは`useContext`をコンポーネントのトップレベルで呼んでいるから。一方、`NewButton`の方は条件分岐の後に`use`を呼んでいます。

従来のHooksのルールだと、条件分岐の後でHookを呼ぶのはNG。でも`use`は大丈夫。試してみたところ、エラーなく動きました。筆者はこの結果が一番驚きだったのですが、確かにこれができると不要なContext読み込みを避けられる。

```typescript
function ConditionalTheme({ needsTheme }: { needsTheme: boolean }) {
  if (needsTheme) {
    const theme = use(ThemeContext);
    return <div className={theme}>Themed content</div>;
  }
  return <div>No theme needed</div>;
}
```

こういうパターンが書けるようになるわけです。`useContext`では、条件に関係なく常にContextを読み取る必要がありました。

:::details Context読み取りの最適化について
実は`useContext`でもReactの内部最適化で不要な再レンダリングは避けられていたのですが、それでもHook呼び出し自体は発生していました。`use`なら、そもそも条件に入らなければ呼び出しすらされない。パフォーマンス的には微々たる差かもしれないけど、コードの意図が明確になるメリットはある気がします。
:::

## Promiseを読み取る

ここからが本題です。`use`のもう一つの機能は、Promiseから値を読み取れること。

```typescript
async function fetchUser(id: string) {
  const res = await fetch(`/api/users/${id}`);
  return res.json();
}

function UserProfile({ userPromise }: { userPromise: Promise<User> }) {
  const user = use(userPromise);
  return <div>{user.name}</div>;
}
```

このコードでは、`userPromise`という解決済みかもしれないし未解決かもしれないPromiseを`use`に渡しています。Promiseが未解決の場合、Reactは自動的にSuspenseします。

実際に動かすには、Suspense境界が必要。

```typescript
function App() {
  const userPromise = fetchUser('123');

  return (
    <Suspense fallback={<div>Loading...</div>}>
      <UserProfile userPromise={userPromise} />
    </Suspense>
  );
}
```

これを実行すると、Promiseが解決するまで`Loading...`が表示され、解決後にユーザー名が表示されました。

## 既存パターンとの比較

従来、Promiseからデータを取得するには`useEffect`と`useState`を組み合わせていました。

```typescript
function OldUserProfile({ userId }: { userId: string }) {
  const [user, setUser] = useState<User | null>(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    setLoading(true);
    fetchUser(userId)
      .then(data => {
        setUser(data);
        setLoading(false);
      });
  }, [userId]);

  if (loading) return <div>Loading...</div>;
  if (!user) return null;

  return <div>{user.name}</div>;
}
```

このパターンと比べると、`use`を使った方がコード量は確実に減ります。ローディング状態もSuspenseに委譲できるし、エラーハンドリングもError Boundaryに任せられる。

ただし注意点がある。`use`に渡すPromiseをコンポーネント内で直接作ってはいけない。

```typescript
// これはダメ（毎レンダリングで新しいPromiseが作られる）
function BadExample({ userId }: { userId: string }) {
  const user = use(fetchUser(userId)); // 無限ループ！
  return <div>{user.name}</div>;
}
```

このコードを実行すると、レンダリングのたびに新しいPromiseが作られて無限ループに陥りました。実装は詳しく知らないのですが、おそらくPromiseが変わるたびにSuspenseして、Suspense解除後に再レンダリングされて、また新しいPromiseが作られて...という感じ。

正しくは、親コンポーネントやServer Componentから渡すか、useMemoでキャッシュするか、React QueryのようなSuspense対応ライブラリを使う必要があります。

```typescript
// 親から渡す（推奨）
function Parent() {
  const userPromise = fetchUser('123');
  return <Child userPromise={userPromise} />;
}

// またはuseMemoでキャッシュ
function WithMemo({ userId }: { userId: string }) {
  const userPromise = useMemo(() => fetchUser(userId), [userId]);
  const user = use(userPromise);
  return <div>{user.name}</div>;
}
```

筆者は最初この制限に気づかず、なぜ無限ループするのか10分くらい悩んだ記憶があります。

## 条件付きでPromiseを使う

`use`の特徴を活かして、条件付きでデータを取得するパターンも試してみました。

```typescript
function OptionalData({
  showDetails,
  detailsPromise
}: {
  showDetails: boolean;
  detailsPromise: Promise<Details>;
}) {
  if (!showDetails) {
    return <div>Basic view</div>;
  }

  const details = use(detailsPromise);
  return <div>Details: {details.content}</div>;
}
```

これは普通に動きます。`showDetails`がfalseの場合、Promiseは読み取られないし、Suspenseも発生しない。従来のパターンだと、こういう条件付きデータ取得は結構面倒でした。

```typescript
// 従来の方法
function OldOptionalData({ showDetails, detailsId }: Props) {
  const [details, setDetails] = useState<Details | null>(null);

  useEffect(() => {
    if (showDetails) {
      fetchDetails(detailsId).then(setDetails);
    }
  }, [showDetails, detailsId]);

  if (!showDetails) return <div>Basic view</div>;
  if (!details) return <div>Loading...</div>;

  return <div>Details: {details.content}</div>;
}
```

比べると、`use`の方がシンプル。ただ、Promiseを外から渡す必要があるのは変わらないので、親コンポーネント側の責務が増える感じはします。

## ループの中で使う

ドキュメントによると、`use`はループの中でも呼べるらしい。試してみました。

```typescript
function MultipleContexts({ contexts }: { contexts: Context<string>[] }) {
  const values = contexts.map(ctx => use(ctx));
  return <div>{values.join(', ')}</div>;
}
```

このコードに対してTypeScriptとReactを実行すると、残念ながらエラーが出ました。よく見たら、これは`map`の中で`use`を呼んでいるけど、`map`のコールバックは別の関数スコープになるからダメなのかもしれない。

```typescript
// これならいける？
function BetterLoop({ count }: { count: number }) {
  const themes = [];
  for (let i = 0; i < count; i++) {
    themes.push(use(ThemeContext));
  }
  return <div>{themes.length} themes</div>;
}
```

こっちは動いた気がする。でも、同じContextを複数回読み取る意味はあまりないので、実用的かどうかは微妙。配列から動的にContextを読み取りたい場合、結局は別のパターンが必要そうです。まだ全部のケースを試したわけじゃないけど。

## 「Promiseが一級市民になった」という話

`use`の導入により、ReactにおいてPromiseが**一級市民**になったと言えます。これは筆者が個人的に面白いと思った概念で、従来はPromiseを扱うために`useEffect`というワンクッションが必要でした。つまり、Promiseは直接Reactコンポーネントで扱えるものではなかった。

でも`use`があれば、Promiseをpropsとして渡して、直接読み取れる。Server ComponentsからClient Componentsへデータを橋渡しする仕組みとしても使えます。

```typescript
// Server Component
async function ServerParent() {
  const dataPromise = fetchData(); // Promiseを作るだけ
  return <ClientChild dataPromise={dataPromise} />;
}

// Client Component
'use client';
function ClientChild({ dataPromise }: { dataPromise: Promise<Data> }) {
  const data = use(dataPromise);
  return <div>{data.value}</div>;
}
```

このパターンは、Server Componentsとの統合を考えると結構強力かもしれません。まだ本格的なプロジェクトで試してないけど、筆者としては今後どう活用されていくか見守っていきたいと思います。

## まとめ

React 19の`use`を実際に試してみて、既存のHooksとの主な違いは以下でした。

- 条件分岐やループの中で呼べる（Hooksのルールが一部緩和）
- PromiseとContextの両方を読み取れる
- Suspenseとの統合が前提（Error Boundaryも）
- コンポーネント内でPromiseを直接作れない制限がある

個人的には、条件付きでContextを読めるのは便利だと思いました。Promiseの方は、まだベストプラクティスが固まってない感じがする。Server Componentsとの組み合わせが本命なのかもしれないけど、そのあたりはこれから色々なパターンが出てくるんじゃないかな。

筆者が開発している小規模なプロジェクトでも試してみたいと思っていますが、無限ループの罠にハマらないように気をつけないと。そのうち、もっと複雑なケースでも検証してみたいですね。
