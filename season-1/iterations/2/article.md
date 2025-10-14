---
title: "useEffectの依存配列、本当に最適化できてる？"
emoji: "🔄"
type: "tech"
topics: ["react", "typescript", "hooks", "frontend"]
published: true
---

## 依存配列の沼にハマった話

筆者は先日、パフォーマンスの問題を抱えるReactアプリケーションのレビューをしていて、こんなコードを見かけました。

```tsx
const [user, setUser] = useState<User | null>(null);
const [profile, setProfile] = useState<Profile | null>(null);

useEffect(() => {
  if (user) {
    fetchProfile(user.id).then(setProfile);
  }
}, [user.id]); // ESLintエラーが出るので、user全体じゃなくてidだけ
```

一見すると、「userオブジェクト全体を依存配列に入れると参照が変わるたびに実行されちゃうから、プリミティブなidだけ入れておけばいいでしょ」という意図が見えます。実際、ESLintの警告も消えるし、動作もするんですよね。

でも、これって本当に正しい最適化なんでしょうか？

今回は、useEffectの依存配列に関する最適化パターンを、実際によく見かける問題と共に掘り下げていきます。個人的には、この話題って「動けばいい」で済ませがちなんですが、きちんと理解しておかないと後で痛い目を見るんです。

:::message
この記事では、React 18を前提としています。また、筆者の経験と解釈に基づく部分が含まれます。
:::

## 依存配列の本質は「最適化」であって「制御」じゃない

まず最初に押さえておきたいのが、依存配列の本来の目的です。uhyo氏の記事「過激派が教える！ useEffectの正しい使い方」[^uhyo]でも指摘されていますが、**依存配列は最適化のための機能**なんですよね。

[^uhyo]: https://zenn.dev/uhyo/articles/useeffect-taught-by-extremist

試しに、依存配列なしでuseEffectを書いてみましょう。

```tsx
useEffect(() => {
  console.log('実行されました');
});
```

この場合、レンダリングのたびに毎回effectが実行されます。これがデフォルトの挙動です。依存配列は、この「毎回実行」という挙動を「前回と変わってなければスキップ」という形で最適化するためのものなんです。

つまり、依存配列は「いつ実行するか」を制御するための仕組みではなく、「いつスキップするか」を指定する仕組みと捉えた方が正確です。

自分はよく、「依存配列は空っぽにすれば初回だけ実行される」という理解でコードを書いていた時期がありました。でも、それは結果的にそうなるだけであって、本質的には「依存する値が何もないから、常にスキップされる」という意味なんですよね。

:::details 余談: Dan Abramovの名言
Dan Abramovが自身のブログ「A Complete Guide to useEffect」[^overreacted]で書いていることですが、「Lying to React about dependencies has bad consequences」というフレーズがあります。

[^overreacted]: https://overreacted.io/a-complete-guide-to-useeffect/

依存配列に本来必要な値を入れないのは、Reactに嘘をついているのと同じだ、と。この表現が個人的にはすごく腑に落ちていて、依存配列を考えるときは常にこの言葉を思い出すようにしています。
:::

## Stale Closureという古典的な罠

冒頭のコード例に戻りましょう。実は、あのコードには潜在的なバグがあります。

```tsx
useEffect(() => {
  if (user) {
    fetchProfile(user.id).then(setProfile);
  }
}, [user.id]); // user.idしか依存配列に入っていない
```

このコードの問題は、effectのクロージャ内で`user`オブジェクト全体を参照しているのに、依存配列には`user.id`しか入れていない点です。もし仮に、userのidは変わらないけど他のプロパティが更新された、という状況があったとします。

```tsx
// 最初のレンダリング
{ id: 1, name: "Alice", status: "active" }

// 次のレンダリング
{ id: 1, name: "Alice", status: "inactive" } // statusが変わった
```

この場合、`user.id`は変わっていないのでeffectは実行されません。でも、クロージャ内の`user`は古い値を参照したままになります。これがいわゆる**Stale Closure**（古いクロージャ）問題です。

この問題の厄介なところは、多くの場合でたまたま動いてしまうことです。そして、特定の条件下でだけバグが顕在化する。実務でこれにハマったことがある人は多いんじゃないでしょうか。

### ESLintのexhaustive-depsルール

Reactチームは、この問題を静的解析で検出できるように`react-hooks/exhaustive-deps`というESLintルールを提供しています。これは、effect内で使用しているすべてのリアクティブな値が依存配列に含まれているかをチェックするルールです。

```tsx
useEffect(() => {
  if (user) {
    fetchProfile(user.id).then(setProfile);
  }
}, [user.id]); // React Hook useEffect has a missing dependency: 'user'
```

このルールに従うなら、正しくは次のように書くべきです。

```tsx
useEffect(() => {
  if (user) {
    fetchProfile(user.id).then(setProfile);
  }
}, [user]);
```

個人的には、このESLintルールは絶対にオフにしないことをお勧めします。Dan Abramovによれば、Facebook（当時）の巨大なコードベースでも、このルールの警告は30件程度しかないそうです[^exhaustive]。つまり、ほとんどのケースで従うべきルールなんです。

[^exhaustive]: https://overreacted.io/a-complete-guide-to-useeffect/#two-ways-to-be-honest-about-dependencies

## オブジェクトと関数を依存配列に入れる問題

さて、じゃあ素直に`user`オブジェクト全体を依存配列に入れればいいのか、というと話はそう単純じゃありません。

```tsx
const [user, setUser] = useState<User | null>(null);

useEffect(() => {
  if (user) {
    fetchProfile(user.id).then(setProfile);
  }
}, [user]); // これで正しい？
```

一見正しそうですが、これには別の問題があります。Reactは依存配列の値を**参照等価性（===）**で比較するため、userオブジェクトの中身が同じでも、異なるオブジェクトインスタンスであれば「変わった」と判断されます。

試しに、こんな状況を考えてみましょう。

```tsx
function ParentComponent() {
  const [count, setCount] = useState(0);

  const user = {
    id: 1,
    name: "Alice"
  }; // レンダリングのたびに新しいオブジェクトが作られる

  return (
    <div>
      <button onClick={() => setCount(count + 1)}>カウント: {count}</button>
      <UserProfile user={user} />
    </div>
  );
}

function UserProfile({ user }: { user: User }) {
  useEffect(() => {
    console.log('effectが実行されました');
    fetchProfile(user.id);
  }, [user]);

  return <div>{user.name}</div>;
}
```

この場合、親コンポーネントの`count`が変わるたびに、`user`オブジェクトは新しく作られます。すると、UserProfileコンポーネントのeffectも毎回実行されてしまいます。userの中身は何も変わっていないのに、です。

これは、依存配列を正しく指定していても起きる問題なんですよね。

## パターン1: useMemoで値を安定化させる

この問題に対する一つの解決策は、`useMemo`を使ってオブジェクトの参照を安定化させることです。

```tsx
function ParentComponent() {
  const [count, setCount] = useState(0);

  const user = useMemo(() => ({
    id: 1,
    name: "Alice"
  }), []); // 依存配列が空なので、常に同じオブジェクトが返される

  return (
    <div>
      <button onClick={() => setCount(count + 1)}>カウント: {count}</button>
      <UserProfile user={user} />
    </div>
  );
}
```

ただし、これは銀の弾丸ではありません。`useMemo`自体にもコストがありますし、本当に必要な場合にのみ使うべきです。Reactの公式ドキュメントでも、useMemoが有効なのは以下のケースだと明示されています[^usememo]：

[^usememo]: https://react.dev/reference/react/useMemo

- 計算が明らかに重い場合で、依存配列の値がめったに変わらない
- `React.memo`でラップされたコンポーネントにpropsとして渡す場合
- その値が他のHookの依存配列として使われる場合

個人的な経験では、「なんとなく最適化しておこう」でuseMemoを多用すると、かえってコードが読みづらくなり、パフォーマンスも悪化することがあります。

## パターン2: 必要なプリミティブ値だけを抽出する

もっとシンプルな解決策は、effectの中で本当に必要な値だけを依存配列に入れることです。

```tsx
useEffect(() => {
  fetchProfile(userId);
}, [userId]); // userオブジェクトではなく、必要なidだけ
```

この場合、effectのクロージャ内でも`userId`しか使わないようにします。

```tsx
function UserProfile({ user }: { user: User }) {
  const userId = user.id; // プリミティブ値を抽出

  useEffect(() => {
    fetchProfile(userId); // userオブジェクト全体には触らない
  }, [userId]);

  return <div>{user.name}</div>;
}
```

これなら、userオブジェクトの参照が変わっても、idという数値（プリミティブ）が同じであればeffectはスキップされます。プリミティブ値は値で比較されるため、参照等価性の問題が起きないんです。

ただし、これが使えるのは、effectの中でオブジェクトの他のプロパティを参照する必要がない場合に限ります。もしuserのnameやstatusも使うなら、結局オブジェクト全体を依存配列に入れるか、それぞれのプロパティを個別に抽出する必要があります。

:::details 余談: 配列の場合はどうする？
オブジェクトと同様に、配列も参照で比較されます。

```tsx
const items = [1, 2, 3];

useEffect(() => {
  console.log('items changed');
}, [items]); // 配列の中身が同じでも、参照が変わると実行される
```

配列の場合は、内容が変わったかどうかを判定する方法がいくつかあります。

1. **配列の長さだけを依存配列に入れる**
```tsx
useEffect(() => {
  // itemsを使った処理
}, [items.length]); // 長さが変わったときだけ実行
```

これは、配列の要素数が変わることが重要で、個々の要素の変化は気にしない場合に使えます。

2. **配列をJSON文字列化する（非推奨）**
```tsx
const itemsStr = JSON.stringify(items);

useEffect(() => {
  const parsed = JSON.parse(itemsStr);
  // parsedを使った処理
}, [itemsStr]);
```

これは一応動きますが、パフォーマンス的にも可読性的にも推奨されません。素直にuseMemoを使うか、設計を見直した方がいいでしょう。

3. **Reactの`useMemo`で配列自体をメモ化する**
```tsx
const memoizedItems = useMemo(() => items, [items.length, ...items]);
```

ただ、これもあまりエレガントではないですね。配列の依存配列問題は、正直なところ決定打がないのが現状です。
:::

## パターン3: 関数を依存配列から除外する（setState関数の場合）

実は、`useState`から返されるsetter関数は、依存配列に含める必要がありません。

```tsx
const [profile, setProfile] = useState<Profile | null>(null);

useEffect(() => {
  fetchProfile(userId).then(setProfile);
}, [userId]); // setProfileは依存配列に入れなくてもOK
```

なぜなら、Reactはsetter関数の参照が常に安定していることを保証しているからです[^identity]。つまり、コンポーネントが再レンダリングされても、同じ`useState`から返される`setProfile`関数は常に同一のオブジェクトなんです。

[^identity]: https://react.dev/reference/react/useState#setstate

これはdispatch関数にも当てはまります。

```tsx
const [state, dispatch] = useReducer(reducer, initialState);

useEffect(() => {
  // dispatchは依存配列に入れなくてもOK
  dispatch({ type: 'FETCH_START' });
}, [userId]);
```

とはいえ、ESLintのexhaustive-depsルールは、これらの関数を依存配列に入れても警告を出しません。入れても害はないので、「安定している」という知識があやふやなら入れておいた方が安全です。

## パターン4: useCallbackで関数をメモ化する

もっと複雑なのが、自分で定義した関数を依存配列に含める場合です。

```tsx
function UserProfile({ userId }: { userId: number }) {
  const fetchData = async () => {
    const profile = await fetchProfile(userId);
    const settings = await fetchSettings(userId);
    return { profile, settings };
  };

  useEffect(() => {
    fetchData();
  }, [fetchData]); // 警告: fetchDataを依存配列に入れるべき

  return <div>...</div>;
}
```

この場合、`fetchData`関数はレンダリングのたびに新しく作られるため、effectも毎回実行されてしまいます。

解決策は`useCallback`を使うことです。

```tsx
const fetchData = useCallback(async () => {
  const profile = await fetchProfile(userId);
  const settings = await fetchSettings(userId);
  return { profile, settings };
}, [userId]); // fetchDataはuserIdが変わったときだけ再作成される

useEffect(() => {
  fetchData();
}, [fetchData]);
```

これで、`userId`が変わったときだけ`fetchData`が再作成され、effectも再実行されます。

ただ、個人的にはこのパターンはあまり好きじゃないんですよね。useCallbackとuseEffectが連鎖して、依存関係が複雑になりがちです。もっとシンプルに、effect内で直接処理を書いた方が読みやすい場合も多いです。

```tsx
useEffect(() => {
  const fetchData = async () => {
    const profile = await fetchProfile(userId);
    const settings = await fetchSettings(userId);
    // 処理...
  };

  fetchData();
}, [userId]); // シンプル！
```

この方が依存関係も明確ですし、コードも短くなります。

## パターン5: useEffectEventという未来の解決策

実は、Reactチームはこの問題を根本的に解決するための新しいHookを検討していました。それが`useEffectEvent`（当初は`useEvent`という名前でした）です[^useevent]。

[^useevent]: https://react.dev/reference/react/experimental_useEffectEvent

```tsx
const onProfileUpdate = useEffectEvent((userId: number) => {
  // この関数は常に安定した参照を持つ
  // でも、内部では常に最新のstateやpropsにアクセスできる
  fetchProfile(userId);
});

useEffect(() => {
  onProfileUpdate(userId);
}, [userId]); // onProfileUpdateは依存配列に入れる必要がない
```

`useEffectEvent`で作られた関数は、参照が安定しているため依存配列に含める必要がありません。それでいて、関数内では常に最新のstateやpropsにアクセスできる、という魔法のような仕組みです。

ただし、これは2025年1月現在でもまだexperimentalな機能で、本番環境で使うことは推奨されていません。将来的にはこれが標準になる可能性がありますが、今のところは上記のパターン1〜4で対応する必要があります。

:::details 余談: useEffectEventの設計思想
GitHub issueの #19820[^issue19820] では、「"what"の依存と"when"の依存を分けたい」という提案がされていました。つまり、「副作用の内容（what）を決める変数」と「副作用を実行するタイミング（when）を決める変数」は本来別物なのに、useEffectの依存配列では区別できない、という問題提起です。

[^issue19820]: https://github.com/facebook/react/issues/19820

useEffectEventは、まさにこの問題を解決するために生まれた提案なんですね。"what"を決める部分はuseEffectEvent内に書き、"when"を決める部分だけを依存配列に含める、という棲み分けができるわけです。

ただ、個人的には、この機能が本当に必要なケースってそこまで多くないんじゃないかな、と思っています。多くの場合、設計を見直すかuseCallbackを使えば十分対応できますし。とはいえ、選択肢が増えるのは良いことですね。
:::

## クリーンアップ関数を忘れずに

最後に、依存配列の最適化を考える際に忘れがちな、でも重要なポイントがあります。それは**クリーンアップ関数**です。

```tsx
useEffect(() => {
  const timer = setInterval(() => {
    console.log('tick');
  }, 1000);

  return () => {
    clearInterval(timer); // クリーンアップ
  };
}, []);
```

uhyo氏の記事[^uhyo]でも強調されていますが、「クリーンアップ関数のないuseEffectは不適切」というのが基本的な考え方です。特に、依存配列を空にして初回だけ実行するようなeffectでも、クリーンアップは必要なんです。

なぜなら、開発環境ではReactのStrict Modeにより、コンポーネントが意図的にマウント→アンマウント→マウントという流れを経ることがあるからです。また、Fast Refreshでもコンポーネントの再マウントが発生します。

自分も以前、「どうせ一回しか実行されないし、クリーンアップいらないでしょ」と思ってサボったら、開発中に二重にイベントリスナーが登録されてバグった、という経験があります。

クリーンアップ関数は、依存配列の最適化以前の、もっと基本的な部分なんですよね。でも、つい忘れがちなので意識的に書くようにしています。

## 結局、どうすればいいのか

ここまでいろいろなパターンを見てきましたが、個人的な実践指針をまとめるとこんな感じです。

**1. まずはESLintのルールに従う**
`react-hooks/exhaustive-deps`の警告は基本的に全部対応する。「警告が出てるけど動いてるからいいや」は絶対にダメ。

**2. オブジェクトや関数はできるだけ依存配列に入れない設計にする**
- 必要なプリミティブ値だけを抽出する
- effect内で関数を定義する（わざわざuseCallbackを使わない）
- 状態の持ち方を見直す

**3. どうしても必要ならuseMemo/useCallbackを使う**
ただし、使う前に「本当に必要か？」と自問する。パフォーマンス測定もせずに、なんとなくメモ化するのは避ける。

**4. クリーンアップ関数は必ず書く**
副作用が発生する以上、その後始末も責任を持つ。これは最適化以前の基本です。

**5. 迷ったらシンプルな方を選ぶ**
凝った最適化をするより、わかりやすいコードの方が長期的には保守しやすい。

React Hooksは強力ですが、依存配列の扱いは最初に思った以上に奥が深いです。でも、本質的な考え方さえ理解しておけば、そこまで難しくはありません。「依存配列は最適化であって制御じゃない」「Reactに嘘をつかない」という原則を守っていれば、大きく外れることはないと思います。

そして何より、わからないときは素直にESLintの言うことを聞く。これが一番確実だったりします。
