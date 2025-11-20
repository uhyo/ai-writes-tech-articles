---
title: "Next.js 14のServer Actionsを試してエラーハンドリングの挙動を調べる"
emoji: "🔥"
type: "tech"
topics: ["nextjs", "react", "typescript", "serveractions"]
published: true
---

皆さんこんにちは。Next.js 14で正式リリースされた**Server Actions**を本格的に使い始めたのですが、エラーハンドリングの挙動が予想と違って困った場面がありました。今回はその挙動を調べた結果を共有します。

## Server Actionsの基本的なエラー

まず、Server Actionsで例外が発生した場合にどうなるのか、シンプルなケースから見ていきます。

```typescript
'use server'

export async function submitForm(formData: FormData) {
  const name = formData.get('name')

  if (!name) {
    throw new Error('名前が入力されていません')
  }

  // 処理続行
  return { success: true }
}
```

このコードをクライアントコンポーネントから呼び出すと、エラーが発生します。

```tsx
'use client'

export default function MyForm() {
  async function handleSubmit(formData: FormData) {
    const result = await submitForm(formData)
    console.log(result)
  }

  return (
    <form action={handleSubmit}>
      <input name="name" />
      <button type="submit">送信</button>
    </form>
  )
}
```

これを試したところ、ブラウザのコンソールに `Error: 名前が入力されていません` が出力されました。ただし、ここで注目すべきは**クライアント側でcatchしていないのに、アプリケーション全体がクラッシュしない**という点です。

筆者は最初、これはサーバー側の例外なので何らかの形でアプリケーションが落ちると思っていました。しかし実際には、Next.jsがServer Actionsの例外を内部的にキャッチして、クライアント側にエラー情報を伝播させる仕組みになっています。

## try-catchで処理した場合の挙動

では、クライアント側でtry-catchを使った場合はどうなるのか試してみます。

```tsx
'use client'

export default function MyForm() {
  const [error, setError] = useState<string | null>(null)

  async function handleSubmit(formData: FormData) {
    try {
      const result = await submitForm(formData)
      console.log(result)
      setError(null)
    } catch (e) {
      if (e instanceof Error) {
        setError(e.message)
      }
    }
  }

  return (
    <form action={handleSubmit}>
      {error && <div className="error">{error}</div>}
      <input name="name" />
      <button type="submit">送信</button>
    </form>
  )
}
```

これを実行すると、`名前が入力されていません` というメッセージが正常にキャッチできました。つまり、Server Actionsで投げたErrorオブジェクトは、ネットワークを経由してクライアント側で再構築されるということです。

ところで、エラーメッセージ以外のプロパティはどうなるのか気になったので、カスタムエラークラスを試してみました。

```typescript
'use server'

class ValidationError extends Error {
  code: string

  constructor(message: string, code: string) {
    super(message)
    this.code = code
  }
}

export async function submitForm(formData: FormData) {
  throw new ValidationError('入力エラー', 'VALIDATION_ERROR')
}
```

このコードをクライアントで受け取って `e.code` を参照したところ、undefinedでした。どうやら、Server Actionsのエラーは `message` と `stack` のみがシリアライズされて送信されるようです。Next.jsのissue #55180でも同じような議論がされていて、カスタムプロパティを渡したい場合は別の方法を考える必要があることがわかりました。

## 非同期処理中のエラー

次に、Server Action内で非同期処理を実行している最中にエラーが発生した場合を調べます。

```typescript
'use server'

export async function fetchUserData(userId: string) {
  // 外部APIを呼び出す想定
  const response = await fetch(`https://api.example.com/users/${userId}`)

  if (!response.ok) {
    throw new Error(`APIエラー: ${response.status}`)
  }

  return response.json()
}
```

このコードで、APIが500エラーを返した場合、クライアント側では `APIエラー: 500` という例外を受け取ることができます。個人的にはちょっとびっくりしたのですが、非同期処理のエラーもServer Actionsの枠組みの中で適切に処理されていました。

ただし、ここで注意が必要なのは、fetchが完全にタイムアウトした場合です。

```typescript
'use server'

export async function slowFetch() {
  // 60秒以上かかる処理
  const controller = new AbortController()
  const timeoutId = setTimeout(() => controller.abort(), 70000)

  const response = await fetch('https://slow-api.example.com/data', {
    signal: controller.signal
  })

  clearTimeout(timeoutId)
  return response.json()
}
```

このコードを実行すると、Next.jsのServer Actionsのデフォルトタイムアウト（確か60秒だったはず？）に引っかかって、クライアント側では `AbortError` が発生します。推測ですが、Next.js側で設定されているタイムアウトと、fetch自体のタイムアウトが競合する可能性があるので、長時間実行される処理には注意が必要です。

## ネストしたServer Actionsでのエラー伝播

Server Actionsは他のServer Actionsを呼び出すことができます。この場合、エラーがどのように伝播するのか見ていきます。

```typescript
'use server'

async function validateUser(userId: string) {
  if (!userId) {
    throw new Error('ユーザーIDが必要です')
  }
  return true
}

async function fetchUserProfile(userId: string) {
  await validateUser(userId)

  // プロフィール取得処理
  return { name: 'test user' }
}

export async function updateProfile(formData: FormData) {
  const userId = formData.get('userId') as string
  const profile = await fetchUserProfile(userId)

  // 更新処理
  return { success: true }
}
```

このコードで `userId` を空にして実行すると、`validateUser` で投げたエラーが `fetchUserProfile` を経由して、最終的に `updateProfile` を呼び出したクライアント側まで伝播しました。

つまり、Server Actions内部でどれだけ深く関数がネストしていても、エラーは適切に上位まで伝播するということです。これは普通のJavaScriptの挙動と同じなので当たり前といえば当たり前なのですが、ネットワーク境界を越えてこの挙動が保たれているのは便利です。

ただし、ここで問題が一つあります。内部的に複数のServer Actionsを呼び出している場合、どの関数でエラーが発生したのか特定しづらいです。スタックトレースを見れば分かるのですが、本番環境ではスタックトレースが省略されることがあるので、エラーメッセージに十分な情報を含めておく必要があります。

## 実際のプロジェクトで遭遇した罠

筆者が開発している社内の問い合わせ管理ツールで、Server Actionsを使ったフォーム送信を実装したときに、予想外の挙動に遭遇しました。

具体的には、フォームのバリデーションエラーとデータベース接続エラーを区別して表示したかったのですが、Server Actionsで投げたErrorオブジェクトのカスタムプロパティがクライアント側で受け取れなかったため、エラーメッセージだけで判断するしかありませんでした。

最初は、こんなコードを書いていました。

```typescript
'use server'

class ValidationError extends Error {
  statusCode = 400
}

class DatabaseError extends Error {
  statusCode = 500
}

export async function submitInquiry(formData: FormData) {
  const email = formData.get('email')

  if (!email) {
    throw new ValidationError('メールアドレスが必要です')
  }

  try {
    await db.insert(email)
  } catch (e) {
    throw new DatabaseError('データベースエラー')
  }
}
```

これをクライアント側で受け取って `error.statusCode` を見ようとしたところ、undefinedでした。先ほど説明したように、カスタムプロパティはシリアライズされないからです。

結局、こういう実装に変更しました。

```typescript
'use server'

export async function submitInquiry(formData: FormData) {
  const email = formData.get('email')

  if (!email) {
    return { error: 'メールアドレスが必要です', type: 'validation' }
  }

  try {
    await db.insert(email)
    return { success: true }
  } catch (e) {
    return { error: 'データベースエラー', type: 'database' }
  }
}
```

例外を投げるのではなく、エラー情報を含むオブジェクトを返す方式に変更したわけです。これで、クライアント側でエラーの種類を判別できるようになりました。

この方式は、Twitterで見たのですが、Remixコミュニティでも一般的なパターンらしいです。Server ActionsとRemixのactionは似た仕組みなので、参考になるノウハウは多いと感じています。

ただ、この実装には問題もあって、すべてのServer Actionsで統一的なエラーハンドリングを強制できないという点です。型システムで `Result<T, E>` みたいなパターンを強制できれば良いのですが、まだそこまで整理できていません。zodみたいなライブラリと組み合わせる方法を今後試してみたいと思っています。

## エラーバウンダリとの関係

最後に、React 19の**エラーバウンダリ**とServer Actionsの関係についても少し調べました。

```tsx
'use client'

import { ErrorBoundary } from 'react-error-boundary'

export default function MyPage() {
  return (
    <ErrorBoundary fallback={<div>エラーが発生しました</div>}>
      <MyForm />
    </ErrorBoundary>
  )
}
```

この構成で、Server Actionsが例外を投げた場合、エラーバウンダリがキャッチしてくれるのか試したところ、キャッチされませんでした。

理由を考えてみたのですが、Server Actionsの例外は非同期処理の結果として伝播するため、Reactのレンダリングフロー外で発生します。そのため、エラーバウンダリの範囲外になるということだと思います。

まだ試してないのですが、Next.js 15のドキュメントには**unhandled rejection**に関する記述があったので、そちらの仕組みで対応できる可能性があります。筆者としては、この辺りの挙動がどう変わっていくのか見守っていきたいと思います。

ちなみに、Server Componentsで発生したエラーはエラーバウンダリでキャッチできるので、Server ActionsとServer Componentsでエラーハンドリングの方式が異なるという点は注意が必要です。この境界が曖昧で、最初は混乱しました。
