---
title: "React Compilerの最適化の仕組みと制約"
emoji: "⚡"
type: "tech"
topics: ["react", "javascript", "frontend", "compiler"]
published: true
---

## はじめに

皆さんこんにちは。先日、React 19のリリースに向けて**React Compiler**が話題になっています。これは長年「Forget」というコードネームで開発されていたもので、useMemoやuseCallbackを手動で書かなくても、コンパイラが自動的に**メモ化**を挿入してくれるという画期的な機能です[^1]。

[^1]: React CompilerはMetaのReact Coreチームが2021年から開発していたプロジェクトで、当初は「React Forget」という名前でした。

これはReactの開発体験を大きく変える可能性があります。しかし、実際にどのように動作するのか、そしてどのような制約があるのかを理解しておくことが重要です。この記事では、React Compilerの仕組みと、コンパイラが最適化できないケースについて深掘りしていきます。

:::message
この記事はReact 19.0.0のリリース候補版の時点の情報です。正式リリース時には動作が変わる可能性があります。
:::

## React Compilerの基本的な仕組み

React Compilerは、ビルド時にコンポーネントのコードを解析し、自動的にメモ化を挿入します。従来は開発者が手動でuseMemoやuseCallbackを配置する必要がありましたが、コンパイラがこれを自動化してくれるわけです。これにより、パフォーマンスの最適化がより手軽になります。

例えば、次のようなコンポーネントがあるとします。このコンポーネントは、todosの配列をfilterの値でフィルタリングする単純な構造です。

```tsx
function TodoList({ todos, filter }) {
  const filteredTodos = todos.filter(todo => todo.status === filter);

  return (
    <ul>
      {filteredTodos.map(todo => (
        <TodoItem key={todo.id} todo={todo} />
      ))}
    </ul>
  );
}
```

従来のReactでは、`todos`や`filter`が変わらない限り`filteredTodos`の計算をスキップしたい場合、次のようにuseMemoで明示的に囲む必要がありました。依存配列の指定も手動で行う必要があります。

```tsx
const filteredTodos = useMemo(
  () => todos.filter(todo => todo.status === filter),
  [todos, filter]
);
```

React Compilerは、このメモ化を自動的に挿入してくれます。開発者は何も意識せずに最初のコードを書くだけで、コンパイラが**依存配列**を解析し、適切にメモ化してくれるのです。これにより、メモ化の漏れや誤った依存配列の指定といった問題を防ぐことができます。

筆者はここの仕組みが一番興味深かったのですが、どうやって依存配列を自動的に判定しているのでしょうか。

## どのコードが最適化されるのか

React Compilerは、コンポーネントやフックの中で使用される値の依存関係を**静的解析**によって追跡します。基本的には、次のような条件を満たすコードが最適化の対象になります。

```tsx
function ExpensiveComponent({ data }) {
  // この計算は自動的にメモ化される
  const processed = data.map(item => ({
    ...item,
    computed: heavyCalculation(item.value)
  }));

  // このコールバックも自動的にメモ化される
  const handleClick = () => {
    console.log(processed);
  };

  return <div onClick={handleClick}>{/* ... */}</div>;
}
```

実際に試してみると、なんとReact Compilerはこのコンポーネント全体を分析し、`processed`と`handleClick`の両方に適切なメモ化を挿入しました。従来なら、useMemoとuseCallbackを2つ書く必要があったところです。

個人的にはちょっとびっくりしました。依存配列の抜け漏れを心配する必要がなくなるのは大きなメリットです。コンパイラが自動的に最適な依存関係を判断してくれるため、開発者の負担が大幅に軽減されます。

しかし、すべてのコードが最適化されるわけではありません。React Compilerにはいくつかの重要な制約があります。次のセクションでは、React Compilerが最適化できないケースを見ていきます。

## React Compilerの制約

ここからが本題です。React Compilerは非常に強力ですが、いくつかの重要な制約があります。これらは**Rules of React**と呼ばれるReactのルールに違反しているコードです。このルールを守らないと、コンパイラは安全に最適化を行うことができません。

### ミューテーションの問題

React Compilerが最も苦手とするのは、既存のオブジェクトを直接変更するコードです。これはミューテーションと呼ばれる操作で、Reactの原則に反します。

```tsx
function BrokenComponent({ items }) {
  const processed = items;
  processed.push({ id: 'new', value: 0 }); // NG: propsを直接変更

  return <List data={processed} />;
}
```

このコードを実行すると、思わぬバグが発生する可能性があります。React Compilerはこのようなミューテーションを検知できない場合があり、最適化の前提が崩れてしまうのです。コンパイラは値が不変であることを前提に最適化を行うため、このような直接的な変更は問題になります。

正しくは、次のように新しい配列を作成する必要があります。

```tsx
function CorrectComponent({ items }) {
  const processed = [...items, { id: 'new', value: 0 }]; // OK: 新しい配列を作成

  return <List data={processed} />;
}
```

この制約は理にかなっています。Reactの不変性の原則を守ることで、コンパイラが安全に最適化できるわけです。

### クロージャの制約

もう一つ興味深い制約は、クロージャが古い値をキャプチャするケースです。これは特に非同期処理で問題になりやすいパターンです。

```tsx
function CounterComponent() {
  const [count, setCount] = useState(0);

  const increment = () => {
    setTimeout(() => {
      setCount(count + 1); // NG: クロージャが古いcountをキャプチャ
    }, 1000);
  };

  return <button onClick={increment}>Increment</button>;
}
```

このパターンを試してみたところ、React Compilerは警告を出しました。問題は、`setTimeout`の中のクロージャが、呼び出し時の`count`の値をキャプチャしてしまうことです。非同期処理の中で古い値を参照してしまうと、予期しない動作を引き起こす可能性があります。

正しくは、関数形式の更新を使う必要があります。

```tsx
const increment = () => {
  setTimeout(() => {
    setCount(prev => prev + 1); // OK: 常に最新の値を使用
  }, 1000);
};
```

残念ながら、すべてのケースでコンパイラがこのパターンを自動修正できるわけではありません。開発者がRules of Reactを理解していることが前提になります。コンパイラはあくまでサポートツールであり、基本的なReactの知識は依然として重要です。

### 条件付きフックの問題

React Compilerは条件付きフックの呼び出しも検知します。これはReactの基本的なルールの一つです。

```tsx
function ConditionalHookComponent({ condition }) {
  if (condition) {
    const [state, setState] = useState(0); // NG: 条件付きフック
    return <div>{state}</div>;
  }
  return null;
}
```

これは従来のReactでも禁止されているパターンですが、React Compilerはこのようなコードに対してビルドエラーを出します。コンパイル時に検知してくれるのは嬉しい反面、ランタイムエラーではなくビルドエラーになるため、既存コードの移行時には注意が必要です。

正しくは、フックを常に呼び出すように書き換える必要があります。条件分岐はフックの呼び出しの後に配置します。

```tsx
function CorrectHookComponent({ condition }) {
  const [state, setState] = useState(0);

  if (!condition) {
    return null;
  }

  return <div>{state}</div>;
}
```

フックのルールを守ることでコンパイラの最適化が可能になっているわけです。

## eslint-plugin-react-compilerの活用

React Compilerの制約を事前に検知するために、公式のESLintプラグインが提供されています[^2]。

[^2]: eslint-plugin-react-compilerは、React Compilerのルール違反を静的解析で検出してくれるツールです。GitHub上で活発に開発が進んでいます。

```bash
npm install -D eslint-plugin-react-compiler
```

設定ファイルに追加します。

```json
{
  "plugins": ["react-compiler"],
  "rules": {
    "react-compiler/react-compiler": "error"
  }
}
```

このプラグインを使うと、コンパイラが最適化できないコードを開発時に検知できます。GitHubのissue #983では、このプラグインの誤検知に関する議論が行われていて、コミュニティでも活発に改善が進んでいます。ESLintの統合により、CI/CDパイプラインにも組み込むことができるため、チーム開発での品質管理にも役立ちます。

実際にプラグインを導入してみたところ、既存のコードベースでいくつかのルール違反が見つかりました。特に、古いコードでpropsを直接変更しているパターンが多く残っていたのには驚きました。長年運用されているプロジェクトでは、このような潜在的な問題が蓄積されていることが多いです。

筆者はまだ試していないのですが、このプラグインを既存の大規模プロジェクトに導入する場合、段階的に適用していく戦略が必要になりそうです。

## React Compilerの未来

React Compilerは、Reactの開発体験を大きく変える可能性を秘めています。しかし、実際にプロダクションで使うには、いくつかの課題が残っています。

まず、既存のコードベースがRules of Reactを完全に遵守しているかどうかを確認する必要があります。これは単なるリファクタリング作業ではなく、コードの品質を見直す良い機会になります。

また、React Compilerがどの程度のパフォーマンス改善をもたらすかは、アプリケーションの特性に依存します。React Working Groupでは、実際のアプリケーションでのベンチマーク結果が共有されていて、多くのケースで10-30%のレンダリング回数削減が報告されています。ただし、すでに適切にメモ化されているアプリケーションでは、改善効果は限定的かもしれません。

推測ですが、コンパイラの最適化アルゴリズムは今後も進化していくでしょう。現在は保守的に最適化されているケースでも、将来的にはより積極的な最適化が可能になるかもしれません。Reactのエコシステムは常に進化し続けているため、筆者の考えでは今後の発展に大いに期待が持てます。

筆者としては、React Compilerの今後の発展に期待しつつ、まずは小規模なプロジェクトで試してみることから始めるのが良いと思います。Rules of Reactを守ることの重要性を再認識する良い機会にもなるでしょう。

ただし、すべてのプロジェクトで必須というわけではありません。useMemoやuseCallbackを適切に使えている既存のコードベースでは、移行のコストと得られるメリットのバランスを慎重に検討する必要があります。新しい技術の導入は、プロジェクトの状況や制約を考慮した上で判断することが重要です。筆者はまだ試していないのですが、大規模なレガシーコードベースでの導入事例が増えてくれば、より実践的な知見が得られるでしょう。
