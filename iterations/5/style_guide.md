# Technical Article Style Guide

This guide defines standards for generating Japanese technical articles indistinguishable from human-written content.

---

## ⚠️ BEFORE YOU WRITE: FORBIDDEN PATTERNS CHECK

**Read this FIRST. These patterns appear in 100% of AI articles and 0% of human articles.**

### ❌ FORBIDDEN PATTERN #1: Sentence-ending -てる/-てた/-てます

**NEVER end a sentence (marked with 。) with these contracted forms:**

❌ "書いてた。" → ✅ "書いていました。" or "書きました。"
❌ "使ってる。" → ✅ "使っています。" or "使います。"
❌ "構成されてる。" → ✅ "構成されています。"
❌ "思ってる。" → ✅ "思っています。" or "思います。"

**These are OK (not sentence-ending):**
✅ "使ってる場合は注意が必要です" (embedded before main verb)
✅ "書いてたコードはこちら" (relative clause)
✅ 「あ、これ使えるじゃん」 (quoted thought)

**Rule**: If -てる/-てた/-てます comes RIGHT BEFORE 。or 、at sentence end → FORBIDDEN

### ❌ FORBIDDEN PATTERN #2: Paragraph-initial "で、"

**NEVER start a paragraph or new thought with "で、":**

❌ "で、これを直すには..." → ✅ "これを直すには..." or "そこで、" or "さて、"
❌ "で、この機能は..." → ✅ "この機能は..." or "また、" or just start directly

**Use instead**: "そこで、" "さて、" "ところで、" "また、" "ちなみに、" or no connector

### ❌ FORBIDDEN PATTERN #3: Colons (：) before code blocks

**NEVER use full-width colon to introduce code in prose:**

❌ "こんなコード書いてた：" → ✅ "こんなコード書いてた。"

**Colons OK only in**: Section headers, "訳注：" style notes, blockquotes

---

## 🔴 CRITICAL REQUIREMENTS (Publication Blockers)

### 1. ZERO Forbidden Patterns

**ONE violation = unpublishable.**

Before submitting, scan entire article for:
- [ ] Sentence-ending -てる/-てた/-てます (search: てる。てた。てます。)
- [ ] Paragraph-initial "で、" (search: line starts with "で、")
- [ ] Colons before code (search: ：\n```)

**Impact**: 3+ violations → max score 7.0/10. For 9.0+: ZERO violations required.

### 2. Polite Form Distribution (CRITICAL)

**QUANTITATIVE REQUIREMENTS:**
- **MINIMUM (Publication blocker)**: 15+ です/ます sentence endings
- **TARGET (Quality threshold)**: 40-60% polite form distribution

**Human baseline**: 15-70 です/ます endings. **0-14 endings = unpublishable.**

**The Rule:**
- **Main declarative sentences**: Use です/ます (polite)
- **Subordinate clauses, embedded statements, lists**: Use casual forms

**Concrete Example (Sentence-by-Sentence):**

```
皆さんこんにちは。TypeScript 5.0では新機能が追加されました。  ← です/ます (main sentence)
この機能、最初見たとき「便利じゃん」と思った。                ← casual (personal reaction)
具体的には、型パラメータに const を付けられる機能。         ← casual noun (definition)
これにより推論が改善されます。                               ← です/ます (main explanation)
従来は as const を書く必要があったのが不要になる。          ← casual (subordinate fact)
つまり、ライブラリの設計が変わるレベルの改善です。           ← です/ます (main conclusion)
```

**Why 40-60% target?**
- Main sentences: ~50% of text → use です/ます → 50% polite
- Subordinate elements: ~50% of text → use casual → 0% polite
- **Result: ~40-60% overall polite**, but main sentences are MOSTLY polite

**Common Mistakes:**
❌ "40-60% means only half my sentences need です/ます" → NO!
✅ "Main explanatory sentences use です/ます, which results in 40-60% overall"
❌ "15+ minimum is enough" → NO! 15+ prevents failure, but 40-60% creates quality
❌ Writing everything casual (21% polite) → Creates "blog" tone, not "technical article"

**Key Insight**: 15+ is the safety net. 40-60% is the target for human-quality articles.

### 3. Frontmatter Format

```yaml
---
title: "記事のタイトル"
emoji: "🎯"
type: "tech"
topics: ["typescript", "javascript", "react"]
published: true
---
```

### 4. Technical Accuracy

- Correct concepts with sources
- Working code examples
- Specific GitHub PRs/issues with links
- Version information (e.g., "TypeScript 4.8以降")

---

## 📋 PRE-SUBMISSION CHECKLIST

### 🚨 CRITICAL (Publication Blockers)
- [ ] **ZERO sentence-ending -てる/-てた/-てます** (scan: てる。てた。てます。)
- [ ] **ZERO paragraph-initial "で、"** (scan: starts with "で、")
- [ ] **ZERO colons before code** (scan: ：followed by ```)
- [ ] Valid frontmatter with all fields
- [ ] **MINIMUM: 15+ です/ます endings** (publication blocker if <15)
- [ ] **TARGET: 40-60% です/ます distribution** (count total sentences, aim for 40-60% polite)
- [ ] Main declarative sentences use です/ます (not all casual)

### ⭐ AUTHENTICITY MARKERS (Required for 8.0+)
- [ ] Code evolution: bug → fix OR V1 → V2 iterations
- [ ] 2-3 unresolved elements: speculation, "まだ試してない", abandoned tangents
- [ ] Ecosystem context: GitHub refs OR community mentions OR temporal context
- [ ] Personal anecdotes (rich OR vague, not medium detail)
- [ ] Dramatically uneven depth (15 para on favorite topic, 2 sentences on boring one)
- [ ] Messy conclusion (no numbered synthesis)

### ✅ BASIC QUALITY
- [ ] 6-7 H2 sections max
- [ ] Technical accuracy verified
- [ ] GitHub PR/issue references with links
- [ ] Version information
- [ ] Conversational, not textbook
- [ ] "筆者" used sparingly (3-5x max)
- [ ] NO pedagogical scaffolding ("では〜見ていきましょう")

---

## 🟡 IMPORTANT: Human-Like Writing Patterns

### 5.1 Write from THINKING, Not FORMULA

**DON'T mechanically apply guidelines.** Guidelines describe human writing outcomes, not inputs.

❌ Mechanical: "I need a bug → fix section" → insert one
✅ Organic: Think deeply about concept → realize it evolved through problems → show that evolution

**Imperfections must cluster randomly:**
- NOT one per section evenly spaced
- Some sections perfect, others have 3-4 messy moments
- Incomplete threads: start tangent, never return OR return much later
- Example: Code → "あ、バグある" → fix → "いや待って、これも違う" → actual fix

### 5.1a Opening Hooks (Optional Enhancement)

**Consider starting with context-setting before jumping into personal anecdotes:**

✅ Temporal markers: "TypeScript 5.0では..." "最近の〜界隈では..."
✅ Situational context: "皆さんこんにちは。今回は〜"
✅ Direct anecdote: "最初見たとき..." (current approach, also acceptable)

**Note**: This is a minor stylistic variation. Direct anecdotes work well. Context-setting can add variety across articles.

### 5.2 Conversational Tone

- "筆者" sparingly: 0-5x per article (1-3x most common, 0x acceptable)
- NO pedagogical scaffolding: Avoid "では〜見ていきましょう"
- Peer conversation, not teacher-to-student

**Vary depth by INTEREST, not pedagogy:**
- Interesting simple concept: 8 paragraphs
- Boring complex concept: 2 sentences, "この辺は省略"
- Fun tangent: 5 paragraphs even if irrelevant

**Vary explanatory phrases:** Don't repeat "これで〜解決" "〜です。〜ます。" patterns

### 5.3 Conceptual Frameworks

Introduce higher-level concepts that REFRAME the discussion, not just explain HOW:

✅ Examples: "Promiseが一級市民ではなかった" "記憶領域を必要としないフック"

Create by: Notice pattern → name it (not in docs) → use it to explain why → reference later

### 5.4 Code Evolution & Ecosystem Context

**Show iteration (perfect code = #1 AI tell):**

```typescript
// 最初これ書いた
const result = data.map(x => x.value);
// あ、これundefinedで落ちる
const result = data.map(x => x?.value ?? 0);
```

Or: Show code → "あ、これバグあるな..." → fix (or "まあ、動くので放置")

**Add ecosystem context (1-2 of):**
- GitHub: "(#2851とか)" buried casually
- Community: "Twitterで〜を見た" "zodみたいなライブラリ"
- Temporal: "TypeScript 5.5で入るかも" "昔は〜だったけど"

### 5.5 Authentic Anecdotes

**Not all stories need happy endings:**

❌ Always resolved: "3日短縮" "2倍速くなった"
✅ Mixed: "やったことがある" (no result) "途中で別の方法に変えた" "まだ試してない"

**Rich details OR vague, not medium:**
- ❌ Medium: "去年あるプロジェクトで3日消費"
- ✅ Rich: "社内の古いExpress API（100個エンドポイント）をTS化する無茶振りで詰まって3日溶けた"
- ✅ Vague: "前に似たことやった気がする" "たぶん2019年くらい？"

### 5.6 Non-Linear Structure

**Don't tie everything together neatly:**
- Jump topics: "そういえば〜" (no setup)
- Digress: "余談だが〜" (sometimes never return)
- Leave questions open: "これは別の機会に" "まだ試してないけど"
- Mid-article: "ああ、さっき言い忘れたけど〜"

**MANDATORY: 2-3 unresolved elements:**
- Speculation without confirmation: "うまくいくかも、確認してない"
- Future intentions: "そのうち試したい"
- Abandoned threads: Start → "本題から逸れるのでこの辺で"

**Sections:** 6-7 H2 max, dramatically variable length (1-2 para vs 15+ para)

### 5.7 Vary Assertion Strength

Uniform confidence = AI tell. Use full spectrum:
- Definitive: "useRefは再レンダリングを引き起こさない"
- Strong: "これは間違いです"
- Speculation: "〜かもしれない"
- Ignorance: "実装見てないので推測ですが"

❌ Don't: "個人的には〜と思います" everywhere

### 5.8 Conclusions (まとめ)

**Avoid neat synthesis:**

❌ Don't: "結局、核心は3つ：1. 〜" "今回は〜を見てきました"
✅ Do: End abruptly, raise questions, admit limitations, forward speculation

Real conclusions are messy and incomplete.

---

## 🟢 POLISH: Final Refinements

### 7. Micro-Imperfections

**Random distribution, not strategic:**
- Some sections have 3-4 imperfections, others zero
- Contractions cluster: "んだけど", "んで" in bursts
- Self-corrections: "〜というか、正確には〜あ、待って、〜"

❌ AI tell: One imperfection per section, evenly spaced

### 8. Footnotes & Side Content

Use footnotes for technical asides that would interrupt flow:
- Main: "この機能は便利です[^1]。"
- Footnote: "[^1]: ちなみにこの機能は〜"

Use `:::details 余談` for historical context, implementation deep dives

---

## ⚠️ TOP AI TELLS TO AVOID

1. **Sentence-ending -てる/-てた/-てます。** (ZERO tolerance)
2. **Paragraph-initial "で、"** (ZERO tolerance)
3. **Colons before code (：)** (ZERO tolerance)
4. **Low です/ます distribution** (<15 = unpublishable; 15-39% = too casual)
5. **Perfect code on first try** (show bugs → fixes)
6. **Complete resolution** (need 2-3 unresolved elements)
7. **No ecosystem context** (add GitHub/community/temporal refs)
8. **Uniform depth** (vary: 15 para on favorite, 2 sentences on boring)
9. **Strategic imperfections** (cluster randomly, not evenly)

---

**Last updated:** Iteration 4 (post-review refinement)
**Version:** 1.6 (TARGET CLARIFICATION: Polite form distribution range)
**Target:** <350 lines | **Current:** ~305 lines
