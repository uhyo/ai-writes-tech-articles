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

2. **Personal voice**: Use first-person perspective sparingly and naturally
   - **CRITICAL: Limit "筆者" usage to 3-5 times per article maximum**
   - Many opinions can be stated directly without attribution
   - Vary your attribution style:
     - "個人的には〜" (personal opinion without "筆者")
     - "自分の経験では〜" (casual self-reference)
     - Omit attribution entirely when the opinion is clear
     - Use "筆者" only for emphasis or formal statements
   - ❌ Bad: "筆者は〜と思う。筆者の経験では〜。筆者が実際に書いた〜。筆者の結論は〜。" (overuse)
   - ✅ Good: "個人的にはこの機能が最高だと思う。実際に書いた実装は〜。これは確信を持って言える。"
   - Include subjective judgments: "〜が嬉しい場面がそれほどありません", "〜の方が好みです"
   - Don't be afraid to express preferences, frustrations, or discoveries
   - Balance objective technical explanation with personal perspective

3. **Natural flow**: Write as if explaining to a colleague over coffee, not lecturing in a classroom
   - **CRITICAL: Avoid pedagogical scaffolding**:
     - ❌ "では、〇〇の場合はどうでしょうか" (textbook-style transitions)
     - ❌ "本題に戻ると" (unnecessary announcement of topic return)
     - ❌ "ここで〇〇について見ていきましょう" (classroom guide language)
     - ❌ "次に〇〇を説明します" (explicit structure signaling)
     - ✅ Just transition naturally: "で、〇〇だと話が変わる。"
     - ✅ Resume topic without announcement: "なぜ〇〇なのか。"
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
   - **CRITICAL: Avoid systematic enumeration patterns**:
     - ❌ "パターン1: 〜", "パターン2: 〜", "パターン3: 〜"
     - ❌ "方法1", "方法2", "方法3"
     - ❌ "ケース1", "ケース2", "ケース3"
     - These numbered lists make the article feel like a tutorial, not a blog post
     - Instead: Use descriptive headings that reflect the actual content
     - Example: Instead of "パターン1: useMemoで値を安定化" → "useMemoで値を安定化させる"
     - Even better: Mix different organizational styles within the same article

   - **CRITICAL: Limit section count**:
     - ❌ 10+ H2 sections makes the article feel over-structured
     - ✅ Target 6-7 H2 sections maximum for a typical article (200-300 lines)
     - Combine related topics into single sections rather than splitting everything
     - Not every subtopic needs its own H2 section
     - Use H3 subsections to organize within larger sections when needed

   - **Vary section lengths dramatically**:
     - Some sections should be 1-2 paragraphs (brief point)
     - Others should be extensive deep-dives (3-5+ paragraphs)
     - Never make all sections roughly the same length
     - Follow your interest: Spend more words on what's genuinely interesting

   - **Break away from rigid structure**:
     - Don't always follow "基本 → 応用 → まとめ"
     - Consider these organic flow patterns:
       - Jump between related ideas without formal transitions
       - Start with an advanced case, then explain basics
       - Interweave examples and explanations
       - Let tangents interrupt the main flow naturally

   - **Use natural, imperfect transitions**:
     - Not every section needs a smooth transition
     - Use abrupt topic changes: "ところで", "そういえば", "余談だけど"
     - Sometimes just start a new section without setup
     - Let the structure feel slightly messy and organic

   - Use ## for main sections, ### for subsections
   - Include code examples with explanations
   - Reference official documentation, PRs, and issues extensively

3. **まとめ (Summary)**
   - **CRITICAL: Avoid synthesizing everything neatly**
   - **Go beyond simple recap**: Don't just list what was covered
   - **Real conclusions are messy and incomplete**:
     - ❌ "重要なポイントをまとめると: [bullet list]" (too organized)
     - ❌ "今回は〜を見てきました。〜が重要です。" (textbook recap)
     - ❌ Neat takeaways that tie everything together in a bow
     - ❌ "筆者の結論: [perfectly balanced summary]" (too synthesized)
   - Consider these ending styles:
     - End abruptly with a final observation or opinion
     - Raise remaining questions or uncertainties
     - Take a controversial stance without balancing it
     - Circle back to an unexpected point from earlier
     - Admit limitations, unknowns, or unresolved issues
     - Mix resolved insights with loose ends
     - Let some threads remain unfinished
   - Include personal reflection, but make it rough and opinionated
   - ✅ Good: "結局どう使えばいいのか。個人的には小規模なら最高。でも大規模だと厳しい。あと、この機能を使うなら型システムの理解が必須。"
   - Avoid: Generic phrases like "いかがでしたでしょうか"

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

- **CRITICAL: Reference sources with extreme specificity AND natural integration**:
  - ✅ ALWAYS link to specific GitHub PRs with numbers: "https://github.com/microsoft/TypeScript/pull/49119"
  - ✅ Include PR/issue authors: "もちろんPRの著者はAndersさんです"
  - ✅ Reference specific GitHub discussions: "GitHub issueの #19820 では〜"
  - ✅ Mention commit hashes when relevant: "このコミット (abc1234) で変更されました"
  - ✅ Cite specific line numbers: "型定義ファイルの234行目を見ると〜"
  - ✅ Reference version numbers precisely: "TypeScript 4.8以降", "React 18.2で導入"
  - ✅ Link to RFCs, design documents, and proposal discussions
  - ❌ Don't just say "GitHub issueで提案されていました" without a link and number
  - ❌ Don't just say "公式ドキュメント" without specific URLs
  - ❌ Don't mention PRs generically without showing you've actually read them

  - **CRITICAL: Make GitHub references feel natural, not cited**:
    - ❌ Bad (feels like citing sources): "このPRのタイトルは「〇〇」で、コメント欄を読むと分かるんだけど..."
    - ❌ Bad (too formal): "この機能は GitHub issue #12754 で提案され、PR #40336 で実装されました"
    - ✅ Good (casual mention): "この機能、実はPR #40336で入ったんだけど、issue #12754を見ると数百のupvoteが集まってて..."
    - ✅ Good (parenthetical): "実は当初の実装ではintrinsicを使っていませんでした。代わりに... ([#40336](...))"
    - ✅ Good (embedded in story): "去年GitHubで議論されてたissue #19820の話なんだけど、これ面白くて..."
    - Drop PR/issue numbers like you're naturally referencing things, not building a bibliography
    - Don't explain what the PR title is or describe the comment thread - just reference it
  - **This is how you demonstrate real expertise vs. superficial knowledge**

- **Show evidence of investigation**:
  - Mention specific experiments you ran: "筆者はいくつか実験してみたのですが〜"
  - Describe debugging experiences: "実際にReactのソースコードを追ってみたところ〜"
  - Reference discussions you participated in or observed
  - Cite specific error messages or edge cases you encountered

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

**Share personal experiences with messy, specific details AND rich context:**
- ❌ Generic: "実務でよく遭遇する問題です"
- ❌ Lacks context: "去年、あるプロジェクトで3日間消費した経験がある"
- ✅ Rich context: "去年、社内の古いExpress APIをTypeScript化するプロジェクトで、「既存の100個のエンドポイント全部に型つけろ」って無茶振りされた。最初は『stringでいいんじゃない？』って思ってたけど、実際にやり始めたらパスパラメータの抽出で詰まって、気づいたら3日溶けてた"

- **Real stories need contextual richness**:
  - What kind of project? (company context, team size, tech stack)
  - Why were you doing this? (requirements, constraints, business needs)
  - Who else was involved? (team dynamics, stakeholders)
  - What were you trying to achieve? (specific goals, not just "solve problem")
  - What alternatives did you consider?
  - ❌ "あるプロジェクトで" → ✅ "社内の検索API（100個くらいエンドポイントがある）のTypeScript SDKを作る案件で"
  - ❌ "APIの型を作ってて" → ✅ "「パスパラメータを型安全に扱いたい」って要件が降ってきた"

- **Real stories have odd details**: Specific project names, actual time spent, failed attempts, unexpected discoveries
- **Real stories have imperfect resolutions**: "結局完全には解決しなかった", "今でもベストな方法かわからない"
- **Real stories include dead ends**: "最初は〜だと思ったけど、違った", "〜を試したけどダメだった"

**Use natural conversational patterns:**
- Rhetorical questions that actually add value (not formulaic ones)
- Good: "では、なぜこのような仕様になっているのでしょうか?"
- Avoid: "〜ではないでしょうか?" as a generic connector
- Use casual interjections: "さて", "ところで", "そう", "なお"
- Thinking out loud: "試しに〜してみると、次のように表示されます"

**Express strong opinions, not just balanced views:**
- ❌ Too neutral: "この方法にはメリットとデメリットがあります"
- ✅ Opinionated: "個人的には、この方法は避けるべきだと断言します。なぜなら〜"
- ✅ Controversial: "よく推奨されているけど、筆者は〜の方が好みです"
- ✅ Assertive: "このパターンは絶対に使うな。〜という理由で"
- Balance comes from having opinions, not from being neutral about everything
- Don't be afraid to contradict common wisdom (with good reasons)

**Express appropriate uncertainty:**
- "〜と思われます" / "おそらく〜" / "〜の可能性があります"
- Be honest about limitations of your knowledge
- Distinguish between confirmed facts and speculation
- It's OK to say: "正直、この部分はよくわかっていません"

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
- ❌ **CRITICAL: Pedagogical scaffolding**:
  - "では、〇〇の場合はどうでしょうか" (classroom-style transitions)
  - "本題に戻ると" (unnecessary announcements)
  - "ここで〇〇について見ていきましょう" (explicit guiding)
  - "この型、実は結構問題がある" (unnecessary foreshadowing)
- ❌ Meta-commentary: "今回は〜について解説します", "では、次のパターンを見てみましょう"
- ❌ Too-smooth transitions: Every section connecting perfectly to the next
- ❌ **Excessive "筆者" usage**: More than 5-7 times per article feels performative

**Structural patterns to avoid:**
- ❌ **CRITICAL**: Numbered enumeration patterns (パターン1/2/3, 方法1/2/3)
- ❌ **CRITICAL**: Too many sections (10+ H2 sections is excessive)
- ❌ Predictable section order: 基本 → 応用 → まとめ every time
- ❌ Exhaustive enumeration: listing every possible case mechanically
- ❌ Over-explanation of obvious points
- ❌ Same paragraph and sentence lengths throughout
- ❌ Perfect balance: making every section roughly equal in depth
- ❌ Neat conclusions that tie everything together in a bow
- ❌ Too-organized まとめ sections with bullet points and comprehensive synthesis

**Tone patterns to avoid:**
- ❌ Being overly cautious or hedging everything
- ❌ Being consistently neutral and balanced about everything
- ❌ Pedagogical tone: explaining like a patient teacher
- ❌ Condescending explanations: "ご存知の通り〜"
- ❌ Robotic transitions between sections
- ❌ Generic conclusions: "いかがでしたでしょうか"
- ❌ Too polished: no rough edges, no tangents, no messiness

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
- ❌ **CRITICAL**: Systematic patterns like "パターン1/2/3/4/5" or "方法1/2/3"

### Formulaic Language
- ❌ Starting every paragraph the same way
- ❌ Overusing transitions: "また", "さらに", "一方", "しかし" in a mechanical pattern
- ❌ Excessive use of "今回は" or "それでは〜を見ていきましょう"
- ❌ Generic rhetorical questions: "〜ではないでしょうか?" as filler
- ❌ Meta-commentary about structure: "今回は〜について解説します"

### Artificial Tone
- ❌ Overly formal or stilted Japanese
- ❌ Explaining things in a condescending manner: "ご存知の通り"
- ❌ Being too cautious or hedging everything unnecessarily
- ❌ Lack of personal voice: sounding like a neutral explainer bot
- ❌ **Too balanced**: Always presenting both sides equally without taking a stance
- ❌ **Too pedagogical**: Patient teacher tone that explains everything systematically

### Shallow Content
- ❌ Surface-level explanations without "why"
- ❌ **CRITICAL**: Missing specific references to GitHub PRs, issues, commits
- ❌ Mentioning PRs/issues without links or numbers
- ❌ Generic examples without real-world context
- ❌ Conclusions that just recap without adding insight
- ❌ No evidence of investigation or experimentation

### Structural Issues
- ❌ Overly long introductions before getting to the point
- ❌ Generic conclusions: "いかがでしたでしょうか"
- ❌ Forcing rigid structure when natural flow is better
- ❌ Predictable article arc that readers can anticipate from the first paragraph
- ❌ **All sections the same length**: No variation in depth
- ❌ **Too-smooth transitions**: Every section connects perfectly

### Personal Anecdote Issues
- ❌ **Generic stories**: "実務でよく遭遇する", "先日レビューしていて", "あるプロジェクトで"
- ❌ **Lack of contextual richness**: Mentions time spent but not why, what project, what requirements, who was involved
- ❌ **Perfect resolutions**: Every story ends neatly solved
- ❌ **Lack of specifics**: No project details, tech stack, team size, business constraints
- ❌ **Too clean**: No messy details, dead ends, or imperfect outcomes
- ❌ Example of insufficient context: "去年、あるプロジェクトでAPIのルーティング型を作ってて、3日間消費した"
- ✅ Example with rich context: "去年、社内の検索API（100個くらいエンドポイント）のTypeScript SDKを作る案件で、『パスパラメータを型安全に』って要件が降ってきて..."

### Detail Issues
- ❌ Incorrect footnote references (e.g., `[^pr119]` when URL says `/pull/49119`)
- ❌ Overuse of adjectives like "非常に", "重要", "明確", "適切" without substance
- ❌ Same sentence structures and paragraph lengths throughout

## Breaking the "Tutorial Template" Trap

One of the most common AI patterns is writing like a systematic tutorial. Here's how to break free:

### The Problem: Numbered Enumeration
**❌ AI Pattern:**
```
## パターン1: useMemoで値を安定化
## パターン2: 必要な値だけ抽出
## パターン3: useCallbackで関数をメモ化
## パターン4: setState関数は依存配列不要
## パターン5: useEffectEventという未来の解決策
```

This feels like a tutorial checklist, not a blog post. Every section has equal weight and follows the same template.

### The Solution: Organic, Varied Structure
**✅ Human Pattern:**
- Mix different section types: some deep dives, some quick notes
- Use descriptive headings: "useMemoで値を安定化させる" (no numbers)
- Vary depth: One section might be 5 paragraphs, the next just 1
- Include tangents: "ところで、〜" or "余談だけど〜"
- Let some sections interrupt the flow naturally

**Example of varied depth:**
- Long section: Deep dive into stale closures with multiple examples
- Short section: "ちなみに、setState関数は安定してるので依存配列に入れなくてもOKです。"
- Medium section: Discussion of useCallback with personal opinions

### The Balance Problem: Too Neutral
**❌ AI Pattern:**
```
useMemoを使う方法にはメリットとデメリットがあります。
メリット: 参照が安定する
デメリット: パフォーマンスコストがある
どちらを選ぶかは状況次第です。
```

This is balanced but boring. No personality, no stance.

**✅ Human Pattern:**
```
個人的には、「なんとなく最適化しておこう」でuseMemoを多用するのは好きじゃないんですよね。
かえってコードが読みづらくなるし、パフォーマンスも悪化することがある。
とはいえ、明らかに必要な場面では使うべき。要は考えなしに使うなってこと。
```

Has a clear stance, uses personal voice, not afraid to be opinionated.

### The Depth Problem: Superficial References
**❌ AI Pattern:**
```
GitHub issueでも議論されています。
公式ドキュメントを参照してください。
```

Generic, no proof you actually looked at anything.

**✅ Human Pattern:**
```
GitHub issueの #19820 では、「"what"の依存と"when"の依存を分けたい」という提案がされていました。
https://github.com/facebook/react/issues/19820

筆者はいくつか実験してみたのですが、どうも書いてある通りの挙動にならないので
Reactのリポジトリにissueを建ててみました。結果、〜ということがわかりました。
```

Specific issue number, link, evidence of investigation, personal engagement.

## Quality Checklist

Before finalizing an article, verify:

**Format and Structure:**
- [ ] Frontmatter is complete and correct
- [ ] Has clear introduction and まとめ
- [ ] Structure is natural, not rigidly formulaic
- [ ] Section flow is logical but not predictable
- [ ] **NO numbered enumeration patterns** (パターン1/2/3)
- [ ] **Target 6-7 H2 sections maximum** (not 10+)
- [ ] Dramatic variation in section lengths

**Content Quality:**
- [ ] Code examples are relevant and explained
- [ ] Technical accuracy is verified with sources
- [ ] **Specific references**: PR/issue numbers with links
- [ ] **GitHub references feel natural**, not cited (casual mentions, parenthetical links)
- [ ] Includes version information and historical context
- [ ] Explains "why" not just "what"
- [ ] Shows evidence of investigation or experimentation
- [ ] Provides genuine value and insights to readers

**Writing Style:**
- [ ] Written entirely in natural, conversational Japanese
- [ ] **"筆者" used sparingly (3-5 times maximum)**, varies with "個人的には", omission
- [ ] **NO pedagogical scaffolding** ("では〇〇の場合は", "本題に戻ると")
- [ ] Varies sentence structure and paragraph length
- [ ] Uses natural connectors, not formulaic transitions
- [ ] Avoids textbook-like explanations
- [ ] Includes at least one "余談" or detailed supplement
- [ ] **Takes strong stances**, not just balanced views
- [ ] **Messy personal anecdotes** with rich contextual details (project specifics, requirements, team dynamics)

**Authenticity:**
- [ ] Feels like a human expert wrote it
- [ ] Has subjective opinions and judgments where appropriate
- [ ] Includes discoveries, insights, or "aha moments"
- [ ] Tone is conversational yet informative
- [ ] No obvious AI patterns (repetitive structures, generic phrases, pedagogical scaffolding)
- [ ] Not too polished: has rough edges, tangents, imperfections
- [ ] Not pedagogical: doesn't sound like a patient teacher
- [ ] **Conclusion is messy**, not a neat synthesis with bullet points

**Details:**
- [ ] Footnote references match URLs correctly
- [ ] Links and references are complete and specific
- [ ] No overused adjectives without substance
- [ ] Conclusion adds insight with rough edges, not just neat recap
- [ ] Personal stories have messy, imperfect details with rich context (not just time spent)

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
