---
title: "React 19のuseフックは本当にPromiseを直接扱えるのか"
emoji: "🔄"
type: "tech"
topics: ["react", "typescript", "javascript"]
published: true
---

皆さんこんにちは。React 19のリリースが近づいてきて、新しく追加される**useフック**が話題になっています。公式ドキュメントを見ると「Promiseを直接扱える」と書かれていますが、この「直接」という言葉が具体的に何を意味するのか気になったので調べてみました。ReactのRFC[^rfc]でも議論されていた機能ですが、実際の挙動を確認してみます。

[^rfc]: React Working Groupでは、useフックの仕様について長い議論が行われていました。特にエラーハンドリングとサスペンションの統合が課題でした。

## useの基本的な挙動

まずは簡単な例から見ていきます。useフックは以下のように使えます。

```tsx
function UserProfile({ userIdPromise }: { userIdPromise: Promise<string> }) {
  const userId = use(userIdPromise);
  return <div>User ID: {userId}</div>;
}
```

このコードを実行すると、Promiseが解決されるまでコンポーネントが**サスペンド**します。Promiseが解決されたら、その値が返ってきます。

一見すると`await`と同じように見えますが、重要な違いがあります。useはフック内でしか使えませんが、条件分岐やループの中でも使えます[^1]。

[^1]: 従来のフックルールでは条件分岐の中でフックを呼ぶことは禁止されていましたが、useは例外的にこのルールが緩和されています。

```tsx
function Component({ shouldFetch }: { shouldFetch: boolean }) {
  if (shouldFetch) {
    const data = use(fetchData());
    return <div>{data}</div>;
  }
  return <div>No data</div>;
}
```

これが動作するのは興味深いです。

## Promiseのrejectionとサスペンション

ここからが本題です。useがPromiseを「直接扱える」というのは、具体的にどういう挙動を指すのでしょうか。

まず、Promiseがrejectされた場合の挙動を見てみます。

```tsx
function ErrorProneComponent() {
  const data = use(Promise.reject(new Error("Failed to fetch")));
  return <div>{data}</div>;
}
```

このコードを実行すると、どうなるでしょうか。試してみたところ、コンポーネントは**エラー境界**にエラーをスローします。つまり、useはPromiseのrejectionをキャッチして、Reactのエラーハンドリング機構に渡しているようです。

これは重要な発見でした。useは単にPromiseの値を取り出すだけでなく、エラーハンドリングも統合されています。

### サスペンションの仕組み

次に、サスペンションがどのように機能するかを深掘りしてみます。Reactのドキュメントには「Promiseが解決されるまでサスペンドする」と書かれていますが、これは内部的にどう実装されているのでしょうか。

実験として、以下のようなコードを書いてみました。

```tsx
let promiseInstance: Promise<string> | null = null;

function TestComponent() {
  if (!promiseInstance) {
    promiseInstance = new Promise((resolve) => {
      setTimeout(() => resolve("Hello"), 1000);
    });
  }

  const result = use(promiseInstance);
  console.log("Rendered with:", result);
  return <div>{result}</div>;
}
```

このコードを実行すると、興味深い挙動が観察できます。コンポーネントは最初にレンダリングを試み、Promiseがpendingの状態だとサスペンドします。1秒後にPromiseが解決されると、再度レンダリングが走り、今度は値が取得できます。

筆者はここの挙動が一番興味深かったのですが、Reactはどうやってコンポーネントを「再開」しているのでしょうか。

Reactのソースコードを追ってみると、useフックはPromiseがpendingの場合に特殊な例外をスローすることで、Reactにサスペンドを通知しているようです。Reactはこの例外をキャッチして、Promiseにthenハンドラを登録します。Promiseが解決されたら、そのハンドラ内でコンポーネントの再レンダリングをスケジュールします。

```typescript
// 実際のReactの内部実装の簡略化版（推測）
function use<T>(promise: Promise<T>): T {
  const status = promise._status;

  if (status === 'fulfilled') {
    return promise._value;
  }

  if (status === 'rejected') {
    throw promise._reason;
  }

  // pendingの場合
  throw promise.then(
    (value) => {
      promise._status = 'fulfilled';
      promise._value = value;
    },
    (reason) => {
      promise._status = 'rejected';
      promise._reason = reason;
    }
  );
}
```

この実装を見ると、useは確かにPromiseを「直接」扱っていると言えそうです。ただし、Promiseオブジェクト自体に状態を追加している可能性があります。

### 同じPromiseを複数回useした場合

では、同じPromiseインスタンスを複数のコンポーネントでuseしたらどうなるでしょうか。

```tsx
const sharedPromise = fetch('/api/user').then(res => res.json());

function ComponentA() {
  const data = use(sharedPromise);
  return <div>A: {data.name}</div>;
}

function ComponentB() {
  const data = use(sharedPromise);
  return <div>B: {data.name}</div>;
}
```

このパターンを試してみたところ、なんと両方のコンポーネントが同じPromiseを共有できました。Promiseは一度だけ解決され、その結果が両方のコンポーネントで使われます。

これは従来のuseEffectでfetchを行う場合とは大きく異なります。useEffectでは各コンポーネントが個別にfetchを実行するため、重複したリクエストが発生する可能性がありました。

筆者の考えでは、この挙動がuseフックの最大の利点だと思います。**Promiseの共有**によって、コンポーネント間で自然にリクエストの重複を避けられます。

### Promiseの再利用と再実行

さらに実験を続けます。コンポーネントが再レンダリングされたとき、useに渡されたPromiseはどうなるのでしょうか。

```tsx
function DynamicComponent({ userId }: { userId: string }) {
  const userData = use(fetchUser(userId));
  return <div>{userData.name}</div>;
}
```

userIdが変わった場合、fetchUserが返すPromiseは新しいインスタンスになります。この場合、useは新しいPromiseを検出して、再度サスペンドします。

では、同じPromiseインスタンスが渡され続けた場合はどうでしょうか。

```tsx
const cachedPromise = fetchUser('123');

function CachedComponent() {
  const [count, setCount] = useState(0);
  const userData = use(cachedPromise);

  return (
    <div>
      {userData.name}
      <button onClick={() => setCount(count + 1)}>
        Rerender ({count})
      </button>
    </div>
  );
}
```

このコードを実行すると、ボタンをクリックして再レンダリングが発生しても、Promiseは再実行されません。useは既に解決済みのPromiseを認識して、即座に値を返します。

これは重要な発見です。useはPromiseの状態を記憶しているため、不要な再実行を避けられます。

### エラー境界との相互作用

次に、エラー境界とuseの相互作用を見てみます。

```tsx
class ErrorBoundary extends React.Component {
  state = { hasError: false };

  static getDerivedStateFromError(error: Error) {
    return { hasError: true };
  }

  render() {
    if (this.state.hasError) {
      return <div>Error occurred</div>;
    }
    return this.props.children;
  }
}

function FailingComponent() {
  const data = use(Promise.reject(new Error("API Error")));
  return <div>{data}</div>;
}

// 使用
<ErrorBoundary>
  <Suspense fallback={<div>Loading...</div>}>
    <FailingComponent />
  </Suspense>
</ErrorBoundary>
```

このコードを実行すると、FailingComponentはエラー境界にエラーをスローします。興味深いのは、SuspenseとErrorBoundaryの両方が必要だという点です。

試しにSuspenseを外してみました。

```tsx
<ErrorBoundary>
  <FailingComponent />
</ErrorBoundary>
```

すると、Reactは警告を出します。「Suspense境界の外でuseを使用しています」というメッセージが表示されました。つまり、useを使う場合は必ずSuspenseで囲む必要があるようです。

でも、ちょっと待ってください。エラーが発生する場合でもSuspenseが必要なのでしょうか。Promiseがrejectされた場合、サスペンドは発生しないはずです。

実際に試してみたところ、予想外の結果になりました。Promiseがpendingからrejectedに遷移する場合、一瞬だけサスペンド状態になることがあります。Reactが内部的にPromiseの状態を確認する際に、一時的にサスペンドが発生するようです。

この挙動は筆者にとって予想外でした。エラーケースでもSuspenseが関与するとは思っていませんでした。

## 従来のアプローチとの比較

useフックが登場する前は、データフェッチにuseEffectを使っていました。簡単に比較すると、useEffectではコンポーネントが一度マウントされてから非同期処理が開始されますが、useではレンダリング中に直接Promiseを扱えます。これにより、Suspenseとの統合がスムーズになっています。

## まとめ

React 19のuseフックは、確かにPromiseを「直接」扱えると言えます。内部的には例外をスローすることでサスペンションを実現し、Promiseの状態を追跡して効率的にキャッシュします。

筆者としては、Promiseの共有によるリクエスト重複の回避が最も実用的な利点だと感じました。ただし、エラーハンドリングとサスペンションの相互作用については、まだ完全に理解できていない部分もあります。

特に、Promiseがrejectedに遷移する際の一時的なサスペンドについては、もう少し調査が必要かもしれません。筆者はまだ試していないのですが、useTransitionと組み合わせた場合の挙動も気になっています。

useフックの導入により、Reactのデータフェッチパターンは大きく変わりそうです。これからどのようなベストプラクティスが確立されていくか、見守っていきたいと思います。
