---
title: "useCallbackとuseMemoの使い分けで迷ったときに考えること"
emoji: "🤔"
type: "tech"
topics: ["react", "typescript", "performance"]
published: true
---

## 最初に書いたコードがバグってた話

最近プロジェクトでパフォーマンス改善をやってて、useCallbackとuseMemoをそこら中に配置してたんです。で、こんなコード書いてた。

```typescript
const FilteredList: React.FC<Props> = ({ items, filter }) => {
  const filteredItems = useMemo(() => {
    return items.filter(item => item.category === filter);
  }, [items, filter]);

  const handleClick = useCallback((id: string) => {
    console.log('clicked', id);
  }, []);

  return (
    <div>
      {filteredItems.map(item => (
        <Item key={item.id} data={item} onClick={handleClick} />
      ))}
    </div>
  );
};
```

これで快適になったと思ってたら、あるとき急にクリックイベントが反応しなくなった。んで、よく見たら`handleClick`の依存配列が空っぽで、クロージャの中でキャプチャしてるはずの値が古いまま。当たり前だけどバグです。

```typescript
// 修正版
const handleClick = useCallback((id: string) => {
  console.log('clicked', id);
  onItemSelect(id); // これが動いてなかった
}, [onItemSelect]); // 依存追加
```

で、これ直したあとに気づいたんだけど、そもそもこのuseCallback、必要だったのか？

## useCallbackって何のためにあるのか

公式ドキュメント読むと「関数をメモ化する」って書いてあって、最初はこれで納得してた。でも実際に使い始めると、いつ使えばいいのか全然分からなくなる。

結論から言うと、useCallbackが意味を持つのは次の2つのケースだけ。

1. 子コンポーネントがReact.memoでラップされてて、その子に関数をpropsとして渡す場合
2. useEffectやuseMemoの依存配列に関数を入れたい場合

それ以外で使っても、メモ化のコスト分だけ遅くなる。筆者も最初これ知らなくて、あらゆる関数をuseCallbackで包んでパフォーマンス悪化させたことがある[^1]。

[^1]: React Profilerで計測したら、useCallbackを外した方が速かった。メモ化自体にもコストがかかるので、不要な場所で使うと逆効果になる。

### React.memoと組み合わせる例

```typescript
const ExpensiveChild = React.memo<ChildProps>(({ onUpdate, data }) => {
  // 重い処理がここにある
  const result = heavyCalculation(data);
  return <div onClick={onUpdate}>{result}</div>;
});

const Parent = () => {
  const [count, setCount] = useState(0);

  // これ、最初useCallbackなしで書いてた
  const handleUpdate = () => {
    console.log('update');
  };

  return (
    <>
      <button onClick={() => setCount(count + 1)}>Increment</button>
      <ExpensiveChild onUpdate={handleUpdate} data={count} />
    </>
  );
};
```

このコードだと、`count`が変わるたびに`handleUpdate`の参照が変わって、React.memoが無意味になる。で、useCallbackで包む。

```typescript
const handleUpdate = useCallback(() => {
  console.log('update');
}, []);
```

あ、でもこれ、`ExpensiveChild`のpropsに`data={count}`があるから、結局毎回再レンダリングされるじゃん。意味ない。

このパターン、実はReact.memoとuseCallbackを両方正しく使っても、他のpropsで再レンダリングされてたら意味がないという罠がある。実装してから気づくことが多い。

## useMemoは値をキャッシュする

useMemoの方は比較的シンプルで、計算結果をメモ化する。

```typescript
const filteredAndSortedItems = useMemo(() => {
  return items
    .filter(item => item.active)
    .sort((a, b) => a.priority - b.priority);
}, [items]);
```

これは`items`が変わらない限り、filter + sortの処理をスキップできる。

ただ、ここで疑問が出てくるんだけど、どれくらい重い処理ならuseMemoを使うべきなのか。配列100件のfilterとsortって、実際めちゃくちゃ速い。useMemoのオーバーヘッドの方が大きい可能性もある。

筆者の経験では、1000件を超える配列操作とか、ネストしたループがある処理じゃない限り、useMemoは不要なことが多い。でもプロジェクトによるし、React Profilerで測らないと正確には分からない。

### オブジェクト生成でハマったケース

あと、useMemoが必要になるのは、オブジェクトや配列を生成して子コンポーネントに渡すとき。

```typescript
const Parent = () => {
  const config = { theme: 'dark', size: 'large' };
  return <Child config={config} />;
};
```

これ、毎回新しいオブジェクトが生成されるから、`Child`がReact.memoでも必ず再レンダリングされる。

```typescript
const config = useMemo(() => ({
  theme: 'dark',
  size: 'large'
}), []);
```

こう書けば参照が保持される。でも正直、これくらいならReact.memoを外した方が分かりやすいんじゃないかと思ってる。コードが複雑になるだけで、パフォーマンス改善効果は微妙。

## useCallbackとuseMemoの関係

実は、useCallbackはuseMemoの糖衣構文に過ぎない。

```typescript
useCallback(fn, deps)
// これは以下と同じ
useMemo(() => fn, deps)
```

関数を返すuseMemoがuseCallbackというだけ。この事実を知ってから、筆者の中では「メモ化するのは値か関数か」という区分けで考えるようになった。

値ならuseMemo、関数ならuseCallback。シンプル。

でもこれ、TypeScriptの型推論の観点だと微妙に違いがあって、useCallbackの方が型が正確に推論される。useMemoで関数返すと、戻り値の型がちょっと複雑になるんで、素直にuseCallback使った方がいい[^2]。

[^2]: 具体的にはジェネリクスの扱いとか、オーバーロードされた関数の型とか。試してないけど、多分そういう細かい違いがあるはず。

## 実際にいつ使うべきか

結局、useCallbackとuseMemoは「使わない」が正解なことが多い。

Kent C. Doddsのブログ（https://kentcdodds.com/blog/usememo-and-usecallback）でも書かれてるけど、パフォーマンス問題が顕在化するまで使わなくていい。Reactは十分速いので、最適化を先走ると逆に遅くなる。

筆者がプロジェクトで実際に使うのは次のケースくらい。

- 1000件以上のリストをフィルタリング/ソートする場合（useMemo）
- 無限スクロールやバーチャルリストで子コンポーネントが大量にある場合（useCallback + React.memo）
- useEffectの依存配列に関数を含めたい場合（useCallback）

それ以外は基本的に使わない。特に、「なんとなく速くなりそう」で使うのは危険。

### カスタムフックでの使い方

カスタムフック内で関数を返す場合は、useCallbackを使うべきか。これも微妙なところで、意見が分かれる。

```typescript
const useCounter = () => {
  const [count, setCount] = useState(0);

  const increment = useCallback(() => {
    setCount(c => c + 1);
  }, []);

  return { count, increment };
};
```

このコード、`increment`をuseCallbackで包んでるけど、実際に使う側でuseEffectの依存配列に入れない限り意味がない。でもカスタムフックの利用側がどう使うか分からないから、一応useCallbackしとく、みたいな保険的な使い方をすることもある。

個人的には、カスタムフック内では基本的に使わなくていいと思ってる。必要になったら追加すればいい。でもチーム開発だと「念のため」でつけることもあるので、プロジェクトの方針次第。

## パフォーマンス計測の話

結局、useCallbackとuseMemoを使うべきかどうかは、React Profilerで測定しないと分からない。

Chrome DevToolsのReact Profilerタブで、コンポーネントのレンダリング時間を可視化できる。これで「このコンポーネント、毎回500msかかってるな」みたいなのが分かる。で、そこに対してuseMemoやuseCallbackを適用して、改善されたかどうかを確認する。

筆者、以前これをサボって勘でuseCallbackを配置しまくって、逆にパフォーマンス悪化させたことがある。測定大事。

ただ、React Profilerの使い方自体が分かりにくいというか、数字の見方が難しい。どれくらいの時間なら許容範囲なのか、基準が曖昧なんで、経験が必要になる。この辺、もうちょっと良いツールがあればいいんだけど。

## まとめ

useCallbackとuseMemoの使い分けは、結局「測定してから使う」が答えな気がする。

- useCallback：関数をメモ化。React.memoと組み合わせるか、useEffectの依存配列で使う場合のみ
- useMemo：値をメモ化。重い計算処理の結果をキャッシュする場合のみ

それ以外は使わない。特に「なんとなく」で使うと、コードが複雑になってメンテナンスコストが上がるだけ。

あと、useCallbackの依存配列のバグは本当に気をつけないと、クロージャで古い値キャプチャして、デバッグに時間かかる。ESLintのexhaustive-depsルールは必ず有効にしといた方がいい。

将来的にはReactコンパイラ（React Forget）が自動でメモ化してくれるようになるかもしれない。そうなったら、こういう手動最適化は不要になるんだろうけど、まだ実験段階なので、しばらくは自分で判断する必要がある。そのうち試してみたい。
