---
title: "Next.js 14のServer ActionsでフォームValidationを実装する際の実践パターン"
emoji: "📝"
type: "tech"
topics: ["nextjs", "react", "typescript", "serveractions"]
published: true
---

# Next.js 14のServer ActionsでフォームValidationを実装する際の実践パターン

皆さんこんにちは。Next.js 14で正式に安定版となった**Server Actions**について、筆者が開発しているReactアプリケーションでフォームValidationを実装する際に考えたパターンをまとめます。Server Actionsは従来のAPI Routesと比べて記述が簡潔ですが、**型安全性**やエラーハンドリングの設計には工夫が必要です。

:::message
この記事はNext.js 14.0時点の挙動を前提としています。Next.js 15以降では仕様が変わる可能性があります。
:::

## Server Actionsの基本

Server Actionsは、サーバー側で実行される非同期関数をクライアントから直接呼び出せる機能です。従来のAPI Routesと比べて、エンドポイントの定義が不要になるのが特徴です。最もシンプルな例を見てみます。

```tsx
// app/actions.ts
'use server'

export async function createUser(formData: FormData) {
  const name = formData.get('name') as string
  const email = formData.get('email') as string
  await db.users.create({ name, email })
  return { success: true }
}
```

フォームから呼び出すには、`action`プロパティに直接渡します。

```tsx
// app/page.tsx
export default function Page() {
  return (
    <form action={createUser}>
      <input name="name" type="text" />
      <input name="email" type="email" />
      <button type="submit">送信</button>
    </form>
  )
}
```

非常にシンプルですが、このままでは入力値の検証ができません。不正な値がそのまま処理されてしまいます。実際のアプリケーションでは、Validationの実装が必須です。

## Validation Schemaの統合

Validationを実装するなら、zodのようなスキーマライブラリを使うのが現実的です。筆者も最近、フォーム処理の設計を考える機会がありました。

```tsx
'use server'
import { z } from 'zod'

const userSchema = z.object({
  name: z.string().min(1, '名前は必須です'),
  email: z.string().email('正しいメールアドレスを入力してください'),
})

export async function createUser(formData: FormData) {
  const rawData = {
    name: formData.get('name'),
    email: formData.get('email'),
  }

  const result = userSchema.safeParse(rawData)
  if (!result.success) {
    return { success: false, errors: result.error.flatten().fieldErrors }
  }

  await db.users.create(result.data)
  return { success: true }
}
```

エラーハンドリングをServer Action側で行い、結果オブジェクトとして返すパターンです。この設計により、サーバー側で一元的にValidationを管理できます。

## useFormStateでエラー表示

Next.js 14では**useFormState**フックが提供されており、Server Actionの返却値を状態として扱えます。

```tsx
'use client'
import { useFormState } from 'react-dom'

export default function UserForm() {
  const [state, formAction] = useFormState(createUser, null)

  return (
    <form action={formAction}>
      <input name="name" type="text" />
      {state?.errors?.name && <p>{state.errors.name[0]}</p>}

      <input name="email" type="email" />
      {state?.errors?.email && <p>{state.errors.email[0]}</p>}

      <button type="submit">送信</button>
      {state?.success && <p>登録完了</p>}
    </form>
  )
}
```

ところが、ここで問題がある。TypeScriptの型推論がうまく効かないのです。`state`の型は`any`になってしまい、型安全性が失われます。筆者がこれを書いている時点では、GitHubのNext.js issuesでも議論されているようです。現状では、返却値の型を明示的に定義するしかないと考えられます。

```tsx
type ActionState = {
  success: boolean
  errors?: { name?: string[]; email?: string[] }
} | null

export async function createUser(prevState: ActionState, formData: FormData): Promise<ActionState> {
  // ...
}
```

`useFormState`を使う場合、Server Actionの第一引数に前の状態が渡される点に注意です。シグネチャが変わるので、型定義もそれに合わせて調整します。

## Pending状態の表示

フォーム送信中の状態を表示するには**useFormStatus**が使えます。ただし、このフックは`<form>`の子コンポーネントから呼び出す必要があります。

```tsx
'use client'
import { useFormStatus } from 'react-dom'

function SubmitButton() {
  const { pending } = useFormStatus()
  return <button disabled={pending}>{pending ? '送信中...' : '送信'}</button>
}

export default function UserForm() {
  return (
    <form action={formAction}>
      {/* ... */}
      <SubmitButton />
    </form>
  )
}
```

最初、筆者は同じコンポーネント内で`useFormStatus`を呼ぼうとして動かなかった。コンポーネント分割が必須だと気づきました。これはReactのContext APIの制約によるものと推測されます。

## 複数フィールドの複雑なValidation

実際のプロジェクトでは、単純なフィールドValidationだけでは不十分な場合があります。たとえば、パスワード確認フィールドのような**相互依存**するValidationです。

```tsx
const registerSchema = z.object({
  email: z.string().email(),
  password: z.string().min(8),
  passwordConfirm: z.string(),
}).refine((data) => data.password === data.passwordConfirm, {
  message: 'パスワードが一致しません',
  path: ['passwordConfirm'],
})

export async function registerUser(prevState: ActionState, formData: FormData): Promise<ActionState> {
  const result = registerSchema.safeParse({
    email: formData.get('email'),
    password: formData.get('password'),
    passwordConfirm: formData.get('passwordConfirm'),
  })

  if (!result.success) {
    return { success: false, errors: result.error.flatten().fieldErrors }
  }

  const hashedPassword = await hashPassword(result.data.password)
  await db.users.create({ email: result.data.email, password: hashedPassword })
  return { success: true }
}
```

zodの`refine`を使えば、オブジェクト全体を見た上でのValidationが可能です。エラーメッセージを特定のフィールドに紐づけるには`path`オプションを使います。パスワード以外にも、住所の入力チェックなど様々な場面で応用できます。

## リダイレクトとrevalidate

Server Action内でリダイレクトする場合、Next.jsの`redirect`関数を使います。データ更新後のキャッシュ制御には**revalidatePath**が重要です。

```tsx
import { redirect } from 'next/navigation'
import { revalidatePath } from 'next/cache'

export async function createUser(prevState: ActionState, formData: FormData) {
  // ... validation
  const user = await db.users.create(result.data)

  revalidatePath('/users')  // キャッシュの再検証
  redirect(`/users/${user.id}`)  // リダイレクト
}
```

注意したいのは、`redirect`を呼ぶと例外がthrowされるため、それ以降のコードは実行されない点です。TypeScriptの制御フロー分析では`redirect`の後ろが到達不能と認識されるはずです。

また、`revalidatePath`は`redirect`より前に呼ぶ必要があります。順序を間違えるとキャッシュが更新されないまま遷移してしまいます。これはNext.jsのキャッシュ機構の仕様によるものと考えられます。

## まとめ

Next.js 14のServer ActionsとフォームValidationについて、基本的なパターンから実践的な使い方まで見てきました。

個人的には、Server Actionsの簡潔さは魅力的ですが、型安全性の面でまだ改善の余地があると感じています。特に`useFormState`の型推論が弱い点は、将来的に改善されることを期待したいです。

また、zodのようなスキーマライブラリとの統合は必須と言えます。クライアント側でもValidationを行うべきかどうかは、UXとセキュリティのバランスを考える必要がありそうです。筆者としては、今後のNext.jsのアップデートでこの辺りがどう進化していくか見守っていきたいと思います。

Validationロジックの共有や、Server ComponentsとClient Componentsの境界設計など、まだ試していないパターンも多いので、引き続き検証していく予定です。
