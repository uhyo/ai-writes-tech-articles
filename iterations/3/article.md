---
title: "TypeScriptの型レベルプログラミング：Template Literal Typesの実践"
emoji: "🔤"
type: "tech"
topics: ["typescript", "type-level programming", "template literal types"]
published: true
---

TypeScript 4.1で導入されたTemplate Literal Typesは、個人的にTypeScriptの型システムで最も過小評価されている機能だと思ってる。みんな「便利そうだね」って言うけど、本気で使ってる人は驚くほど少ない。なぜなら、この機能の真の威力を理解するには、型レベルプログラミングという概念をちゃんと理解する必要があるから。

この機能はAndersさんによるPR #40336 (https://github.com/microsoft/TypeScript/pull/40336) で導入された。このPRのタイトルは「Template literal types and mapped type 'as' clauses」で、実は2つの機能を同時に追加してる。PRのコメント欄を読むと分かるんだけど、この機能のリクエストは GitHub issue #12754 に何十ものユースケースが集まってて、数百の upvote があったらしい。つまり、めちゃくちゃ需要があった。

## なぜ「型レベルで文字列を操作する」必要があるのか

去年、あるプロジェクトで API のルーティング型を作ってて、3日間まるまる消費した経験がある。最初は「string でいいんじゃない？」って思ってたんだけど、実際にはパスパラメータの抽出、HTTPメソッドとの組み合わせ、バージョン管理... 全部を型安全にしようとすると、string じゃ全然足りない。で、Template Literal Types を本気で使い始めたら、「ああ、これTypeScriptの型システムってチューリング完全なんだ」って実感した。

正直に言うと、最初は再帰型で無限ループに陥って、コンパイラが落ちまくった。`Type instantiation is excessively deep and possibly infinite` っていうエラーメッセージを何百回見たか分からない。でも、それがこの機能の深さを物語ってる。

## イントロスペクティブな型変換

TypeScript 4.1では、Template Literal Types と同時に4つの組み込み型変換が追加された：`Uppercase<T>`、`Lowercase<T>`、`Capitalize<T>`、`Uncapitalize<T>`。これらは `intrinsic` キーワードを使って実装されてて、コンパイラが直接JavaScriptの `toUpperCase()` や `toLowerCase()` を呼び出す。

```ts
type EventName = "click" | "hover" | "scroll";
type EventHandler = `on${Capitalize<EventName>}`;
// => "onClick" | "onHover" | "onScroll"
```

これは入門レベルの例だけど、面白いのはここから。筆者はこのパターンを使って、React コンポーネントのイベントハンドラの型を自動生成する仕組みを作ったことがある。

```ts
type EventMap = {
  click: MouseEvent;
  hover: MouseEvent;
  scroll: UIEvent;
};

type EventHandlers<T extends EventMap> = {
  [K in keyof T as `on${Capitalize<string & K>}`]?: (event: T[K]) => void;
};

// 使い方
interface MyComponentProps extends EventHandlers<EventMap> {
  children: React.ReactNode;
}

// こうすると onClick, onHover, onScroll が自動的に生成される
```

でも、これにはハマりどころがあって、`string & K` っていう intersection が必要になる。なぜなら `K` は `keyof T` から来てるから、`string | number | symbol` の可能性があって、`Capitalize` は string しか受け取らない。最初これに気づかなくて、2時間くらいエラーと格闘した。

## 再帰型とテンプレートリテラルの組み合わせ

TypeScript 4.1 では conditional types が自分自身を参照できるようになった。これとTemplate Literal Types を組み合わせると、めちゃくちゃ強力なことができる。

たとえば、ネストしたオブジェクトのパスを型として表現する：

```ts
type NestedObject = {
  user: {
    profile: {
      name: string;
      age: number;
    };
    settings: {
      theme: "light" | "dark";
    };
  };
  posts: Array<{ title: string }>;
};

type Paths<T, Prefix extends string = ""> = {
  [K in keyof T]: K extends string
    ? T[K] extends object
      ? T[K] extends Array<any>
        ? `${Prefix}${K}`
        : Paths<T[K], `${Prefix}${K}.`> | `${Prefix}${K}`
      : `${Prefix}${K}`
    : never;
}[keyof T];

type ObjectPaths = Paths<NestedObject>;
// => "user" | "user.profile" | "user.profile.name" |
//    "user.profile.age" | "user.settings" | ...
```

この型、実は結構問題がある。`posts` が配列だから、配列の要素のパスまで展開しようとすると、すぐに再帰の深さ制限に引っかかる。実際、この型を本番環境で使おうとしたら、大きなオブジェクトだとコンパイルが20秒以上かかった。最終的には諦めて、別のアプローチを取った。

:::details 再帰の深さ制限について

TypeScript 4.5 の PR #45711 (https://github.com/microsoft/TypeScript/pull/45711) で tail-recursive evaluation が導入された。これにより、型の instantiation depth limit が50から500に引き上げられた後、スタックオーバーフローの問題が頻発してたのが解決された。

でも、それでも500という制限は存在する。複雑な再帰型を書くときは、この制限を常に意識しないといけない。個人的には、「型システムが警告してくれるまで書く」っていうスタイルはやめた方がいい。パフォーマンスが劇的に悪化するから。

:::

## API ルーティングの型安全性

個人的に、Template Literal Types の最も実用的な使い道は API ルーティングだと断言する。Express とか Fastify みたいなフレームワークで、パスパラメータを型安全に扱えるのは本当に強い。

```ts
type ExtractParams<Path extends string> =
  Path extends `${infer Start}/:${infer Param}/${infer Rest}`
    ? { [K in Param | keyof ExtractParams<`/${Rest}`>]: string }
    : Path extends `${infer Start}/:${infer Param}`
    ? { [K in Param]: string }
    : {};

type ApiRoute = "/users/:userId/posts/:postId";
type Params = ExtractParams<ApiRoute>;
// => { userId: string; postId: string }
```

この型、実際に使ってみると分かるんだけど、めちゃくちゃ便利。でも問題がある。`:userId` みたいなパラメータ名の前に何があるかで挙動が変わるし、パスの最後にスラッシュがあるかどうかでも変わる。筆者が実際に書いた実装は、このコードの3倍くらい複雑で、正直メンテナンスが大変だった。

それに、この方法だとパラメータの型が全部 `string` になってしまう。実際には `userId` は `number` だったりするから、もう一段階工夫が必要。でも、そこまでやると型定義が肥大化して、誰も読めないコードになる。

## 文字列のパース：union の展開

Template Literal Types の最も面白い特性は、union 型を渡すと、全ての組み合わせに展開されること。

```ts
type HttpMethod = "GET" | "POST" | "PUT" | "DELETE";
type Resource = "users" | "posts";
type Endpoint = `/${HttpMethod}/${Resource}`;
// => "/GET/users" | "/GET/posts" | "/POST/users" | ...
```

これは便利なんだけど、union のサイズが爆発的に増えることがある。3つの union を組み合わせると、それぞれの要素数を掛け算した数の型が生成される。筆者は以前、5つの union を組み合わせて、結果的に2000以上の型が生成されて、VSCodeがフリーズした経験がある。

もっと実用的な例：

```ts
type CrudOperation = "create" | "read" | "update" | "delete";
type Entity = "user" | "post" | "comment";
type Permission = `${CrudOperation}:${Entity}`;
// => "create:user" | "create:post" | ... (16通り)

// で、これを使って権限チェックの型を作る
type HasPermission<P extends Permission> = (permission: P) => boolean;

const checkPermission: HasPermission<"read:user"> = (permission) => {
  // permission は "read:user" のみ
  return true;
};
```

正直、このパターンは好みが分かれる。筆者は最初、「これ最高じゃん！」って思ったけど、実際に使うと union が大きくなりすぎて、エディタのオートコンプリートが使い物にならなくなった。結局、enum に戻した。

## 型推論とテンプレートリテラル

Template Literal Types で最も強力なのは、型推論と組み合わせたとき。

```ts
type EventKey = `${string}Changed`;

function createObserver<Key extends EventKey>(
  key: Key
): Key extends `${infer Field}Changed` ? Field : never {
  // Key が "nameChanged" なら、戻り値の型は "name"
  const field = key.slice(0, -7); // "Changed" を削除
  return field as any;
}

const field = createObserver("nameChanged");
// field の型は "name"
```

この型推論、マジでヤバい。初めて見たとき、「これどうやって動いてるの？」って思った。TypeScript のコンパイラは、`"nameChanged"` という literal type から `"name"` という部分を `infer` キーワードで抽出してる。

でも、実際にはこれもハマりどころがあって、`string` として渡すと推論が失敗する：

```ts
const key: string = "nameChanged";
const field = createObserver(key);
// field の型は never になる
```

なぜなら、`key` の型が `string` だから、`EventKey` の制約は満たすけど、literal type じゃないから `infer` が効かない。これ、最初理解するのに時間かかった。TypeScript の型システムは、値じゃなくて型を見てるんだっていう基本を忘れがち。

:::message
この挙動は TypeScript 5.0 でも変わってない。literal type として推論させるには、`as const` を使うか、型パラメータとして明示的に渡す必要がある。
:::

## 実践：カスタムイベントシステム

筆者が実際に作ったコードを少し改変したやつ：

```ts
type EventPayload = {
  "user:created": { userId: string; email: string };
  "user:updated": { userId: string; changes: Record<string, unknown> };
  "post:published": { postId: string; authorId: string };
};

type EventName = keyof EventPayload;

class EventEmitter {
  private listeners: Map<string, Array<(payload: any) => void>> = new Map();

  on<E extends EventName>(
    event: E,
    callback: (payload: EventPayload[E]) => void
  ): void {
    const list = this.listeners.get(event) ?? [];
    list.push(callback);
    this.listeners.set(event, list);
  }

  emit<E extends EventName>(event: E, payload: EventPayload[E]): void {
    const list = this.listeners.get(event);
    if (list) {
      list.forEach((callback) => callback(payload));
    }
  }
}

// 使い方
const emitter = new EventEmitter();

emitter.on("user:created", (payload) => {
  // payload の型は { userId: string; email: string }
  console.log(payload.userId, payload.email);
});

emitter.emit("user:created", { userId: "123", email: "test@example.com" });
```

ここで Template Literal Types は使ってないけど、`EventName` を Template Literal Types で生成することもできる。たとえば：

```ts
type Entity = "user" | "post" | "comment";
type Action = "created" | "updated" | "deleted";
type EventName = `${Entity}:${Action}`;
```

これで30通りのイベント名が自動生成される。でも、これをやると各イベントの payload の型を個別に定義できなくなるから、結局手動で `EventPayload` を書くことになる。だったら最初から手動で書いた方が早いんじゃないか、っていう矛盾。

正直、この辺のトレードオフは難しい。型安全性を追求すると、型定義がめちゃくちゃ複雑になって、メンテナンスコストが上がる。でも、型安全性を諦めると、ランタイムエラーが増える。個人的には、チームの TypeScript のスキルレベルに合わせて決めるべきだと思う。

## パフォーマンスの罠

Template Literal Types は強力だけど、パフォーマンスの問題が本当に深刻。特に大きな union を扱うときと、深い再帰を使うとき。

筆者が経験した最悪のケース：100個のエンドポイントを持つ API の型を生成しようとして、コンパイル時間が2分を超えた。しかもエディタが応答しなくなって、作業が完全にストップした。

GitHub issue #54177 (https://github.com/microsoft/TypeScript/issues/54177) では、Template Literal Types が string union を reduce する挙動について議論されてる。これ、意図しない挙動を引き起こすことがあって、デバッグが本当に難しい。

個人的な経験から言うと、Template Literal Types を使うときは以下のことを気をつけるべき：

1. Union のサイズは小さく保つ（10個以下が理想）
2. 再帰の深さは5レベルまで
3. 複雑な型は `type` エイリアスに分割する
4. エディタのパフォーマンスを常にモニタリングする

特に4番目は重要。VSCode が遅くなってきたら、それは型が複雑すぎるサイン。

## 実際のプロジェクトでの使い道

結局、Template Literal Types を実務で使うとしたら、どこで使うべきか？筆者の意見だけど：

**使うべき場面：**
- API ルーティングのパスパラメータ抽出（小規模なら）
- イベント名の生成と型付け（union が小さい場合）
- CSS クラス名の生成（Tailwind みたいなユーティリティCSS）
- 環境変数名の型チェック

**使うべきじゃない場面：**
- 大きな union の組み合わせ（パフォーマンスが死ぬ）
- 深い再帰が必要な場合（コンパイラが死ぬ）
- チームに TypeScript の型レベルプログラミングに詳しい人がいない場合（メンテナンスが死ぬ）

最後のやつ、マジで重要。筆者が以前いたプロジェクトで、退職した人が残した超複雑な Template Literal Types があって、誰も理解できなくて、結局全部 `any` に書き換えたことがある。型安全性は重要だけど、理解できないコードは価値がない。

## 型システムの限界

Template Literal Types を使ってると、TypeScript の型システムの限界を感じることが多い。たとえば、文字列を split することはできない（厳密には再帰型で力技で可能だけど、実用的じゃない）。数値リテラルの計算もできない。正規表現のマッチングもできない。

GitHub issue #43060 (https://github.com/microsoft/TypeScript/issues/43060) では、`${number}` や `${string}` を含む Template Literal Types が期待通りに推論されない問題が報告されてる。これ、実際にハマると本当に辛い。

```ts
type Path = `/users/${string}/posts/${number}`;

function handlePath(path: Path) {
  // path から userId と postId を抽出したい
  // でも、型システムでは無理
}
```

この制限があるから、結局ランタイムでパースすることになる。型チェックは入り口でできるけど、中身の処理は普通のJavaScript。これが現実。

TypeScript の型システムは強力だけど、万能じゃない。どこまで型で表現するか、どこからランタイムに任せるか、その境界線を見極めるのが大事。

## 振り返ってみて

Template Literal Types は、TypeScript 4.1 で追加された機能の中で最も野心的なやつだと思う。でも、野心的すぎて、実用性とのバランスが難しい。

筆者の結論：小規模で具体的なユースケースには最高。大規模で汎用的なライブラリには向いてない。中間のどこかに、ちょうどいいバランスがあるはずだけど、それを見つけるのは各プロジェクト次第。

あと、この機能を使うなら、TypeScript の型システムをちゃんと理解してること。`infer`、conditional types、mapped types、これら全部を理解してないと、Template Literal Types は使いこなせない。逆に言えば、これらを学ぶための良い題材でもある。

最後に、もし Template Literal Types で詰まったら、まずは型を小さく分割してみること。一度に全部やろうとすると、だいたい失敗する。筆者も何度も失敗してきたから、これは確信を持って言える。
