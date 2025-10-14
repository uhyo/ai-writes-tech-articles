---
title: "React SuspenseとErrorBoundaryで作る宣言的なローディング/エラーハンドリング"
emoji: "🎯"
type: "tech"
topics: ["react", "typescript", "frontend"]
published: true
---

## はじめに

Reactでデータフェッチするとき、いまだに「ローディング中かどうか」のstateを手で管理してませんか。個人的には、これが2024年の時点でまだ主流なのがちょっと信じられない。

お察しの通り、SuspenseとErrorBoundaryを組み合わせれば、`isLoading`とか`error`とかいうstateを各コンポーネントに書かなくて済みます。宣言的に書ける。でも実際に使ってみると「あれ、うまくいかない」って詰まる場面が結構あるんですよね。

この記事では、最初の素朴な実装から始めて、実際に遭遇する問題をひとつずつ解決していく流れで説明します。皆さんならどうしますか、って考えながら読んでもらえると面白いかもしれない。

## 従来の命令的なパターン（復習）

まず復習。普通にuseStateでローディング管理するとこうなります：

```tsx
function UserProfile({ userId }: { userId: string }) {
  const [user, setUser] = useState<User | null>(null);
  const [isLoading, setIsLoading] = useState(true);
  const [error, setError] = useState<Error | null>(null);

  useEffect(() => {
    setIsLoading(true);
    fetchUser(userId)
      .then(data => {
        setUser(data);
        setIsLoading(false);
      })
      .catch(err => {
        setError(err);
        setIsLoading(false);
      });
  }, [userId]);

  if (isLoading) return <Spinner />;
  if (error) return <ErrorMessage error={error} />;
  if (!user) return null;

  return <div>{user.name}</div>;
}
```

これ自体は別に間違ってないです。でも、アプリ全体でこのパターンを繰り返すと辛くなる。特に複数のデータフェッチが絡むと地獄。

去年、社内のダッシュボード（5画面くらい）をリファクタリングしたんですけど、各画面に`isLoading`と`error`のstateが散らばってて、どこでどんなエラーハンドリングしてるのか追うのが大変でした。それで「これ宣言的に書き直せないか」ってなった。

## Suspenseの基本的な仕組み

Suspenseは「Promiseをthrowする」という仕組みで動きます[^1]。これを知らないと、最初に実装しようとしたときに必ず詰まる。

```tsx
function DataComponent() {
  const data = useSomeData(); // このhookがPromiseをthrowする
  return <div>{data}</div>;
}

function App() {
  return (
    <Suspense fallback={<Spinner />}>
      <DataComponent />
    </Suspense>
  );
}
```

Suspenseは、子コンポーネントがレンダリング中にPromiseをthrowしたら、それをキャッチしてfallbackを表示します。Promiseが解決したら、子を再レンダリングする。つまり、Suspenseの内側では「データが取得できた前提」でコードを書けばいい。

実装を見ると、Reactは内部でPromiseをキャッチして、`.then()`で再レンダリングをスケジュールしてます（https://github.com/facebook/react/blob/main/packages/react-reconciler/src/ReactFiberWorkLoop.js#L1856 あたり）。要するに、例外機構を使ってフロー制御してる。

[^1]: 正確には、Reactが認識できる特殊なPromiseをthrowします。普通のPromiseをthrowしてもReactは無視します。

## V1: 素朴な実装（失敗例）

じゃあ実際にやってみよう。最初はこう書きました：

```tsx
// V1: 素朴な実装
function fetchData(url: string) {
  return fetch(url).then(res => res.json());
}

function useFetchData(url: string) {
  const [data, setData] = useState(null);

  useEffect(() => {
    fetchData(url).then(setData);
  }, [url]);

  if (data === null) {
    throw fetchData(url); // Promiseをthrow
  }

  return data;
}
```

これを使うと...動かない。というか、無限ループします。

なんでかというと、`data === null`のたびに**新しいPromise**を作ってthrowしてるから。Promiseが解決しても、再レンダリング時にまた別のPromiseをthrowする。で、また解決して、また新しいPromise作って...の繰り返し。

これを見て「なんで？」と思った方、正解です。Suspenseは「同じPromise」が解決されるのを待つ仕組みなので、毎回違うPromiseをthrowすると永遠に終わらない。

## V2: Promiseをキャッシュする

次に、Promiseをキャッシュするように修正しました：

```tsx
// V2: Promiseをキャッシュ
const cache = new Map<string, Promise<any>>();

function useFetchData(url: string) {
  const [data, setData] = useState(null);

  if (!cache.has(url)) {
    const promise = fetchData(url).then(result => {
      setData(result);
      return result;
    });
    cache.set(url, promise);
  }

  if (data === null) {
    throw cache.get(url);
  }

  return data;
}
```

これで動く...ように見えるけど、実は問題があります。

`setData`を呼んでるのがPromiseの`.then()`の中。つまり、非同期で呼ばれる。でも、Suspenseが再レンダリングをトリガーするのもPromise解決時。結果、**レースコンディション**が起きます[^2]。

具体的には、Suspenseが再レンダリングをスケジュールするタイミングと、`setData`でstateを更新するタイミングが前後する可能性がある。運が悪いと、再レンダリング時に`data`がまだ`null`で、また同じPromiseをthrowして...みたいな状況になる。

これ、実際に起きたんですよね。開発環境では動いてたのに、本番でたまに無限ループする。最初はReactのバグかと思った。

[^2]: React 18のStrictモードだと、開発環境で意図的にこういうレースコンディションを誘発してくれます。ありがたい。

## V3: キャッシュに結果も保存

というわけで、Promiseだけじゃなくて、解決後の結果もキャッシュする必要があります：

```tsx
// V3: 結果もキャッシュ
type CacheEntry<T> =
  | { status: 'pending'; promise: Promise<T> }
  | { status: 'resolved'; data: T };

const cache = new Map<string, CacheEntry<any>>();

function useFetchData<T>(url: string): T {
  if (!cache.has(url)) {
    const promise = fetchData(url).then(result => {
      cache.set(url, { status: 'resolved', data: result });
      return result;
    });
    cache.set(url, { status: 'pending', promise });
  }

  const entry = cache.get(url)!;

  if (entry.status === 'pending') {
    throw entry.promise;
  }

  return entry.data;
}
```

ようやくまともに動くようになった。stateを使わずに、キャッシュで完結してます。

ここでのポイントは、**Promiseとデータの状態を一箇所で管理**すること。`useState`を使うと、Reactのstate更新タイミングとSuspenseの再レンダリングタイミングがずれる可能性があるので、自前のMapで管理する方が確実です。

ちなみに、この実装はReact QueryやSWRの内部と似たようなことをやってます（#2851とか見ると面白い）。

## ErrorBoundaryでエラーハンドリング

さて、すでに何か勉強した気になったかもしれませんが、ここまではローディングの話だけ。エラーハンドリングはどうするか。

ErrorBoundaryは、子コンポーネントがレンダリング中にエラーをthrowしたらキャッチしてfallback UIを表示するやつです：

```tsx
class ErrorBoundary extends React.Component<
  { children: React.ReactNode; fallback: React.ReactNode },
  { hasError: boolean; error: Error | null }
> {
  state = { hasError: false, error: null };

  static getDerivedStateFromError(error: Error) {
    return { hasError: true, error };
  }

  render() {
    if (this.state.hasError) {
      return this.props.fallback;
    }
    return this.props.children;
  }
}
```

これをSuspenseと組み合わせると、こうなります：

```tsx
<ErrorBoundary fallback={<ErrorMessage />}>
  <Suspense fallback={<Spinner />}>
    <DataComponent />
  </Suspense>
</ErrorBoundary>
```

ローディング中は`<Spinner />`、エラーが起きたら`<ErrorMessage />`、データ取得できたら`<DataComponent />`が表示される。DataComponentの中では、`isLoading`も`error`も気にしなくていい。

## V4: エラーもキャッシュする

でも、さっきの`useFetchData`はエラーハンドリングしてないです。修正します：

```tsx
// V4: エラーもキャッシュ
type CacheEntry<T> =
  | { status: 'pending'; promise: Promise<T> }
  | { status: 'resolved'; data: T }
  | { status: 'rejected'; error: Error };

function useFetchData<T>(url: string): T {
  if (!cache.has(url)) {
    const promise = fetchData(url)
      .then(result => {
        cache.set(url, { status: 'resolved', data: result });
        return result;
      })
      .catch(error => {
        cache.set(url, { status: 'rejected', error });
        throw error; // ErrorBoundaryに伝播させる
      });
    cache.set(url, { status: 'pending', promise });
  }

  const entry = cache.get(url)!;

  if (entry.status === 'pending') {
    throw entry.promise;
  }

  if (entry.status === 'rejected') {
    throw entry.error;
  }

  return entry.data;
}
```

これでエラーが起きたら、ErrorBoundaryがキャッチしてfallbackを表示します。しかもエラーもキャッシュされるので、再レンダリングのたびにリクエストが飛ばない。

ただし、実際にこれを使ってみると「エラーから回復できない」問題に気づきます。一度エラーになったら、ページをリロードするまでずっとエラー画面。これは不便。

## リトライ機能を追加

エラーから回復するには、キャッシュをクリアする必要があります：

```tsx
function clearCache(url: string) {
  cache.delete(url);
}

function ErrorMessage({ error, onRetry }: { error: Error; onRetry: () => void }) {
  return (
    <div>
      <p>エラーが発生しました: {error.message}</p>
      <button onClick={onRetry}>リトライ</button>
    </div>
  );
}
```

でも、ErrorBoundaryのfallbackにはpropsを渡せない。じゃあどうするか。

答えは、**ErrorBoundaryにリセット機能を持たせる**：

```tsx
class ErrorBoundary extends React.Component<{
  children: React.ReactNode;
  fallback: (error: Error, reset: () => void) => React.ReactNode;
}> {
  state = { hasError: false, error: null };

  static getDerivedStateFromError(error: Error) {
    return { hasError: true, error };
  }

  reset = () => {
    this.setState({ hasError: false, error: null });
  };

  render() {
    if (this.state.hasError) {
      return this.props.fallback(this.state.error!, this.reset);
    }
    return this.props.children;
  }
}
```

使う側はこう：

```tsx
<ErrorBoundary
  fallback={(error, reset) => (
    <ErrorMessage
      error={error}
      onRetry={() => {
        clearCache(url); // キャッシュをクリア
        reset(); // ErrorBoundaryをリセット
      }}
    />
  )}
>
  <Suspense fallback={<Spinner />}>
    <DataComponent />
  </Suspense>
</ErrorBoundary>
```

これでリトライが機能します。ボタンを押すと、キャッシュがクリアされて、ErrorBoundaryがリセットされて、再レンダリングが走る。再レンダリング時にキャッシュがないので、新しいリクエストが飛ぶ。

## 実務で使うときの注意点

ここまでの実装で基本的な動作は実現できてます。でも、実務で使うにはいくつか考慮が必要。

**並列フェッチの扱い**

複数のコンポーネントが同時にフェッチする場合、それぞれが独立してSuspendします：

```tsx
<Suspense fallback={<Spinner />}>
  <UserProfile userId={userId} />
  <UserPosts userId={userId} />
</Suspense>
```

この場合、`UserProfile`と`UserPosts`が別々にデータをフェッチして、両方とも準備できたら表示される。いいですね。

でも、片方のフェッチがもう片方に依存してる場合は工夫が必要です。例えば、ユーザー情報を取得してから、そのユーザーの投稿を取得する、みたいな。

この場合、Suspenseを入れ子にするか、依存関係を明示的に扱う必要があります。個人的には、できるだけ依存しないように設計する方が楽。

**キャッシュの無効化タイミング**

さっきの実装だと、一度キャッシュしたら永遠に使い回される。実際には、時間が経ったら再フェッチしたいですよね。

React QueryやSWRは`staleTime`みたいな設定があって、「このキャッシュは○秒で古くなる」って指定できます。自分で実装するなら、タイムスタンプをキャッシュに含めて、古くなったら再フェッチする仕組みを入れる必要があります。

ただ、これを真面目にやり始めると、結局React Queryを使った方が早い。自分で全部実装するのは学習目的ならいいけど、実務だと車輪の再発明になりがち。

**Server Componentsとの関係**

React Server Components（RSC）が登場してから、「Suspenseはクライアントだけじゃなくサーバーでも使える」って話になってます。

RSCでは、サーバー側でデータをフェッチしてから、結果をクライアントに送信します。Suspenseは、サーバー側でのデータフェッチ待ちを表現するのにも使われる。

ただし、サーバーとクライアントでSuspenseの挙動は微妙に違います。サーバーではストリーミングレスポンスの中で「まだ準備できてない部分」を表現するために使われる。クライアントでは、非同期処理をthrowして待つ仕組み。

この辺りは別の話題なので、今回は深掘りしないです。気になる方はNext.js 13以降のドキュメントを読むといいかも。

## まとめ

結局、SuspenseとErrorBoundaryで宣言的に書くメリットは何か。

コンポーネントが「データがある前提」で書けること。`isLoading`や`error`のstateを各所に散らばせなくて済むこと。ローディングやエラーハンドリングのUIを、コンポーネントツリーの適切な場所に配置できること。

でも、実装は意外と面倒です。Promiseのキャッシュ、エラーのキャッシュ、リトライ機能、キャッシュの無効化...って考えると、React QueryやSWRを使った方が圧倒的に楽。

個人的には、小規模なプロジェクトや学習目的なら自分で実装してみるのも面白いと思います。でも、実務で使うなら既存のライブラリを使う方が無難。

あと、この記事では触れなかったけど、Concurrent Renderingとの相性とか、`useTransition`との組み合わせとか、まだまだ奥が深い話題があります。そこまで行くと長くなるので、また別の機会に。
