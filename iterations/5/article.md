---
title: "ReactのuseRefの実践的な使い方：DOM操作以外の活用パターン"
emoji: "🔗"
type: "tech"
topics: ["react", "typescript", "javascript"]
published: true
---

## useRefの本質を理解する

`useRef`って、公式ドキュメントや入門記事だと「DOM要素への参照を保持するためのフック」みたいに説明されることが多いんですよね。確かに間違いじゃないんだけど、それだけだとuseRefの真価の半分も理解できてない気がします。

useRefの本質は「**再レンダリングを引き起こさずに値を保持し続ける箱**」です。この箱の中身を変更しても、Reactのレンダリングサイクルには一切影響しない。これが重要なポイントで、DOM参照はあくまで応用例の一つに過ぎません。

公式のTypeScript型定義を見てみると：

```typescript
interface MutableRefObject<T> {
  current: T;
}

function useRef<T>(initialValue: T): MutableRefObject<T>;
```

シンプルですよね。`current`プロパティを持つオブジェクトを返すだけ。このオブジェクトはコンポーネントのライフサイクル全体で同一のインスタンスが保持されます。

## 前回の値を保持する

個人的に一番使うのがこれ。前回のpropsやstateの値を記憶しておきたい場面って、実務だと結構あります。

```typescript
function UserProfile({ userId }: { userId: string }) {
  const prevUserIdRef = useRef<string>();

  useEffect(() => {
    if (prevUserIdRef.current !== undefined && prevUserIdRef.current !== userId) {
      // ユーザーが切り替わった時だけデータをフェッチ
      fetchUserData(userId);
    }
    prevUserIdRef.current = userId;
  }, [userId]);

  // ...
}
```

この実装、`useState`を使っても似たようなことはできるんだけど、前回の値を保持するためだけに余計なレンダリングが走るのは無駄ですよね。useRefなら`current`を更新してもレンダリングは発生しません。

去年、社内のダッシュボード（リアルタイムで色々更新されるやつ）を作ってた時、最初は全部stateで管理してたんです。そしたら1秒間に何度もレンダリングが走って、ブラウザがもっさりして。で、「本当にレンダリングが必要な値」と「単に記憶しておきたいだけの値」を分離したら、パフォーマンスが劇的に改善しました（ちなみにこのプロジェクト、元々はReduxで全部管理する設計だったのが、途中でチームの誰かが「Redux重すぎる」って言い出して、結局シンプルなhooksベースになった）。

## タイマーIDやsubscriptionの保持

`setInterval`や`setTimeout`のIDを保持する時、useRefは最適です。というか、これ以外の選択肢があまり思いつかない。

```typescript
function Timer() {
  const [count, setCount] = useState(0);
  const intervalIdRef = useRef<NodeJS.Timeout>();

  const startTimer = () => {
    if (intervalIdRef.current) return; // 既に動いてたら何もしない

    intervalIdRef.current = setInterval(() => {
      setCount(c => c + 1);
    }, 1000);
  };

  const stopTimer = () => {
    if (intervalIdRef.current) {
      clearInterval(intervalIdRef.current);
      intervalIdRef.current = undefined;
    }
  };

  useEffect(() => {
    return () => {
      if (intervalIdRef.current) {
        clearInterval(intervalIdRef.current);
      }
    };
  }, []);

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={startTimer}>Start</button>
      <button onClick={stopTimer}>Stop</button>
    </div>
  );
}
```

この実装の何が良いかって、`intervalIdRef.current`を更新してもコンポーネントが再レンダリングされないこと。stateで管理すると、タイマーIDを更新する度にレンダリングが走るんで無駄なんですよね。

WebSocket connectionとかも同じパターンで管理できます：

```typescript
function useWebSocket(url: string) {
  const wsRef = useRef<WebSocket>();
  const [messages, setMessages] = useState<string[]>([]);

  useEffect(() => {
    wsRef.current = new WebSocket(url);

    wsRef.current.onmessage = (event) => {
      setMessages(prev => [...prev, event.data]);
    };

    return () => {
      wsRef.current?.close();
    };
  }, [url]);

  const sendMessage = (message: string) => {
    wsRef.current?.send(message);
  };

  return { messages, sendMessage };
}
```

## 最新のpropsやstateにアクセスする

これ、意外と知られてないんだけど、useRefを使うとclosure問題を回避できる場面があります。

例えば、イベントハンドラやsubscription callbackの中で、最新のstateにアクセスしたい時：

```typescript
function ChatRoom({ roomId }: { roomId: string }) {
  const [message, setMessage] = useState('');
  const messageRef = useRef(message);

  // 常に最新のmessageをrefに保持
  useEffect(() => {
    messageRef.current = message;
  }, [message]);

  useEffect(() => {
    const interval = setInterval(() => {
      // ここで最新のmessageにアクセスできる
      console.log('Current message:', messageRef.current);
    }, 5000);

    return () => clearInterval(interval);
  }, []); // 依存配列は空でOK

  return (
    <input
      value={message}
      onChange={(e) => setMessage(e.target.value)}
    />
  );
}
```

依存配列に`message`を入れると、messageが変わる度にintervalが再作成されて無駄なんです。でもrefを使えば、effectは一度だけ実行されて、常に最新の値にアクセスできる。

Reactの公式ドキュメントには載ってないんだけど、実はReactのソースコード（https://github.com/facebook/react/blob/main/packages/react-reconciler/src/ReactFiberHooks.js#L1900あたり）を見ると、React内部でもこのパターンが使われてます。特にevent handlerの実装で。

:::details 余談：React 18のuseEventとの関係

React 18で提案されてた`useEvent`フック（現在はまだRFCの段階）は、まさにこの問題を解決するために設計されてます。useEventを使えば、依存配列に入れなくても常に最新の値にアクセスできるイベントハンドラを作れる。

```typescript
function ChatRoom({ roomId }) {
  const [message, setMessage] = useState('');

  const onMessage = useEvent(() => {
    // messageは常に最新
    console.log(message);
  });

  useEffect(() => {
    const interval = setInterval(onMessage, 5000);
    return () => clearInterval(interval);
  }, []);
}
```

ただし、useEventはまだ実装されてない（2024年10月時点）ので、現状はuseRefで代用するしかありません。
:::

## レンダリングを跨いだカウンター

レンダリング回数をカウントしたい時とか、何かの実行回数を数えたい時。これもuseRefの出番です。

```typescript
function ExpensiveComponent({ data }: { data: unknown }) {
  const renderCountRef = useRef(0);

  renderCountRef.current += 1;

  console.log(`Rendered ${renderCountRef.current} times`);

  // 実際の処理...
}
```

stateでやろうとすると、カウントを更新する度にレンダリングが走って、無限ループになります。useRefなら安全。

開発中のデバッグとか、パフォーマンスの問題を追跡する時に便利なんですよね。「このコンポーネント、何回レンダリングされてるんだ？」って気になった時、サッと仕込める。

## 高価な初期化処理のメモ化

`useMemo`や`useCallback`は依存配列が変わると再計算されるんだけど、本当に一度だけ初期化したい処理ってあります。

```typescript
function DataProcessor() {
  const processorRef = useRef<HeavyProcessor>();

  if (!processorRef.current) {
    // 初回レンダリング時だけ実行される
    processorRef.current = new HeavyProcessor({
      config: loadComplexConfig(),
      cache: new Map(),
      // 重い初期化処理
    });
  }

  return (
    <div>
      {/* processorを使った処理 */}
    </div>
  );
}
```

この実装、`useMemo(() => new HeavyProcessor(...), [])`でも似たことができるんだけど、React 18のConcurrent Renderingでは`useMemo`の結果が捨てられる可能性があるって公式ドキュメントに書いてあるんです。useRefなら確実に一度だけ実行されます。

試しにStrict Modeで両方試してみたんだけど、useMemoだと開発環境で2回実行されることがありました。useRefは確実に1回だけ。

## まとめ

useRefって、DOM参照だけじゃなくて色々使えるんですよね。再レンダリングを引き起こさない値の保持、これが本質。

個人的には、「レンダリングに影響しない値」と「レンダリングに必要な値」を明確に分けることが、Reactのパフォーマンス最適化で一番重要だと思ってます。なんでもかんでもstateに突っ込むと、無駄なレンダリングが増えるだけ。

ただ、useRefを使いすぎるのも考えもので、本当にレンダリングが必要な値までrefに入れちゃうと、UIが更新されなくてバグになります。その辺のバランス感覚は、実際に書いて失敗して身につけるしかない気がします。
