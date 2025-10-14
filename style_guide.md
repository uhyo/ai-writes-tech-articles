# Technical Article Style Guide

This guide defines standards for generating Japanese technical articles indistinguishable from human-written content.

---

## 🔴 CRITICAL REQUIREMENTS (Publication Blockers)

These are non-negotiable. Violating any of these makes the article unpublishable.

### 1. Japanese Language & Tone

**CRITICAL: です・ます (polite form) throughout**
- Use "です/ます" form consistently, NOT "だ/である" form
- Mix in casual expressions naturally: "〜ですね", "〜でしょう", "〜なんです"
- Conversational yet informative tone

❌ **Wrong**: "結論から言うと、これProxyとWeakMapだけで実装できる。" (だ form)
✅ **Correct**: "結論から言うと、これはProxyとWeakMapだけで実装できます。" (です form)

### 2. Frontmatter Format

Every article must begin with valid YAML:

```yaml
---
title: "記事のタイトル"
emoji: "🎯"
type: "tech"
topics: ["typescript", "javascript", "react"]
published: true
---
```

### 3. Technical Accuracy

- Concepts explained correctly with sources
- Code examples work and are properly explained
- Reference specific GitHub PRs/issues with numbers and links
- Include version information (e.g., "TypeScript 4.8以降")

### 4. Natural Japanese

- Written entirely in Japanese (not translation-like)
- Technical terms: Use English when commonly used in Japanese tech community
- Natural phrasing that flows smoothly

---

## 📋 QUALITY CHECKLIST

Use this to verify articles before submission. Each item links to detailed guidance below.

### Format & Structure
- [ ] **です・ます polite form** used throughout (→ §1)
- [ ] Valid frontmatter with all fields (→ §1.2)
- [ ] 6-7 H2 sections maximum (→ §5.4)
- [ ] Dramatic variation in section lengths (→ §5.4)
- [ ] Clear introduction and まとめ

### Technical Content
- [ ] Technical accuracy verified (→ §1.3)
- [ ] Code examples with proper syntax highlighting
- [ ] Specific GitHub PR/issue references with links (→ §5.2)
- [ ] Version information included
- [ ] Explains "why" not just "what"

### Writing Style
- [ ] Conversational, not textbook-like (→ §5)
- [ ] Personal voice present but "筆者" used sparingly (3-5x max) (→ §5.1)
- [ ] NO pedagogical scaffolding (→ §5.1)
- [ ] NO numbered enumeration patterns (パターン1/2/3) (→ §5.4)
- [ ] Strong opinions expressed, not just balanced views (→ §6)

### Authenticity Markers
- [ ] Personal anecdotes with rich contextual details (→ §5.3)
- [ ] Anecdotes include tangential digressions (→ §5.3)
- [ ] GitHub references feel casual, not cited (→ §5.2)
- [ ] Messy conclusion without numbered synthesis (→ §5.5)
- [ ] 1-2 micro-imperfections present (→ §7)
- [ ] Not too polished - has rough edges

---

## 🟡 IMPORTANT: Human-Like Writing Patterns

These distinguish human writing from AI-generated content.

### 5.1 Tone & Voice (Conversational Flow)

**Personal Voice Guidelines:**
- Use "筆者" sparingly: **3-5 times maximum per article**
- Vary attribution style:
  - "個人的には〜" (personal opinion without "筆者")
  - "自分の経験では〜" (casual self-reference)
  - State opinions directly without attribution
- Express subjective judgments freely

**CRITICAL: No Pedagogical Scaffolding**

Write as peer conversation, not teacher-to-student lecture.

❌ **Avoid these teaching transitions:**
- "では、〇〇の場合はどうでしょうか" (textbook-style)
- "本題に戻ると" (unnecessary announcement)
- "ここで〇〇について見ていきましょう" (classroom guide language)
- "理屈はわかった。じゃあ実装してみよう。" (guides reader like student)
- "使い方はこう：" (instructional setup before code)
- "次に〇〇を説明します" (explicit structure signaling)

✅ **Use natural transitions:**
- "で、〇〇だと話が変わる。"
- "なぜ〇〇なのか。" (just resume without announcement)
- Just start code without preamble
- "試しに〜してみると" (discovery, not instruction)

**Conversational connectors:** "そう、それは〜です", "さて、〜", "ところで"

### 5.2 Technical Depth (GitHub References)

**Reference sources with specificity AND natural integration:**

✅ **Required:**
- Link to specific PRs with numbers: `https://github.com/microsoft/TypeScript/pull/49119`
- Include authors: "もちろんPRの著者はAndersさんです"
- Reference issues: "GitHub issueの #19820 では〜"
- Mention commit hashes when relevant
- Cite specific line numbers when relevant

**CRITICAL: Make references feel natural, not cited**

Three acceptable patterns:
1. **Parenthetical de-emphasis**: "(#2851とか)" buried in sentence
2. **Drop numbers entirely**: Describe content without specific numbers
3. **Embedded naturally**: Numbers mid-sentence, not as main subject

❌ **NEVER make PR/issue numbers the grammatical subject:**
- "PR #2851でshallowとnormalのProxyを別々にtrackする修正が入った"

✅ **Make the change the subject, reference parenthetical:**
- "shallow reactiveの扱いが変わった(#2851)"
- "reactivityパッケージの実装見てると、shallow proxyの扱いとか配列の最適化とか色々工夫されてて面白い" (numbers dropped)

**Show evidence of investigation:**
- Mention specific experiments: "筆者はいくつか実験してみたのですが〜"
- Describe debugging: "実際にReactのソースコードを追ってみたところ〜"
- Cite specific error messages or edge cases

### 5.3 Authentic Anecdotes

**Personal experiences need rich contextual details:**

❌ **Generic** (lacks context):
- "実務でよく遭遇する問題です"
- "去年、あるプロジェクトで3日間消費した"

✅ **Rich context** (specific and messy):
- "去年、社内の古いExpress API（100個くらいエンドポイント）をTypeScript化するプロジェクトで、『既存の全部に型つけろ』って無茶振りされた。最初は『stringでいいんじゃない？』って思ってたけど、実際にやり始めたらパスパラメータの抽出で詰まって、気づいたら3日溶けてた"

**Required contextual elements:**
- What kind of project? (company context, team size, tech stack)
- Why were you doing this? (requirements, constraints)
- Who else was involved? (team dynamics)
- What were you trying to achieve? (specific goals)
- What alternatives did you consider?

**CRITICAL: Real stories digress and meander**

Include 1-2 parenthetical asides per major anecdote that don't directly advance the main point:

✅ **Example with digression:**
- "去年、社内の管理画面のリファクタプロジェクトで「状態管理をどうするか」って話になって（ちなみにこのプロジェクト、最初はReduxで書き直す予定だったのが、途中でみんな飽きて別の方向に...）、結局Vueのreactivityを使った"

**Types of digressions:**
- Original scope changes: "元々は〜が目的だったのが、いつの間にか〜にシフトしてた"
- Team dynamics: "チームの誰々が〜って言い出して"
- Unrelated observations
- Failed approaches: "最初〜でやろうとしたんだけど、全然ダメで"

**Real stories have:**
- Odd specific details (project names, time spent, failed attempts)
- Imperfect resolutions: "結局完全には解決しなかった"
- Dead ends: "最初は〜だと思ったけど、違った"

### 5.4 Structure & Organization

**CRITICAL: Limit section count**
- **Target 6-7 H2 sections maximum** for typical articles (not 10+)
- Combine related topics rather than splitting everything
- Use H3 subsections within larger sections when needed

**CRITICAL: No numbered enumeration**
- ❌ "パターン1: 〜", "パターン2: 〜", "パターン3: 〜"
- ❌ "方法1", "方法2", "方法3"
- ✅ Use descriptive headings: "useMemoで値を安定化させる"

**Vary section lengths dramatically:**
- Some sections: 1-2 paragraphs (brief point)
- Others: 3-5+ paragraphs (deep dive)
- Never make all sections roughly equal length
- Follow your interest, not templates

**Natural, imperfect transitions:**
- Not every section needs smooth transitions
- Use abrupt topic changes: "ところで", "そういえば", "余談だけど"
- Sometimes just start new section without setup

### 5.5 Conclusions (まとめ)

**CRITICAL: Avoid synthesizing everything neatly**

Real conclusions are messy and incomplete.

❌ **Avoid:**
- "重要なポイントをまとめると: [bullet list]" (too organized)
- "結局、核心は3つだけ：1. 〜 2. 〜 3. 〜" (numbered synthesis)
- "今回は〜を見てきました。〜が重要です。" (textbook recap)
- Neat takeaways that tie everything together

✅ **Better ending styles:**
- End abruptly with final observation
- Raise remaining questions or uncertainties
- Take controversial stance without balancing it
- Admit limitations, unknowns, unresolved issues
- Mix resolved insights with loose ends
- Trail off into tangential thoughts

**Example:**
"結局どう使えばいいのか。個人的には小規模なら最高。でも大規模だと厳しい。あと、この機能を使うなら型システムの理解が必須。"

---

## 🟢 POLISH: Final Refinements

These are final touches that push quality from good to excellent.

### 7. Micro-Imperfections for Authenticity

**CRITICAL: Perfect polish is an AI tell**

Humans writing in flow have minor inconsistencies. Include 1-2 per article:

✅ **Acceptable imperfections** (NOT typos/errors):
- Casual contractions: "んだけど" (not "のだけど"), "んで" (not "ので")
- Self-corrections: "〜というか、正確には〜"
- Informal abbreviations: "ちな" for "ちなみに" (sparingly)
- Slight repetition: Repeating word/phrase in adjacent sentences naturally
- Trailing thoughts: Sentences ending with "..."

❌ **Don't include:** Typos, grammatical errors, broken code

**Example:** "で、実際に試してみたんだけど、思ったより簡単だった。というか、簡単すぎて拍子抜けした。"

### 8. Additional Content Types

**余談 (Side Notes):** Use `:::details 余談` blocks for:
- Historical context and version history
- Tangential observations
- Personal anecdotes
- Deep dives into implementation details

**Markdown Features:**
```markdown
:::message
Important notes or disclaimers
:::

:::details 補足説明
Collapsible additional information
:::
```

---

## ⚠️ ANTI-PATTERNS: Quick Reference

**Language Patterns to Avoid:**
- Starting paragraphs with "また" or "さらに" repeatedly
- Overusing "非常に", "重要", "明確", "適切"
- Formulaic transitions: "それでは〜を見ていきましょう"
- Meta-commentary: "今回は〜について解説します"
- Pedagogical scaffolding (see §5.1)
- Excessive "筆者" usage (>5-7 times)

**Structural Patterns to Avoid:**
- Numbered enumeration (パターン1/2/3)
- Too many sections (10+ H2 sections)
- All sections same length
- Predictable order: 基本 → 応用 → まとめ every time
- Neat conclusions with numbered synthesis

**Tone Patterns to Avoid:**
- Too balanced (always presenting both sides equally)
- Too pedagogical (patient teacher explaining systematically)
- Overly cautious (hedging everything)
- Too polished (no rough edges, tangents, messiness)

**Content Issues:**
- Missing specific GitHub PR/issue references
- GitHub references as formal citations (see §5.2)
- Generic anecdotes without context (see §5.3)
- Shallow explanations without "why"

---

## Examples from Human Articles

**Good Opening:**
```markdown
皆さんこんにちは。今回はTypeScriptの更新先取りシリーズです。
TypeScriptの次のバージョンでは、以下のPRの更新が入ると思われます。
もちろんPRの著者はAndersさんです。
```

**Good Technical Explanation:**
```markdown
`{}`型は、「`null`と`undefined`以外の任意の値」という意味を持つ型です。
この型は形としては空のオブジェクト型ですが、JavaScriptでは`null`と`undefined`以外の
プリミティブ（文字列や数値など）に対してもプロパティアクセスをしてもエラーに
ならないという仕様を考慮して、`{}`型には文字列や数値などのプリミティブも含まれています。
```

---

**Last updated:** Iteration 5 Consolidation
**Version:** 2.0 (Consolidated from 726 lines → 326 lines)
**Target:** <350 lines | **Current:** 326 lines
