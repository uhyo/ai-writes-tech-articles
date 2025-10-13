# Technical Article Style Guide

This guide defines the standards for generating technical articles that are indistinguishable from human-written content.

## Format Requirements

### Frontmatter

Every article must begin with YAML frontmatter:

```yaml
---
title: "記事のタイトル"
emoji: "🎯"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["typescript", "javascript", "react"]
published: true
---
```

- **title**: Descriptive title in Japanese
- **emoji**: Single relevant emoji
- **type**: Either "tech" (technical article) or "idea" (idea/opinion piece)
- **topics**: Array of relevant topic tags in lowercase English
- **published**: Boolean, typically `true`

### Markdown Features

Use Zenn-specific markdown features where appropriate:

```markdown
:::message
Important notes or disclaimers
:::

:::details 補足説明
Collapsible additional information
:::
```

## Language and Style

### Japanese Writing Standards

- **Primary language**: All content must be in Japanese
- **Technical terms**: Use established Japanese technical terms where they exist, but English terms are acceptable when commonly used in the Japanese tech community
- **Tone**: Conversational yet informative - use "です/ます" (polite form)
- **Personal address**: Start with "皆さんこんにちは" or similar friendly greetings when appropriate

### Writing Style Characteristics

1. **Conversational opener**: Begin with context-setting introduction
   - Avoid formulaic openings like "皆さんこんにちは。今回は〜について解説します" every time
   - Consider starting with a direct PR/issue reference, a specific problem, or an interesting observation
   - Examples: "TypeScriptの次のバージョンでは、以下のPRの更新が入ると思われます。もちろんPRの著者はAndersさんです。"

2. **Personal voice**: Use first-person perspective ("筆者", "私", "自分") when expressing opinions
   - Share your experiences: "個人的には〜", "筆者は〜", "自分はよく次のような関数を作ります"
   - Include subjective judgments: "〜が嬉しい場面がそれほどありません", "〜の方が好みです"
   - Don't be afraid to express preferences, frustrations, or discoveries
   - Balance objective technical explanation with personal perspective

3. **Natural flow**: Write as if explaining to a colleague over coffee, not lecturing in a classroom
   - Use conversational connectors: "そう、それは〜です", "試しに〜してみると", "さて、〜"
   - Vary sentence length and structure naturally
   - Include rhetorical pauses and thinking-out-loud moments
   - Let the conversation meander slightly when it adds value

4. **Appropriate formality**: Balance politeness with technical precision
   - Use "です/ます" form but keep it light and natural
   - Mix in casual expressions where appropriate: "〜ですね", "〜でしょう", "〜なんです"

## Article Structure

### Required Sections

1. **Introduction**
   - Set context and explain why the topic matters
   - **Avoid predictable patterns**: Don't always follow "挨拶 → 問題提起 → 今回の内容"
   - Consider these variations:
     - Start with a PR/issue reference: "以下のPRの更新が入ると思われます"
     - Begin with a specific problem or observation
     - Jump straight into an interesting technical detail
     - Use a question that leads naturally into the topic
   - May include links to related articles or background

2. **Main Content**
   - **Avoid textbook structure**: Don't rigidly follow "基本 → 応用 → まとめ" every time
   - Let the narrative flow naturally based on what's interesting
   - Consider these organization strategies:
     - Problem-first: Show the problem, then explain the background
     - Historical: Trace how the feature evolved over versions
     - Comparative: Explore multiple approaches side-by-side
     - Discovery-based: Lead the reader through a thought process
   - Use ## for main sections, ### for subsections
   - Include code examples with explanations
   - Reference official documentation, PRs, and issues extensively

3. **まとめ (Summary)**
   - **Go beyond simple recap**: Don't just list what was covered
   - Include these elements:
     - Personal reflection or "aha moment"
     - Future outlook or unsolved problems
     - Practical advice for readers
     - Optional: Invitation for discussion or feedback
   - Avoid generic phrases like "いかがでしたでしょうか"

### Optional But Recommended Sections

- **余談 (Side Notes)**: Use `:::details 余談` blocks liberally for:
  - Historical context and version history
  - Related but tangential observations
  - Personal anecdotes or experiences
  - Deep dives into implementation details

- **補足 (Supplements)**: Clarifications or edge cases using `:::message` or `:::details`

## Technical Content Standards

### Code Examples

- Use appropriate syntax highlighting (```ts, ```tsx, ```js, ```php, etc.)
- Provide context before and explanation after code blocks
- Keep examples focused and relevant
- Use comments in Japanese where helpful

Example:
```ts
// TypeScript 4.8では {} | undefined 型になる
function someFunc(x: unknown) {
  if (x !== null) {
    x; // 型が絞り込まれる
  }
}
```

### Technical Accuracy and Depth

- Explain concepts clearly and correctly
- **Go beyond surface explanations**: Dig into "why" not just "what"
  - Explain design decisions and tradeoffs
  - Discuss historical context: "この辺りの背景としては、〜という事情があります"
  - Reference version changes and evolution of features
  - Include implementation details when relevant

- **Reference sources extensively**:
  - Link to specific GitHub PRs and issues (not just generic documentation)
  - Include PR numbers and author names: "もちろんPRの著者はAndersさんです"
  - Reference TypeScript release notes, blog posts, or design documents
  - Cite specific line numbers or code sections when discussing implementations

- Acknowledge limitations or edge cases
- Differentiate facts from opinions/interpretations clearly
  - Facts: "TypeScript 4.8では〜が導入されました"
  - Interpretations: "これは〜という事情があると思われます"
  - Opinions: "個人的には〜の方が好みです"

### Links and References

- Link to official documentation
- Reference related articles (internal or external)
- Use footnotes for additional context: `[^note_name]`
- Format external links naturally in text

Example:
```markdown
この記事で紹介した機能については、[公式ドキュメント](https://example.com)を参照してください。

詳細は以下のPRで確認できます[^note_pr]。

[^note_pr]: https://github.com/microsoft/TypeScript/pull/49119
```

## Engagement and Authenticity

### Make It Human

**Share personal experiences and perspectives:**
- "筆者は〜" / "個人的には〜" / "自分はよく〜"
- Include concrete examples from your work: "実務で〜に遭遇した際"
- Share discoveries: "〜に気づいた", "〜が面白い"
- Express feelings: "〜が嬉しい", "〜に驚いた", "〜に困った"

**Use natural conversational patterns:**
- Rhetorical questions that actually add value (not formulaic ones)
- Good: "では、なぜこのような仕様になっているのでしょうか?"
- Avoid: "〜ではないでしょうか?" as a generic connector
- Use casual interjections: "さて", "ところで", "そう", "なお"
- Thinking out loud: "試しに〜してみると、次のように表示されます"

**Express appropriate uncertainty:**
- "〜と思われます" / "おそらく〜" / "〜の可能性があります"
- Be honest about limitations of your knowledge
- Distinguish between confirmed facts and speculation

**Include "aha moments" and insights:**
- Share non-obvious observations
- Explain connections that aren't immediately clear
- Provide that moment of understanding: "つまり、〜ということです"

### Avoid AI Patterns

**Language patterns to avoid:**
- ❌ Starting multiple paragraphs with "また" or "さらに"
- ❌ Overusing "非常に", "重要", "明確", "適切"
- ❌ Formulaic transitions: "それでは〜を見ていきましょう" repeatedly
- ❌ Generic questions: "〜ではないでしょうか" as a crutch
- ❌ Textbook explanations: "〜とは何か" followed by dictionary definitions

**Structural patterns to avoid:**
- ❌ Predictable section order: 基本 → 応用 → まとめ every time
- ❌ Exhaustive enumeration: listing every possible case mechanically
- ❌ Over-explanation of obvious points
- ❌ Same paragraph and sentence lengths throughout

**Tone patterns to avoid:**
- ❌ Being overly cautious or hedging everything
- ❌ Condescending explanations: "ご存知の通り〜"
- ❌ Robotic transitions between sections
- ❌ Generic conclusions: "いかがでしたでしょうか"

### Show Expertise

**Reference specific technical sources:**
- Cite PR numbers: "以下のPRで確認できます: https://github.com/microsoft/TypeScript/pull/49119"
- Mention authors: "Andersさんが〜", "このPRの著者は〜"
- Reference issues and discussions
- Link to design documents or RFCs

**Discuss implementation and design:**
- Explain why decisions were made: "〜という理由で〜になっています"
- Discuss tradeoffs: "〜のメリットは〜ですが、〜というデメリットもあります"
- Share historical context: "以前は〜でしたが、〜から〜に変更されました"
- Include version-specific information: "TypeScript 4.8以降では〜"

**Connect concepts and domains:**
- Draw parallels to related technologies
- Explain how different features interact
- Share practical patterns and anti-patterns
- Provide real-world use cases from actual development work

## Special Content Types

### Disclaimers

Use message blocks for important disclaimers:

```markdown
:::message
この記事は、Reactの公式見解を説明するものではありません。筆者の考察を紹介するものです。
:::
```

### Comparative Analysis

When comparing concepts or versions:
- Use clear headings for each item
- Provide concrete examples
- Explain practical implications
- May use tables for side-by-side comparison

## Examples from Human Articles

### Good Opening Example
```markdown
皆さんこんにちは。今回はTypeScriptの更新先取りシリーズです。TypeScriptの次のバージョンでは、以下のPRの更新が入ると思われます。
```

### Good Technical Explanation Example
```markdown
`{}`型は、「`null`と`undefined`以外の任意の値」という意味を持つ型です。この型は形としては空のオブジェクト型ですが、JavaScriptでは`null`と`undefined`以外のプリミティブ（文字列や数値など）に対してもプロパティアクセスをしてもエラーにならないという仕様を考慮して、`{}`型には文字列や数値などのプリミティブも含まれています。
```

## Critical Pitfalls to Avoid

### Textbook Syndrome
- ❌ "〜とは何か" followed by dictionary-like definitions
- ❌ Exhaustive enumeration of every possible case
- ❌ Rigid "基本 → 応用 → まとめ" structure every time
- ❌ Section titles that read like chapter headings: "Wideningとは何か"

### Formulaic Language
- ❌ Starting every paragraph the same way
- ❌ Overusing transitions: "また", "さらに", "一方", "しかし" in a mechanical pattern
- ❌ Excessive use of "今回は" or "それでは〜を見ていきましょう"
- ❌ Generic rhetorical questions: "〜ではないでしょうか?" as filler

### Artificial Tone
- ❌ Overly formal or stilted Japanese
- ❌ Explaining things in a condescending manner: "ご存知の通り"
- ❌ Being too cautious or hedging everything unnecessarily
- ❌ Lack of personal voice: sounding like a neutral explainer bot

### Shallow Content
- ❌ Surface-level explanations without "why"
- ❌ Missing references to PRs, issues, and version history
- ❌ Generic examples without real-world context
- ❌ Conclusions that just recap without adding insight

### Structural Issues
- ❌ Overly long introductions before getting to the point
- ❌ Generic conclusions: "いかがでしたでしょうか"
- ❌ Forcing rigid structure when natural flow is better
- ❌ Predictable article arc that readers can anticipate from the first paragraph

### Detail Issues
- ❌ Incorrect footnote references (e.g., `[^pr119]` when URL says `/pull/49119`)
- ❌ Overuse of adjectives like "非常に", "重要", "明確", "適切" without substance
- ❌ Same sentence structures and paragraph lengths throughout

## Quality Checklist

Before finalizing an article, verify:

**Format and Structure:**
- [ ] Frontmatter is complete and correct
- [ ] Has clear introduction and まとめ
- [ ] Structure is natural, not rigidly formulaic
- [ ] Section flow is logical but not predictable

**Content Quality:**
- [ ] Code examples are relevant and explained
- [ ] Technical accuracy is verified with sources
- [ ] References to specific PRs, issues, or documentation
- [ ] Includes version information and historical context
- [ ] Explains "why" not just "what"
- [ ] Provides genuine value and insights to readers

**Writing Style:**
- [ ] Written entirely in natural, conversational Japanese
- [ ] Includes personal voice and perspective ("筆者は", "個人的には")
- [ ] Varies sentence structure and paragraph length
- [ ] Uses natural connectors, not formulaic transitions
- [ ] Avoids textbook-like explanations
- [ ] Includes at least one "余談" or detailed supplement

**Authenticity:**
- [ ] Feels like a human expert wrote it
- [ ] Has subjective opinions and judgments where appropriate
- [ ] Includes discoveries, insights, or "aha moments"
- [ ] Tone is conversational yet informative
- [ ] No obvious AI patterns (repetitive structures, generic phrases)

**Details:**
- [ ] Footnote references match URLs correctly
- [ ] Links and references are complete
- [ ] No overused adjectives without substance
- [ ] Conclusion adds insight, not just recap

---

## Human vs AI Writing Patterns

Understanding the difference between human-written and AI-generated text is crucial. Here are concrete examples:

### Introduction Styles

**❌ AI Pattern (Formulaic):**
```
皆さんこんにちは。TypeScriptを使っているとよく遭遇する「なぜこの型になるの?」という疑問。
今回は、TypeScriptの型推論システムの中核をなすwideningとnarrowingについて、実践的な視点から解説します。
```
Problems: Generic greeting, predictable structure, announces exactly what will be covered

**✅ Human Pattern (Natural):**
```
皆さんこんにちは。今回はTypeScriptの更新先取りシリーズです。TypeScriptの次のバージョンでは、
以下のPRの更新が入ると思われます。もちろんPRの著者はAndersさんです。
```
Strengths: Direct, includes personality ("もちろん〜"), jumps into specifics immediately

### Technical Explanation Styles

**❌ AI Pattern (Textbook):**
```
Wideningとは何か

Wideningは、TypeScriptがリテラル型(具体的な値の型)を、より広い型に拡大することです。
典型的な例を見てみましょう...
```
Problems: Dictionary definition, predictable section title, mechanical flow

**✅ Human Pattern (Conversational):**
```
この辺りの背景としては、任意の値を表すunknown型が{}型に比べて新参であるという事情があります。
個人的にはunknownが{}に絞り込まれても嬉しい場面がそれほどありません。
```
Strengths: Explains background/context, includes personal opinion, natural flow

### Using Personal Voice

**❌ AI Pattern (Impersonal):**
```
実務でよく遭遇する問題を見てみましょう。この挙動は一見不便に見えますが、
TypeScriptが想定しているためです。
```
Problems: Generic "実務", passive voice, no personal connection

**✅ Human Pattern (Personal):**
```
自分はよく次のような関数を作ります。[具体的なコード例]
試しにclock(); clock();のように2つ動かすと、次のように表示されます。
```
Strengths: "自分は", specific action ("試しに"), reader included in discovery

### Transitions and Flow

**❌ AI Pattern (Mechanical):**
```
しかし、この型推論は複雑です。特に重要なのが、wideningとnarrowingです。
一方、constで宣言した変数は...
さらに、実際の開発では...
```
Problems: Formulaic connectors ("しかし", "一方", "さらに"), predictable rhythm

**✅ Human Pattern (Natural):**
```
そう、それはuseContextです。
試しに〜してみると、次のように表示されます。
さて、では次に〜を見ていきましょう。
```
Strengths: Varied connectors, natural rhythm, conversational markers

### Conclusions

**❌ AI Pattern (Generic Recap):**
```
重要なポイントをまとめると:
- Wideningは型が拡大される現象
- Narrowingは型を絞り込むプロセス
- as constを使うことで制御できる

これらの概念を理解すれば、TypeScriptをより深く活用できるようになるはずです。
```
Problems: Bullet list recap, generic encouragement, no new insight

**✅ Human Pattern (Insightful):**
```
TypeScript 4.6でもまだできないこと

個人的にはこのような書き方ができてほしいと思っていますが、現状ではできません。
今後の改善に期待したいところです。
```
Strengths: Addresses limitations, personal opinion on future, honest about what's missing
