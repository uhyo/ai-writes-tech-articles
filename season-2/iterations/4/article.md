---
title: "useTransitionで変わるReactのUX設計"
emoji: "⚡"
type: "tech"
topics: ["react", "typescript", "frontend", "performance"]
published: true
---

皆さんこんにちは。React 18で入ったConcurrent Renderingの話、もう触ってる人も多いと思う。で、今回はその中でも`useTransition`について書きます。というのも、このフック、最初「どこで使うんだよ」と思ってたんだけど、実プロジェクトで使ったら結構効いた。

## useTransitionって何なのか

簡単に言えば、状態更新の優先度を下げるフック。React 18のConcurrent Rendering機能の一部で、重い処理でUIがブロックされるのを防ぎます。

```typescript
const [isPending, startTransition] = useTransition();

startTransition(() => {
  setSearchQuery(value); // この更新は低優先度
});
```

返り値は2つ。`isPending`は遷移中かどうかのフラグで、`startTransition`がその低優先度更新をラップする関数。この中に書いた状態更新は、他の緊急な更新（入力フィールドの変更とか）があれば中断される。

従来のReactでは全部の状態更新が同じ優先度で処理されていました。検索ボックスに文字入力すると、その文字を表示する更新と検索結果をフィルタリングする更新が同時に走って、結果リストが数千件あると入力がカクつく。useTransitionを使うと「入力表示は最優先、フィルタリングは後回し」という制御ができます。

## 実際の使いどころ

最近やったプロジェクトで、管理画面の巨大なテーブル（5000行くらい）にフィルタ機能を付けました。最初普通に書いたら、入力の度に画面がフリーズして使い物にならない。

```typescript
// 最初のコード
function TablePage() {
  const [filterText, setFilterText] = useState('');
  const filteredData = data.filter(item =>
    item.name.includes(filterText)
  );

  return (
    <div>
      <input
        value={filterText}
        onChange={e => setFilterText(e.target.value)}
      />
      <Table data={filteredData} />
    </div>
  );
}
```

これ、文字入力の度に5000件全部フィルタリングが走る。キーを押してから画面に反映されるまで500msとかかかって、タイピングがガタガタになる。

そこで、useTransitionを使ってみた。

```typescript
function TablePage() {
  const [filterText, setFilterText] = useState('');
  const [deferredFilter, setDeferredFilter] = useState('');
  const [isPending, startTransition] = useTransition();

  const filteredData = data.filter(item =>
    item.name.includes(deferredFilter)
  );

  const handleChange = (e: ChangeEvent<HTMLInputElement>) => {
    const value = e.target.value;
    setFilterText(value); // 即座に反映

    startTransition(() => {
      setDeferredFilter(value); // フィルタは低優先度
    });
  };

  return (
    <div>
      <input
        value={filterText}
        onChange={handleChange}
        style={{ opacity: isPending ? 0.7 : 1 }}
      />
      {isPending && <Spinner />}
      <Table data={filteredData} />
    </div>
  );
}
```

inputの値（`filterText`）は即座に更新、フィルタリングに使う値（`deferredFilter`）は低優先度で更新する。結果、キーボード入力はサクサク動いて、フィルタ結果が少し遅れて表示される感じになった。体感的には普通のインクリメンタルサーチと変わらない。

ちなみに最初、両方の状態を1つにまとめようとして「あ、これinputの値がバグる」となった。inputのvalueには即座に反映される状態を渡さないと、タイピングが遅延して見える。

## useDeferredValueとの違い

React 18には`useDeferredValue`というフックもあって、これも似たようなことができます。

```typescript
const deferredValue = useDeferredValue(filterText);
const filteredData = data.filter(item =>
  item.name.includes(deferredValue)
);
```

こっちは値そのものを遅延させる感じ。useTransitionが「この更新を遅延」なのに対して、useDeferredValueは「この値を使った計算を遅延」という違いがある。

個人的には、状態更新を制御したいならuseTransition、外から渡された値を遅延させたいならuseDeferredValueという使い分けをしています。後者は親コンポーネントからpropsで値が来る場合に便利です。

実装見てないので推測だけど、内部的には似たような仕組みを使ってそう。

## Suspenseとの組み合わせ

useTransitionの面白いところは、Suspenseと組み合わせて使える点です。これで「ローディング表示を遅延させる」みたいなことができます。

```typescript
function SearchResults() {
  const [query, setQuery] = useState('');
  const [deferredQuery, setDeferredQuery] = useState('');
  const [isPending, startTransition] = useTransition();

  const handleSearch = (value: string) => {
    setQuery(value);
    startTransition(() => {
      setDeferredQuery(value);
    });
  };

  return (
    <div>
      <SearchBox value={query} onChange={handleSearch} />
      <Suspense fallback={<Skeleton />}>
        <Results query={deferredQuery} />
      </Suspense>
    </div>
  );
}
```

ここで`Results`コンポーネントがSuspenseで中断する場合（データフェッチ中とか）、startTransition内の更新なら古いコンテンツを表示し続けてくれる。通常のSuspenseだと即座にfallbackに切り替わるけど、useTransitionを使うと「新しいデータが準備できるまで古いのを見せとく」という挙動になる。

ただこれ、まだ本番で使ってない。Suspenseでデータフェッチやるパターン自体があんまり普及してないのと、Server Componentsとの兼ね合いがまだよくわかってない。そのうち試したい。

## パフォーマンス計測

useTransitionを入れる前と後で、Chrome DevToolsのPerformanceタブで計測してみた。

入力1文字あたりのブロッキング時間:
- Before: 450ms (Main thread blocked)
- After: 50ms (入力処理のみ)

フィルタリング自体の処理時間は変わってないけど、メインスレッドがブロックされる時間が激減しています。これがConcurrent Renderingの威力です。

ちなみに最初、この計測でフレームレートも測ろうとしたんだけど、入力イベント自体が断続的なので意味のある数値が取れなくて諦めました。結局体感で判断しています。

## 注意点というか制約

useTransitionにもいくつか制約があります。

まず、startTransition内で呼べるのは状態更新関数だけ。非同期処理とか副作用を直接書くのはNG。

```typescript
// これはダメ
startTransition(async () => {
  const data = await fetchData();
  setData(data);
});
```

あと、useTransitionで遅延できるのはReactの状態更新だけです。DOM操作とか外部ライブラリの処理には効きません。当たり前だけど、最初これ勘違いしていました。

それと、低優先度更新が実際にいつ実行されるかは保証されない。状況によっては即座に実行されることもあるし、かなり遅れることもある。「絶対に遅延させたい」みたいな要件には向いてない。

## 他のアプローチとの比較

useTransition以外にも、入力のカクつきを防ぐ方法はいくつかあります。

**Debounce/Throttle**: これは昔からある手法で、lodashとかを使って実装する。useTransitionとの違いは、こっちは「一定時間待ってから処理」なのに対して、useTransitionは「処理を中断可能にする」という点。Debounceは待ち時間が発生するけど、useTransitionは即座に処理を始めつつ中断できる。

**Web Workers**: 重い計算を別スレッドでやる方法。これは確実にメインスレッドをブロックしないけど、Worker APIのオーバーヘッドとか、データのserializeとかが面倒。小規模なフィルタリングくらいならuseTransitionで十分。

**Virtual Scrolling**: テーブルが巨大な場合、表示してる部分だけレンダリングする手法。react-windowとかのライブラリがある。これとuseTransitionは併用できる。というか併用した方がいい。(#5739で議論されてた気がする)

## まとめ

useTransition、正直最初は「いつ使うねん」と思ってたけど、使いどころはちゃんとあります。特に大量のデータを扱うUIでは効果がわかりやすい。

ただ、Concurrent Rendering自体がまだ発展途上な感じもあって、ベストプラクティスが確立されてない部分も多い。Server Componentsとの組み合わせとか、Suspenseとの使い分けとか、まだ手探り状態。

個人的には、まずパフォーマンス問題が出てから試すくらいでいいと思っています。最初から全部useTransitionで書く必要はないし、むしろ複雑になるだけ。プロファイラで計測して、本当にブロッキングが問題になってるところにピンポイントで入れる感じです。

あと、iOS Safariでの挙動がたまに怪しい気がするんだけど、これはまだちゃんと調べてない。Android Chromeでは問題なく動いてるので、多分大丈夫だとは思うんだけど。
