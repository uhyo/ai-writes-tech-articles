---
title: "ReactのuseTransitionでUXを改善する：非ブロッキング更新の実践"
emoji: "⚡"
type: "tech"
topics: ["react", "typescript", "frontend", "ux"]
published: true
---

## はじめに

皆さんこんにちは。最近のReactコミュニティでは、**useTransition**と**Suspense**を組み合わせたUX最適化が話題になっています。筆者も最近、インタラクティブなUIにおける応答性の改善について考える機会があり、この組み合わせは興味深い特徴を持っています。

従来、React アプリケーションでは、状態更新が重い処理を伴う場合、UIがフリーズしたようになる問題がありました。検索ボックスに文字を入力すると、フィルタリング処理中は次のキー入力が受け付けられない状況です。useTransition は、この問題に対する React の公式な解決策となります。

本記事では、useTransition と Suspense を組み合わせた実践的なパターンを探っていきます。

## useTransition の基本動作

useTransition は React 18 で導入された Hook で、状態更新を「緊急」と「非緊急」に分類できます。この機能により、UIの応答性を損なわずに重い処理を実行することが可能になります。

基本的な実装例です。

```tsx
import { useState, useTransition } from 'react';

function SearchResults() {
  const [query, setQuery] = useState('');
  const [results, setResults] = useState<string[]>([]);
  const [isPending, startTransition] = useTransition();

  const handleChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    setQuery(e.target.value);
    startTransition(() => {
      const filtered = heavyFilterOperation(e.target.value);
      setResults(filtered);
    });
  };

  return (
    <div>
      <input value={query} onChange={handleChange} placeholder="検索..." />
      {isPending && <span>検索中...</span>}
      <ul>{results.map(item => <li key={item}>{item}</li>)}</ul>
    </div>
  );
}

function heavyFilterOperation(query: string): string[] {
  const items = Array.from({ length: 10000 }, (_, i) => `Item ${i}`);
  return items.filter(item =>
    item.toLowerCase().includes(query.toLowerCase())
  );
}
```

このコードでは、入力フィールドの値（`query`）は即座に更新されます。一方、重いフィルタリング処理（`setResults`）は `startTransition` でラップすることで遅延可能な更新として扱われます。

`isPending` フラグを使って、処理中であることをユーザーに伝えることができます。興味深いことに、React は内部的に優先度キューを持っており、緊急な更新を優先して処理するはずです。これにより、ユーザーの入力操作がブロックされることはありません。

## Suspense との組み合わせ

useTransition の真価は、Suspense と組み合わせたときに発揮されます。この組み合わせにより、ローディング状態の制御がより柔軟になります。データ取得を伴う画面遷移での実装例を示します。

```tsx
import { useState, useTransition, Suspense, use } from 'react';

type Tab = 'profile' | 'posts' | 'followers';

interface UserProfile { name: string; bio: string; }
interface Post { id: number; title: string; }

function fetchUserProfile(userId: number): Promise<UserProfile> {
  return new Promise((resolve) => {
    setTimeout(() => resolve({ name: 'User', bio: 'Bio' }), 1000);
  });
}

function fetchUserPosts(userId: number): Promise<Post[]> {
  return new Promise((resolve) => {
    setTimeout(() => resolve([
      { id: 1, title: 'Post 1' },
      { id: 2, title: 'Post 2' },
    ]), 1500);
  });
}

function TabContainer({ userId }: { userId: number }) {
  const [activeTab, setActiveTab] = useState<Tab>('profile');
  const [isPending, startTransition] = useTransition();

  const handleTabChange = (tab: Tab) => {
    startTransition(() => setActiveTab(tab));
  };

  return (
    <div>
      <div style={{ opacity: isPending ? 0.7 : 1 }}>
        <button onClick={() => handleTabChange('profile')}>Profile</button>
        <button onClick={() => handleTabChange('posts')}>
          Posts {isPending && '⏳'}
        </button>
        <button onClick={() => handleTabChange('followers')}>Followers</button>
      </div>
      <Suspense fallback={<div>Loading...</div>}>
        {activeTab === 'profile' && <ProfileTab userId={userId} />}
        {activeTab === 'posts' && <PostsTab userId={userId} />}
        {activeTab === 'followers' && <FollowersTab userId={userId} />}
      </Suspense>
    </div>
  );
}

function ProfileTab({ userId }: { userId: number }) {
  const profile = use(fetchUserProfile(userId));
  return <div><h2>{profile.name}</h2><p>{profile.bio}</p></div>;
}

function PostsTab({ userId }: { userId: number }) {
  const posts = use(fetchUserPosts(userId));
  return <ul>{posts.map(post => <li key={post.id}>{post.title}</li>)}</ul>;
}

function FollowersTab({ userId }: { userId: number }) {
  return <div>Followers content</div>;
}
```

このパターンの重要な点は、タブ切り替え時に `startTransition` を使うことで、現在表示中のコンテンツを維持しながら新しいデータの取得を開始できることです。従来の実装では、タブをクリックした瞬間に Loading... が表示され、前のコンテンツが消えてしまう問題がありました。

useTransition を使うと、データ取得が完了するまで前のタブの内容を表示し続けることができます。`isPending` を使って、タブボタンに読み込み中のインジケーターを表示することで、ユーザーに処理中であることを伝えることが可能です。

:::message
この実装例では簡略化のため、コンポーネント内で直接 Promise を作成していますが、実際のアプリケーションでは、Promise を親コンポーネントで useMemo を使ってメモ化し、props として渡す必要があります。そうしないと、再レンダリングのたびに新しい Promise が作成され、無限ループが発生する可能性があります。
:::

## 検索フィルタリングの最適化

より実践的な例として、リアルタイム検索フィルタリングを実装してみます。

```tsx
import { useState, useTransition, useMemo } from 'react';

interface Product {
  id: number;
  name: string;
  category: string;
  price: number;
}

function ProductSearch({ products }: { products: Product[] }) {
  const [searchTerm, setSearchTerm] = useState('');
  const [deferredSearchTerm, setDeferredSearchTerm] = useState('');
  const [isPending, startTransition] = useTransition();

  const handleSearchChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    const value = e.target.value;
    setSearchTerm(value);
    startTransition(() => setDeferredSearchTerm(value));
  };

  const filteredProducts = useMemo(() => {
    return products.filter(product =>
      product.name.toLowerCase().includes(deferredSearchTerm.toLowerCase()) ||
      product.category.toLowerCase().includes(deferredSearchTerm.toLowerCase())
    );
  }, [products, deferredSearchTerm]);

  return (
    <div>
      <input
        type="text"
        value={searchTerm}
        onChange={handleSearchChange}
        placeholder="商品を検索..."
        style={{ border: isPending ? '2px solid orange' : '2px solid #ccc' }}
      />
      <p>
        {filteredProducts.length} 件の商品
        {isPending && ' (フィルタリング中...)'}
      </p>
      <ul style={{ opacity: isPending ? 0.6 : 1 }}>
        {filteredProducts.map(product => (
          <li key={product.id}>{product.name} - ¥{product.price}</li>
        ))}
      </ul>
    </div>
  );
}
```

このパターンでは、2つの state を使っています。`searchTerm` は入力フィールドの値を即座に反映し、`deferredSearchTerm` は遅延可能な更新として扱われます。これにより、大量のデータをフィルタリングする場合でも、入力フィールドの応答性が保たれるはずです。

筆者の観察では、この手法は商品リストが数千件を超えるような規模で特に有効だと考えられます。数百件程度であれば、useTransition を使わない通常の実装でも十分なパフォーマンスが得られる可能性があります。

## パフォーマンス特性と考慮点

useTransition の効果を最大限に引き出すには、いくつかの考慮点があります。適切な場面で使用することが重要です。

useTransition が有効なのは、大量のデータをフィルタリング・ソートする処理、複雑なレンダリングツリーを持つコンポーネントの更新、画面遷移やタブ切り替えでデータ取得を伴う場合です。逆に、単純な状態更新やアニメーションには不要です。すべての状態更新を startTransition でラップすると、かえってパフォーマンスが低下する可能性があります。

useTransition は React の **Concurrent Rendering** の一部として機能します。React 18 以降、createRoot を使ってアプリケーションをマウントすることで、自動的に Concurrent Features が有効になるはずです。

:::details Concurrent Rendering の内部挙動について
React の Concurrent Rendering は、レンダリング処理を中断可能にすることで、優先度の高い更新を優先的に処理します。内部的には、Fiber アーキテクチャを利用して作業単位を分割し、ブラウザのアイドル時間を活用して処理を進めるはずです。

ただし、これらの内部実装の詳細は完全には公開されておらず、将来的に変更される可能性があります。筆者としては、今後のバージョンでさらなる最適化が行われることを期待しています。
:::

開発時、useTransition の動作を確認する際は、Chrome DevTools の Performance タブを使うと効果的です。React DevTools の Profiler も、コンポーネントのレンダリング時間を可視化できます。

ただし、開発モードでは Strict Mode による二重レンダリングが発生するため、本番環境とは異なる挙動を示すことがあります。この点には注意が必要です。最終的なパフォーマンス検証は、プロダクションビルドで行う必要があります。

## まとめ

useTransition と Suspense の組み合わせは、React アプリケーションのUX改善において強力なツールです。特に、ユーザー入力の応答性を保ちながら重い処理を実行したい場合に有効だと考えられます。

筆者が感じたのは、この機能は「使いどころの見極め」が重要だということです。すべての状態更新に適用するのではなく、実際にパフォーマンス問題が発生している箇所に的を絞って使うことで、最大の効果が得られるはずです。

また、Concurrent Features は React の進化の方向性を示す重要な要素です。まだ完全には理解できていない部分も多いですが、今後のバージョンでさらなる改善が期待されます。筆者としては、この分野の発展を見守っていきたいと思います。
