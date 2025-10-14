# Technical Article Style Guide

This guide defines the standards for generating technical articles that are indistinguishable from human-written content.

## Version History

- **v0.1** (Initial): Basic structure and formatting requirements based on analysis of human benchmark articles

---

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
2. **Personal voice**: Use first-person perspective ("筆者", "私") when expressing opinions
3. **Natural flow**: Write as if explaining to a colleague, not lecturing
4. **Appropriate formality**: Balance politeness with technical precision

## Article Structure

### Required Sections

1. **Introduction**
   - Set context and explain why the topic matters
   - May include links to related articles or background
   - Can use Q&A format for clarity

2. **Main Content**
   - Divide into logical sections with clear headings
   - Use ## for main sections, ### for subsections
   - Include code examples with explanations
   - Reference official documentation where relevant

3. **まとめ (Summary)**
   - Conclude with key takeaways
   - May include personal reflections or opinions

### Optional Sections

- **余談 (Side Notes)**: Additional context using `:::details 余談` blocks
- **補足 (Supplements)**: Clarifications or edge cases

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

### Technical Accuracy

- Explain concepts clearly and correctly
- Reference official sources (GitHub PRs, documentation, RFCs)
- Acknowledge limitations or edge cases
- Differentiate facts from opinions/interpretations

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

- Share insights and interpretations, not just facts
- Use rhetorical questions naturally
- Express uncertainty appropriately ("〜と思われます", "おそらく")
- Include personal opinions when relevant, clearly marked

### Avoid AI Patterns

- Don't be overly formal or robotic
- Avoid repetitive sentence structures
- Don't over-explain obvious points
- Use varied paragraph lengths
- Include occasional asides and tangential thoughts

### Show Expertise

- Reference specific GitHub issues, PRs, or commits
- Discuss implementation details and tradeoffs
- Connect concepts across different domains
- Share practical experience and use cases

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

## What to Avoid

- ❌ Starting every paragraph the same way
- ❌ Overly long introductions before getting to the point
- ❌ Excessive use of "今回は" or "それでは"
- ❌ Explaining things in a condescending manner
- ❌ Being too cautious or hedging everything
- ❌ Generic conclusions that don't add value
- ❌ Forcing structure when natural flow is better

## Quality Checklist

Before finalizing an article, verify:

- [ ] Frontmatter is complete and correct
- [ ] Written entirely in natural Japanese
- [ ] Has clear introduction and まとめ
- [ ] Code examples are relevant and explained
- [ ] Technical accuracy is verified
- [ ] Tone is conversational yet informative
- [ ] Links and references are provided
- [ ] Feels like a human expert wrote it
- [ ] Provides genuine value to readers

---

## Notes for Future Iterations

This style guide will evolve based on review feedback. Each iteration should make the guidelines more specific and actionable while maintaining clarity and usability.
