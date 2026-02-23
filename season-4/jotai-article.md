---
title: "Jotaiを状態管理の「部品」として使いこなす"
emoji: "⚛️"
type: "tech"
topics: ["react", "jotai", "typescript", "状態管理"]
published: true
---

皆さんこんにちは。Reactの状態管理ライブラリは数多くありますが、最近**Jotai**について考える機会がありました。Jotaiは「atomic」な状態管理を提供するライブラリとして知られていますが、筆者が注目しているのは、Jotaiが単なる状態管理ライブラリではなく、**低レベルな状態管理の部品**として機能する点です。

この記事では、Jotaiの基本的な使い方から、カスタムatomの作成、そして他の状態管理パターンのバックエンドとしての活用までを扱います。

## Jotaiの基本

Jotaiの基本的なAPIは非常にシンプルです。`atom`関数で状態を定義し、`useAtom`フックでコンポーネントから利用します。

```typescript
import { atom, useAtom } from 'jotai';

// atomの定義
const countAtom = atom(0);

function Counter() {
  const [count, setCount] = useAtom(countAtom);
  return (
    <button onClick={() => setCount(c => c + 1)}>
      Count: {count}
    </button>
  );
}
```

Reduxのようなfluxアーキテクチャと比較すると、action、reducer、dispatchといった概念がありません。atomを定義して、使いたいコンポーネントで`useAtom`を呼ぶだけで状態が共有されます。この直感的なAPIは、Jotaiの大きな魅力の一つです。

Reactコミュニティでも「状態管理の学習コストが高すぎる」という声をよく見かけます。Jotaiはその点でかなり敷居が低いライブラリだと感じます。初めてJotaiを触る人でも、数分で基本的な使い方を理解できるはずです。

:::message
JotaiはデフォルトでProviderなしで動作します。これはReact 18以降のuseSyncExternalStoreを活用した設計によるものと考えられます。複数のストアを分離したい場合は明示的にProviderを使用します。
:::

ここで面白いのは、**派生atom**の仕組みです。

```typescript
const doubledAtom = atom((get) => get(countAtom) * 2);

function DoubledDisplay() {
  const [doubled] = useAtom(doubledAtom);
  return <div>Doubled: {doubled}</div>;
}
```

派生atomは他のatomの値を読み取って計算結果を返します。Recoilのselectorに相当する概念ですが、Jotaiでは派生もすべて「atom」として統一的に扱われます。この統一感は非常に重要です。派生も通常のatomも、使う側からは同じ`useAtom`で扱えます。この設計は、後で見るカスタムユーティリティを作る際に重要になります。

## atomは関数で自由に作れる

Jotaiの本当の強みは、atomが単なる値の入れ物ではなく、**関数で自由にカスタマイズできる**点にあると筆者は考えています。ここがJotaiの最も興味深いところです。

例えば、localStorageと同期するatomを作りたいとします。このような要件は実際のプロジェクトでよく出てきます。

```typescript
function atomWithLocalStorage<T>(key: string, initialValue: T) {
  const baseAtom = atom(initialValue);

  const derivedAtom = atom(
    (get) => get(baseAtom),
    (get, set, update: T | ((prev: T) => T)) => {
      const nextValue = typeof update === 'function'
        ? (update as (prev: T) => T)(get(baseAtom))
        : update;
      set(baseAtom, nextValue);
      localStorage.setItem(key, JSON.stringify(nextValue));
    }
  );

  return derivedAtom;
}

// 使用例
const themeAtom = atomWithLocalStorage('theme', 'light');
```

このパターンでは、読み取り用の関数と書き込み用の関数を両方指定することで、setterの挙動をカスタマイズしています。localStorageへの書き込みは副作用ですが、atomのwrite関数内で自然に扱えます。この柔軟性はJotaiの設計思想をよく表しています。

このようなパターンを「atomファクトリ」と呼ぶことがあります。atomを返す関数を作ることで、再利用可能なロジックをカプセル化できます。

:::details atomの初期化タイミングについて
上記の実装では初期値として`initialValue`を渡していますが、実際のアプリケーションではlocalStorageから初期値を読み込みたい場合があります。その場合は`atom`の初期値を関数にするか、`onMount`を使う方法が考えられます。ただし、SSR環境では`localStorage`が存在しないため、条件分岐が必要になります。この辺りの処理は意外と煩雑になりがちです。
:::

同様のパターンで、様々なカスタムatomを作成できます。いくつか例を挙げてみます。

```typescript
// デバウンス付きatom
function atomWithDebounce<T>(initialValue: T, delay: number) {
  const baseAtom = atom(initialValue);
  let timeoutId: ReturnType<typeof setTimeout> | null = null;

  return atom(
    (get) => get(baseAtom),
    (get, set, update: T) => {
      if (timeoutId) clearTimeout(timeoutId);
      timeoutId = setTimeout(() => {
        set(baseAtom, update);
      }, delay);
    }
  );
}

// バリデーション付きatom
function atomWithValidation<T>(
  initialValue: T,
  validate: (value: T) => boolean
) {
  const baseAtom = atom(initialValue);
  const errorAtom = atom<string | null>(null);

  const derivedAtom = atom(
    (get) => ({ value: get(baseAtom), error: get(errorAtom) }),
    (get, set, update: T) => {
      if (validate(update)) {
        set(baseAtom, update);
        set(errorAtom, null);
      } else {
        set(errorAtom, 'Validation failed');
      }
    }
  );

  return derivedAtom;
}
```

デバウンス付きatomは、検索フォームのようなケースで便利です。バリデーション付きatomは、フォーム入力の検証に使えます。どちらも基本的なパターンを応用したものです。

GitHubのJotaiリポジトリやコミュニティでは、こういったユーティリティが多数公開されています。`jotai/utils`パッケージにも`atomWithStorage`や`atomWithReset`といった便利なatomが用意されています。自分で作らなくても、すでに誰かが作ったものを使えることも多いです。

## 他のスタイルのバックエンドとして

ここからが筆者にとって最も興味深い話題です。Jotaiは、他の状態管理パターンの**バックエンド**として機能できます。これはJotaiを「部品」として捉える上で重要なポイントです。

### useQuery風のフェッチングatom

TanStack Query（旧React Query）のような、hook-basedなデータフェッチングAPIを考えてみます。TanStack Queryは非常に人気のあるライブラリですが、Jotaiでも似たようなAPIを構築できます。

```typescript
// useQueryのようなAPIを提供するatom
function atomWithQuery<T>(
  queryFn: () => Promise<T>
) {
  const dataAtom = atom<T | null>(null);
  const loadingAtom = atom(true);
  const errorAtom = atom<Error | null>(null);

  const queryAtom = atom(
    (get) => ({
      data: get(dataAtom),
      isLoading: get(loadingAtom),
      error: get(errorAtom),
    }),
    async (get, set) => {
      set(loadingAtom, true);
      set(errorAtom, null);
      try {
        const data = await queryFn();
        set(dataAtom, data);
      } catch (e) {
        set(errorAtom, e instanceof Error ? e : new Error(String(e)));
      } finally {
        set(loadingAtom, false);
      }
    }
  );

  // 初回マウント時に自動フェッチ
  queryAtom.onMount = (setAtom) => {
    setAtom();
  };

  return queryAtom;
}
```

これを使うと、以下のようなコードが書けるはずです。

```typescript
const userQueryAtom = atomWithQuery(() =>
  fetch('/api/user').then(res => res.json())
);

function UserProfile() {
  const [{ data, isLoading, error }] = useAtom(userQueryAtom);

  if (isLoading) return <div>Loading...</div>;
  if (error) return <div>Error: {error.message}</div>;
  return <div>Hello, {data?.name}</div>;
}
```

TanStack Queryほど機能は豊富ではありません。キャッシュの有効期限管理やバックグラウンド再取得といった高度な機能は含まれていません。ただし、キャッシュ、リフェッチ、依存関係の解決といった機能もatomの組み合わせで実装できると考えられます。

実際、`jotai-tanstack-query`というライブラリが存在します。このライブラリを使うと、TanStack QueryをJotaiのatomとして扱えるようになります。これはJotaiの柔軟性をよく示す例です。

### Zustand風のストアatom

Zustandのような、オブジェクト単位でのストア管理も実現できます。Zustandは「一つのオブジェクトにまとめる」スタイルで人気があります。Jotaiでも同様のパターンを構築できます。

```typescript
type Store<T> = {
  state: T;
  actions: Record<string, (...args: unknown[]) => void>;
};

function createStore<T extends object>(
  initialState: T,
  actions: (
    set: (partial: Partial<T>) => void,
    get: () => T
  ) => Record<string, (...args: unknown[]) => void>
) {
  const stateAtom = atom(initialState);

  const storeAtom = atom(
    (get) => get(stateAtom),
    (get, set, partial: Partial<T>) => {
      set(stateAtom, { ...get(stateAtom), ...partial });
    }
  );

  const boundActions = actions(
    (partial) => { /* set implementation */ },
    () => { /* get implementation */ }
  );

  return { stateAtom: storeAtom, actions: boundActions };
}
```

この例は概念的なものです。実際に使う場合はもう少し作り込みが必要になります。ただ、Jotaiの柔軟なatom定義を使えば、Zustandライクなパターンも構築できることがわかります。好みのAPIスタイルでラップできるのがJotaiの強みです。

## なぜJotaiを「部品」と捉えるのか

筆者がJotaiを「部品」と表現するのは、Jotaiが**状態管理のプリミティブ**を提供しているからです。これはReact自体の設計思想にも通じるものがあります。

従来の状態管理ライブラリは、特定のパターンを強制する傾向がありました。ReduxならReducer + Action、MobXならObservable、といった具合です。これらは強力ですが、ライブラリの思想に合わせた設計が必要になります。チームでの合意形成にもコストがかかります。

一方Jotaiは、atomという最小単位を提供し、その組み合わせ方は開発者に委ねています。

- シンプルに使いたい → そのまま`atom` + `useAtom`で十分です
- カスタムロジックが必要 → atomファクトリを作れば対応できます
- 既存パターンと統合したい → バックエンドとしてJotaiを使えます

この柔軟性は非常に魅力的です。Reactコミュニティで議論されている「状態管理疲れ」への一つの答えかもしれません。ライブラリを乗り換えるのではなく、低レベルな部品を使って必要なものを組み立てるアプローチです。

:::message
Jotaiはbundle sizeも小さく（コアは約2KB gzip）、必要な機能だけを追加できます。ただし、チームでの開発では、自由度が高すぎてパターンが乱立するリスクもあります。何らかの規約を設けることが推奨されます。「atomファクトリはこのディレクトリに置く」「命名規則は〜」といったルールがあると良いでしょう。
:::

## まとめ

Jotaiは、atomという小さなプリミティブを通じて、様々な状態管理パターンを実現できるライブラリです。

基本的な使い方はシンプルです。atomファクトリを活用することで、localStorage同期、デバウンス、バリデーションといったカスタムロジックを自然に組み込めます。さらに、useQuery風のデータフェッチングやZustand風のストア管理といった、他の状態管理スタイルのバックエンドとしても機能します。

筆者としては、Jotaiを「状態管理ライブラリ」というより「状態管理の部品」として捉えることで、その本当の価値が見えてくると考えています。特定のパターンに縛られず、プロジェクトに合った状態管理を組み立てられる柔軟性があります。今後のReact開発において重要な選択肢になるはずです。

まだ試していないパターンも多いので、引き続き探求していきたいと思います。Jotaiのエコシステムも成長しています。今後どのような発展を見せるか楽しみです。
