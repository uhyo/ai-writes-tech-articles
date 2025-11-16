---
title: "Viteのビルド最適化とTree Shakingの実践テクニック"
emoji: "🌳"
type: "tech"
topics: ["vite", "javascript", "performance", "build"]
published: true
---

皆さんこんにちは。Viteの採用が進む中、本番ビルドの最適化について考える機会が増えてきました。筆者も最近、**Tree Shaking**の挙動を詳しく調べる必要があったのですが、意外と理解が浅かったことに気づきました。

Tree Shakingはバンドルサイズを削減する重要な仕組みです。実際にどこまで最適化されるのか、どういったコードパターンが最適化を妨げるのか、試してみないとわからない部分が多いです。

この記事では、Viteのビルド最適化とTree Shakingについて、実際にコードを書きながら挙動を確認していきます。

## Tree Shakingの基本原理

Tree Shakingは、使われていないコードを削除することでバンドルサイズを削減する技術です。**ESモジュール**の静的な構造を利用して、実際に参照されているexportだけを残します。

Viteは内部で**Rollup**を使っているため、RollupのTree Shaking機能がそのまま利用できます。基本的には、`import`していない関数や変数は最終的なバンドルから削除されるはずです。

まずは簡単な例から。次のようなユーティリティモジュールを考えます。

```typescript
// utils.ts
export function usedFunction() {
  return "This will be included";
}

export function unusedFunction() {
  return "This will be removed";
}

export const USED_CONSTANT = 42;
export const UNUSED_CONSTANT = 100;
```

```typescript
// main.ts
import { usedFunction, USED_CONSTANT } from './utils';

console.log(usedFunction());
console.log(USED_CONSTANT);
```

このコードをビルドすると、`unusedFunction`と`UNUSED_CONSTANT`は最終的なバンドルに含まれないはずです。実際にViteでビルドして確認してみましょう。

```bash
npm run build
```

生成されたバンドルを見ると、確かに`unusedFunction`のコードは削除されているはずです。これがTree Shakingの基本的な動作です。

## Viteでのビルド最適化設定

Viteのビルド設定は`vite.config.js`で細かく調整できます。Tree Shakingに関連する主要な設定を確認してみます。

```javascript
// vite.config.js
import { defineConfig } from 'vite';

export default defineConfig({
  build: {
    rollupOptions: {
      output: {
        manualChunks: (id) => {
          if (id.includes('node_modules')) {
            return 'vendor';
          }
        },
      },
    },
    minify: 'terser',
    terserOptions: {
      compress: {
        drop_console: true,
        drop_debugger: true,
      },
    },
  },
});
```

`manualChunks`は、依存関係をどのチャンクに分割するかを制御します。`node_modules`の中身を別チャンクにすることで、アプリケーションコードの変更時にvendorチャンクをキャッシュできるようになります。

`minify`オプションで圧縮方法を指定できます。デフォルトは`esbuild`ですが、`terser`を使うとより細かい制御が可能です。ただし、terserの方が遅いので、ビルド時間とのトレードオフになります。

筆者としては、開発時は`esbuild`で高速に、本番ビルドだけ`terser`を使うというのが現実的な選択肢だと考えています。

## 実際のバンドルサイズを確認する

理論だけでなく、実際のバンドルサイズを測定してみるのが重要です。Viteには`rollup-plugin-visualizer`を組み込むことで、バンドル内容を可視化できます。

```bash
npm install -D rollup-plugin-visualizer
```

```javascript
// vite.config.js
import { defineConfig } from 'vite';
import { visualizer } from 'rollup-plugin-visualizer';

export default defineConfig({
  plugins: [
    visualizer({
      open: true,
      gzipSize: true,
      brotliSize: true,
    }),
  ],
});
```

ビルド後、`stats.html`が生成されて自動的にブラウザで開かれます。このビジュアライゼーションを見ると、どのモジュールがバンドルサイズに寄与しているかが一目でわかります。

よく見られるケースとして、lodashのような大きなライブラリを丸ごとインポートしている例があります。

```typescript
// ❌ 悪い例：lodashを丸ごとインポート
import _ from 'lodash';

const result = _.debounce(fn, 300);
```

```typescript
// ✅ 良い例：必要な関数だけインポート
import debounce from 'lodash/debounce';

const result = debounce(fn, 300);
```

後者の方が、Tree Shakingが効きやすくなるはずです。visualizerで確認すると、バンドルサイズが数十KB削減されることもあります。これは実際のプロジェクトでよく見られる最適化ポイントです。

## Tree Shakingが効かないケース

Tree Shakingには限界があります。いくつか典型的なケースを試してみます。

### 副作用のあるコード

**副作用**（side effect）があるコードは、Tree Shakingで削除されません。

```typescript
// side-effect.ts
export function setup() {
  console.log("Setting up...");
}

// グローバルな副作用
console.log("This file has side effects");
```

このファイルを`import`すると、たとえ`setup`関数を使わなくても、ファイル全体が評価されます。`console.log("This file has side effects")`は必ず実行されるはずです。

これを回避するには、`package.json`の`sideEffects`フィールドで明示的に指定する必要があります。

```json
{
  "sideEffects": false
}
```

`sideEffects: false`と宣言すると、バンドラーは「このパッケージには副作用がない」と判断して、より積極的にTree Shakingを行います。

ただし、CSSファイルのインポートなど、実際に副作用が必要な場合もあります。その場合は配列で指定する形になります。

```json
{
  "sideEffects": ["*.css", "*.scss"]
}
```

この設定により、CSS以外のファイルは副作用がないと扱われます。筆者が観察する限り、この設定が正しく機能すると、バンドルサイズが大幅に削減されるケースがあるはずです。

### 動的インポートとTree Shaking

動的インポート（`import()`）は、Tree Shakingとの相性が微妙です。

```typescript
async function loadModule(name: string) {
  if (name === 'feature-a') {
    const module = await import('./feature-a');
    return module.default;
  }
}
```

このコードでは、`feature-a`が実際に使われるかどうかが実行時にしか決まらないため、静的解析が困難です。結果として、Tree Shakingの効果が限定的になる可能性があります。

動的インポートを使う場合は、チャンク分割とのトレードオフを考慮する必要があります。コード分割によって初期ロードサイズは削減できますが、Tree Shakingの最適化度合いは下がる傾向にあります。この点は実装時に注意が必要です。

## 最適化の限界を探る

最後に、Tree Shakingの限界を少し深掘りしてみます。

TypeScriptの**enum**は、Tree Shakingが効きにくいことで知られています。

```typescript
// enums.ts
export enum Status {
  Active = "active",
  Inactive = "inactive",
  Pending = "pending",
}
```

```typescript
// main.ts
import { Status } from './enums';

console.log(Status.Active);
```

TypeScriptのenumは、コンパイル後にオブジェクトとして展開されます。この展開されたコードは、バンドラーから見ると「オブジェクト全体が使われている」と判断される可能性が高いです。筆者もこの挙動には注意が必要だと感じています。

```javascript
// コンパイル後（簡略化）
const Status = {
  Active: "active",
  Inactive: "inactive",
  Pending: "pending",
};
```

結果として、`Status.Active`しか使っていなくても、enum全体がバンドルに含まれるはずです。

この問題を回避する方法として、const enumを使う選択肢があります。

```typescript
export const enum Status {
  Active = "active",
  Inactive = "inactive",
  Pending = "pending",
}
```

const enumは、使用箇所がリテラルに置き換えられるため、enum定義そのものがバンドルに含まれません。ただし、const enumには別の制約（isolatedModulesとの非互換性など）があるため、プロジェクトによっては使えないこともあります。

筆者の考えとしては、enumの代わりに**union型**を使う方が、型安全性とバンドルサイズの両立ができると考えられます。

```typescript
export type Status = "active" | "inactive" | "pending";
```

これなら型情報だけで済むため、ランタイムコードが生成されません。Viteのような最新のビルドツールでは、こういった型ベースのアプローチが推奨されることが多いです。

## まとめ

ViteのTree Shakingは強力ですが、完璧ではありません。副作用のあるコード、動的インポート、TypeScriptのenumなど、Tree Shakingが効きにくいパターンは確かに存在します。これらの特性を理解しておくことが重要です。

実際のプロジェクトでは、`rollup-plugin-visualizer`のようなツールで定期的にバンドル内容を確認し、意図しない肥大化が起きていないかチェックするのが重要です。

筆者としては、今後Vite 6やRollup 4の進化によって、さらに最適化が進むことを期待しています。特に、動的インポート周りの静的解析がどこまで賢くなるか、見守っていきたいと思います。
