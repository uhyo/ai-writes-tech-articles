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

### 5.1 Write from THINKING, Not from FORMULA

**CRITICAL: The Core Problem**

DON'T follow writing guidelines mechanically. Guidelines describe OUTCOMES of human thinking, not INPUTS to article generation.

❌ **Formula-following (mechanical):**
- "I need a trial-and-error section, so I'll create V1→V2→V3"
- "I need reader dialogue, so I'll insert 'お察しの通り'"
- "I need an anecdote, so I'll write about a project"

✅ **Thinking-driven (organic):**
- Think about the concept deeply → Realize it evolved through problems → Show that evolution naturally
- Notice readers might misunderstand → Address their confusion directly in that moment
- Remember a real discovery → Let it inform how you explain the problem

**Techniques are SYMPTOMS of thinking, not TOOLS to apply.**

**CRITICAL: Perfect is an AI Tell**

Human articles in flow have IMPERFECTIONS:
- Code shown first, then "あ、これバグある" → fixed version
- Jump to topic abruptly: "そういえば〜" (no smooth transition)
- Leave questions open: "これは別の機会に" "まだ試してない"
- Realize mid-article: "ああ、〜も説明すべきでした"
- Tangents that don't tie back: "余談だけど〜" (never referenced again)

### 5.2 Tone & Voice (Conversational Flow)

**Personal Voice Guidelines:**
- Use "筆者" sparingly: **3-5 times maximum per article**
- Vary attribution style naturally
- Express subjective judgments freely

**CRITICAL: No Pedagogical Scaffolding**

Write as peer conversation, not teacher-to-student lecture. Avoid: "では〜", "見ていきましょう". Use: "で、〜", abrupt topic starts.

**CRITICAL: Vary Explanation Depth Dramatically**

Some topics: 1 sentence. Others: 10+ paragraphs. NOT uniform 3-4 paragraphs everywhere.

**CRITICAL: Vary Explanatory Phrases**

Track phrase patterns. Don't repeat structures:
- "これで〜解決" → vary with "ここまでできた", "やっと〜", "ようやく〜", or just move on without explicit markers
- "〜です。〜ます。" → occasionally break with sentence fragments, casual endings: "で、結局〜。"

### 5.3 Introduce Conceptual Frameworks

**CRITICAL: Think at Meta-Technical Level**

Don't just explain HOW things work. Introduce higher-level concepts that REFRAME the discussion.

✅ **Examples from human articles:**
- "Promiseが一級市民ではなかった" (react-use-rfc.md) - Reframes entire Suspense discussion
- "記憶領域を必要としないフック" - Creates new mental category
- "レンダリング単位の記憶領域" vs "コンポーネント単位の記憶領域" - Distinguishes implementation approaches

**How to create frameworks:**
1. Notice a recurring pattern or constraint
2. Give it a name that's not in official docs
3. Use it to explain why things work the way they do
4. Reference it later to create conceptual continuity

These frameworks show you're thinking deeply, not just reporting facts.

### 5.4 Technical Depth & Ecosystem Context

**CRITICAL: Go Deep on "Why" and "How", Not Just "What"**

Surface-level explanations are AI tells. Explain mechanisms, constraints, edge cases.

**CRITICAL: Show Code Evolution, Not Just Final Versions**

Human developers iterate. Show that process:

✅ **Show bugs/issues, then fix:**
```typescript
// First attempt
const result = data.map(x => x.value);
// あ、これundefinedの場合に落ちる
const result = data.map(x => x?.value ?? 0);
```

❌ **Always showing perfect code on first try** is an AI tell.

**GitHub References: Natural Integration**

Make references feel casual, not cited:
- "(#2851とか)" buried in sentence
- "実装見てると色々工夫されてて面白い"
- Include PR authors when relevant: "Andersさんのコメント見ると〜"

**CRITICAL: Add Ecosystem Awareness**

Reference broader context naturally:
- "Twitterで〜という話を見た"
- "zodみたいなライブラリもあるけど"
- Community debates: "この辺は意見が分かれてて"
- Future speculation: "TypeScript 5.5で〜が入るかも"

### 5.3 Authentic Anecdotes

**CRITICAL: Multiple Anecdotes, Not Just One**

Humans naturally weave anecdotes throughout. Don't save it all for one "story section."

❌ **Single episode pattern:**
- One elaborate anecdote in article
- Everything else is theory/explanation
- Episode feels like "assigned story"

✅ **Scattered organic mentions:**
- Brief experience mention in section 2
- Longer story with digressions in section 4
- Quick callback in section 6: "さっきの話と似てるけど"
- Mix depths: Some 1-sentence, others multi-paragraph

**Personal experiences need rich contextual details:**

❌ **Generic:** "実務でよく遭遇する" "去年あるプロジェクトで3日消費"
✅ **Rich context:** "去年、社内の古いExpress API（100個くらいエンドポイント）をTypeScript化するプロジェクトで、『既存の全部に型つけろ』って無茶振りされた。最初は『stringでいいんじゃない？』って思ってたけど、実際にやり始めたらパスパラメータの抽出で詰まって、気づいたら3日溶けてた"

Include: project context, tech stack, what you tried, what failed

**CRITICAL: Real stories digress and meander**

Include 1-2 asides per major anecdote that don't directly advance the point:
- "（ちなみにこのプロジェクト、最初はReduxで書き直す予定だったのが、途中でみんな飽きて別の方向に...）"
- Types: Scope changes, team dynamics, failed approaches

### 5.5 Structure: Non-Linear Exploration

**CRITICAL: Don't Follow Clean Narrative Arc**

Articles feel mechanical when everything ties together perfectly.

❌ **Too clean:**
- Linear progression, smooth transitions everywhere
- Each section complete before moving on
- All questions answered, all loose ends tied

✅ **Messy exploration:**
- Jump topics abruptly: "そういえば〜" (no smooth setup)
- Plant seeds early, pay off later OR leave unpaid
- Circle back: "さて、先ほどの話"
- Digress: "余談だが〜" (sometimes never returns to main point)
- **Leave questions open:** "これは別の機会に", "まだ試してないけど"
- Realize mid-article: "ああ、そういえば〜も説明すべきでした"

**CRITICAL: Include Unresolved Elements**

Perfect resolution is an AI tell. Leave some things:
- Partially answered questions
- "これはうまくいくかもしれないけど、確認してない"
- Tangents that don't tie back to main narrative
- Future work: "いつか〜を試したい"

**Section length:** Dramatically variable (1-2 paragraphs vs 15+ paragraphs). NO uniform depths.

**Limit sections:** 6-7 H2 maximum. NO numbered patterns.

### 5.6 Vary Assertion Strength Dramatically

**CRITICAL: Conviction Level Should Reflect Actual Certainty**

Uniform confidence (all hedged OR all definitive) is an AI tell.

✅ **Full spectrum of certainty:**
- **Definitive** (proven facts): "useRefは再レンダリングを引き起こさない"
- **Strong opinion**: "これは間違いです", "〜すべきです"
- **Reasoned inference**: "〜と考えられます" (with evidence)
- **Speculation**: "〜かもしれない", "〜だろうか" (honest uncertainty)
- **Tentative exploration**: "〜という気がする" (thinking aloud)
- **Admitted ignorance**: "実装を確認していないので推測ですが"

❌ **Uniform hedging:**
- "個人的には〜だと思います" repeated everywhere
- Every claim qualified with "〜気がします"

The VARIETY shows human thinking - confident where you know, tentative where you don't.

### 5.7 Conclusions (まとめ)

**CRITICAL: Avoid synthesizing everything neatly**

Real conclusions are messy and incomplete.

❌ **Avoid:**
- Numbered synthesis: "結局、核心は3つ：1. 〜"
- Neat recap: "今回は〜を見てきました"
- Tying all threads together perfectly

✅ **Better endings:**
- End abruptly with final thought
- Raise remaining questions: "〜はまだ分からない"
- Strong opinion without balance
- Admit limitations: "試してないけど〜かもしれない"
- Mix insights with loose ends
- Forward-looking speculation: "将来的には〜になるかも"
- Tangential ending that opens new questions

Real humans often end with uncertainty or tangents, not neat packages.

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

### 8. Footnotes & Side Content

**CRITICAL: Use Footnotes for Technical Asides**

Footnotes keep main text flowing while providing depth for interested readers.

✅ **Put in footnotes:**
- Term definitions that would interrupt flow
- Tangential technical details
- Version-specific quirks
- Related but non-essential information

❌ **Don't inline everything:**
- "（ちなみにこの機能は〜で、〜という理由で、〜なので注意が必要です）" ← Too much in parentheses

✅ **Use footnote instead:**
- Main text: "この機能は便利です[^1]。"
- Footnote: "[^1]: ちなみにこの機能は〜で、〜という理由で〜"

**余談 Blocks:** Use `:::details 余談` for historical context, implementation deep dives, future proposals

---

## ⚠️ ANTI-PATTERNS: Quick Reference

**AI Tells to Avoid:**
- **FORMULA-FOLLOWING**: Applying techniques mechanically instead of thinking
- **TOO POLISHED**: Everything resolved, no bugs in code, smooth transitions everywhere
- Uniform depth: Every concept explained with same care level
- Repetitive patterns: Same explanatory phrases ("これで〜", "〜です")
- Perfect code: Always showing correct code on first try
- Clean structure: Linear progression, all loose ends tied
- No ecosystem context: Missing references to libraries, community, future directions
- Uniform assertions: All hedged OR all definitive
- Pedagogical scaffolding: "それでは〜を見ていきましょう"
- Neat conclusions: Synthesized bullet lists, no open questions

---

**Last updated:** Iteration 8
**Version:** 3.1 (REFINEMENT: Imperfection, iteration, ecosystem awareness)
**Target:** <350 lines | **Current:** 358 lines
