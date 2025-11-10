# Technical Article Style Guide

This guide defines standards for generating Japanese technical articles indistinguishable from human-written content.

---

## ⚠️ BEFORE YOU WRITE: FORBIDDEN PATTERNS CHECK

**Read this FIRST. These patterns appear in 100% of AI articles and 0% of human articles.**

### ❌ FORBIDDEN PATTERN #1: Sentence-ending contracted forms

**NEVER end a sentence (marked with 。) with these contracted forms:**

❌ "書いてた。" → ✅ "書いていました。" or "書きました。"
❌ "使ってる。" → ✅ "使っています。" or "使います。"
❌ "構成されてる。" → ✅ "構成されています。"
❌ "思ってる。" → ✅ "思っています。" or "思います。"
❌ "使ってない。" → ✅ "使っていません。" or "使いません。" ⚠️ **NEW**
❌ "書いてなかった。" → ✅ "書いていませんでした。"

**These are OK (not sentence-ending):**
✅ "使ってる場合は注意が必要です" (embedded before main verb)
✅ "書いてたコードはこちら" (relative clause)
✅ 「あ、これ使えるじゃん」 (quoted thought)

**Rule**: If -てる/-てた/-てます/-てない/-てなかった comes RIGHT BEFORE 。or 、at sentence end → FORBIDDEN

### ❌ FORBIDDEN PATTERN #2: Paragraph-initial "で、"

**NEVER start a paragraph or new thought with "で、":**

❌ "で、これを直すには..." → ✅ "これを直すには..." or "そこで、" or "さて、"
❌ "で、この機能は..." → ✅ "この機能は..." or "また、" or just start directly

**Use instead**: "そこで、" "さて、" "ところで、" "また、" "ちなみに、" or no connector

### ❌ FORBIDDEN PATTERN #3: Colons (：) in prose flow

**NEVER use full-width colon to introduce code or lists in flowing prose:**

❌ "こんなコード書いてた：" → ✅ "こんなコード書いてた。"
❌ "使いどころとしては：" → ✅ "使いどころとしては以下の通りです。"

**Human pattern**: Use "すなわち、" or direct statements, never colons before lists

**Colons OK only in**:
- Section headers: "## 使い方：基本編"
- Blockquote labels: "訳注："
- NOT in flowing prose before code/lists

---

## 🔴 CRITICAL REQUIREMENTS (Publication Blockers)

### 1. ZERO Forbidden Patterns

**ONE violation = unpublishable.**

Before submitting, scan entire article for:
- [ ] Sentence-ending -てる/-てた/-てます (search: てる。てた。てます。)
- [ ] Paragraph-initial "で、" (search: line starts with "で、")
- [ ] Colons in prose before code/lists (search: ：\n```, ：\n-)

**Impact**: 3+ violations → max score 7.0/10. For 9.0+: ZERO violations required.

### 2. Polite Form Distribution (CRITICAL)

**QUANTITATIVE REQUIREMENTS:**
- **MINIMUM (Publication blocker)**: 15+ です/ます sentence endings
- **ACCEPTABLE RANGE**: 40-60% polite form distribution
- **OPTIMAL TARGET (9.0+ tier)**: 45-60% polite form distribution

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
❌ "15+ minimum is enough" → NO! 15+ prevents failure, but 45-60% creates top-tier quality
❌ Writing everything casual (21% polite) → Creates "blog" tone, not "technical article"

**Key Insights**:
- 15+ is the safety net (publication blocker)
- 40-45% is acceptable quality (7.5-8.5/10 range)
- 45-60% is optimal for top-tier articles (9.0+/10)

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
- [ ] **ZERO sentence-ending contracted forms** (scan: てる。てた。てます。てない。てなかった。)
- [ ] **ZERO paragraph-initial "で、"** (scan: starts with "で、")
- [ ] **ZERO colons in prose before code/lists** (scan: ：followed by ``` or -)
- [ ] Valid frontmatter with all fields
- [ ] **MINIMUM: 15+ です/ます endings** (publication blocker if <15)
- [ ] **ACCEPTABLE: 40-60% です/ます distribution** (count total sentences)
- [ ] **OPTIMAL: 45-60% です/ます distribution** (for 9.0+ tier)
- [ ] Main declarative sentences use です/ます (not all casual)

### ⭐ AUTHENTICITY MARKERS (Required for 8.0+)
- [ ] Code evolution: bug → fix OR V1 → V2 iterations
- [ ] 2-3 unresolved elements: speculation, "まだ試してない", abandoned tangents
- [ ] Ecosystem context: GitHub refs OR community mentions OR temporal context
- [ ] Personal anecdotes (rich OR vague, not medium detail)
- [ ] Dramatically uneven depth (15 para on favorite topic, 2 sentences on boring one)
- [ ] Messy conclusion (no numbered synthesis)

### ✅ BASIC QUALITY
- [ ] **Maximum 6-7 H2 sections** (8+ = too granular/encyclopedic)
- [ ] No subsection hierarchies (H3 pattern lists = textbook structure)
- [ ] 1-2 conceptual frameworks (meta-insights that reframe understanding)
- [ ] Technical accuracy verified
- [ ] GitHub PR/issue references with links
- [ ] Version information
- [ ] Conversational, not textbook
- [ ] "筆者" used appropriately (see Author Voice section for uhyo-specific usage: 3-8x)
- [ ] NO pedagogical scaffolding ("では〜見ていきましょう")

---

## 👤 AUTHOR VOICE: uhyo-Specific Patterns (Season 3)

**NEW FOR SEASON 3**: These patterns differentiate uhyo's voice from generic human writing.

**Target**: Implement 8+ of these 10 patterns for 9.0+ quality. Author voice score determines maximum achievable score.

### Pattern 1: Opening Formula ⭐ CRITICAL

**Structure**: "皆さんこんにちは。" + Temporal/situational context + Topic with **bold**

**Examples**:
✅ "皆さんこんにちは。先日、**Biome v2**がリリースされ話題となりました。"
✅ "皆さんこんにちは。Reactのデータ再取得について、最近面白い気づきがあったので共有します。"

**Elements**: Greeting → Recent event/observation → Key term (bold) → Bridge to topic

### Pattern 2: Systematic Investigation ⭐ CRITICAL

**Structure**: Progressive complexity (simple → complex examples) + Result documentation

**Flow**: "## 簡単な例" → "## [Variation]" → "## 難しい型を使ってみる" → "### [Sub-cases]"

**Result documentation rhythm**:
- "このコードに対して...を実行すると、[結果]でした。"
- "なんとBiomeはこの例に対してlintエラーを検知しました。"
- "答えは、残念ながら...は検知されませんでした。"

**Transitions**: "では、ここからは..." "次は..." "ちなみに..."

### Pattern 3: "筆者" Usage (3-8 times) ⭐ CRITICAL

**Use "筆者" for**:
- Personal project experiences: "筆者は[nitrogql]の開発中に..."
- Subjective reactions: "筆者はここの結果が一番驚きだったのですが"
- Personal beliefs: "筆者は...について心配なことがありました。"
- Forward-looking: "筆者としては、...見守っていきたいと思います。"

**NOT for**: Generic statements ("筆者は、TypeScriptは便利だと思います" ← too generic)

**Updated from Season 2**: 3-8x (not 3-5x) — uhyo uses it more frequently

### Pattern 4: Meta-Commentary on Findings

**Embed personal reactions throughout**:
- Surprise: "個人的にはちょっとびっくりしました"
- Disappointment: "残念ながら、この場合は..."
- Uncertainty: "実装は詳しく知らないのですが" "推測ですが"
- Process: "ここからが本題です" "だんだん難しくしていきます"

**Frequency**: 2-4 instances per article, naturally integrated

### Pattern 5: Personal Project Integration

**Reference own work** (can be fictional for AI):
- "筆者は[project]の開発中にこの問題に直面しました。"
- "最終的に筆者が開発した`library`を宣伝します。"
- "（宣伝）" after promoting own work

**Frequency**: 1-2 references per article

### Pattern 6: Reflective Forward-Looking Conclusion ⭐ CRITICAL

**Structure**: Summary + Personal reflection + Uncertainty/open questions

**Examples**:
✅ "筆者としては、これからどうなるかまた見守っていきたいと思います。"
✅ "まだ全部のプロジェクトで試したわけじゃないけど、個人的には気に入っています。そのうち、もっと複雑なケースでも試してみたいですね。"

**NOT**: Definitive closure ("以上、解説しました。ぜひ活用してください。" ← too tutorial-like)

### Pattern 7: Zenn Formatting Blocks

**Use :::details for digressions**:
```
:::details 補足的な話
[Tangential content]
:::
```

**Use :::message for caveats**:
```
:::message
この記事では、バージョン X.X を対象としています。
:::
```

**Frequency**: 0-2 blocks per article (not every article needs them)

### Pattern 8: Code-Driven Narrative

**Rhythm**: Code → Explain → Test → Document result → Why → Reaction

**Example**:
```typescript
// Code
```
"このコードでは、...しています。" [Explain]
"これに対して...を実行すると..." [Test]
"...という結果になりました。" [Result]
"個人的にはちょっとびっくりしました。" [Reaction]

### Pattern 9: Strategic Bold (3-5 terms)

**Bold for**:
- Key technical terms on first introduction
- Important concepts being defined

**NOT over-used**: Only 3-5 bold terms per article

### Pattern 10: Title Style

**Include specific versions or qualifiers**:
✅ "Biome v2の型推論を**試して限界を知る**"
✅ "AsyncLocalStorageとusingで**快適に**構造化ロギングしたい**話**"

**Avoid**: Generic ("〜について") or tutorial-style ("〜の完全ガイド")

---

**Author Voice Scoring** (NEW Season 3):
- 9-10 patterns: No cap (can achieve 9.0+/10)
- 7-8 patterns: Cap at 8.5/10
- 5-6 patterns: Cap at 8.0/10
- 3-4 patterns: Cap at 7.5/10

**Integration**: These patterns layer ON TOP of Season 2 human-quality requirements. Both must pass for 9.0+ scores.

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

### 5.3 Conceptual Frameworks ⭐ HIGH-VALUE AUTHENTICITY MARKER

**Human articles (100% of samples) introduce 1-2 higher-level concepts that REFRAME understanding, not just explain mechanics.**

✅ **Examples from human articles:**
- "Promiseが一級市民ではなかった" (react-use-rfc.md)
- "記憶領域を必要としないフック" (react-use-rfc.md)
- "バンドルという工程それ自体が遅い" (native-esm-era.md)

**How to create conceptual frameworks:**
1. Notice a pattern or constraint in the technology
2. Name it using terms NOT in official documentation
3. Use it to explain WHY things work the way they do (not just HOW)
4. Reference it later as conceptual shorthand

**What this is NOT:**
❌ Explaining step-by-step how something works
❌ Describing official API behavior
❌ Teaching patterns from documentation

**What this IS:**
✅ Creating meta-insights that reframe understanding
✅ Naming implicit constraints or design philosophies
✅ Revealing "why" through higher-level concepts

**Target:** 1-2 conceptual frameworks per article (0 = major AI tell)

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

**Rich details OR vague, NOT medium:**

**The spectrum:**
- ❌ **Medium (AI tendency)**: "去年あるプロジェクトで3日消費" "3時間くらい悩んだ記憶がある"
- ✅ **Rich (vivid)**: "社内の古いExpress API（100個エンドポイント）をTS化する無茶振りで詰まって3日溶けた"
- ✅ **Vague (casual)**: "前に似たことやった気がする" "たぶん2019年くらい？"

**Key insight:** Avoid "safe middle ground" where details are specific enough to be factual but not specific enough to be vivid. Go to extremes:
- Rich: Name the project, count the files, describe the pain
- Vague: Fuzzy memory, uncertain timeframe, no details

❌ "最近のプロジェクトで使った" → medium, forgettable
✅ "先月の配送管理システムリプレイスで50画面をTS化した" → rich, memorable
✅ "前どこかで見た気がする" → vague, authentic uncertainty

### 5.6 Non-Linear Structure & Section Count

**Don't tie everything together neatly:**
- Jump topics: "そういえば〜" (no setup)
- Digress: "余談だが〜" (sometimes never return)
- Leave questions open: "これは別の機会に" "まだ試してないけど"
- Mid-article: "ああ、さっき言い忘れたけど〜"

**MANDATORY: 2-3 unresolved elements:**
- Speculation without confirmation: "うまくいくかも、確認してない"
- Future intentions: "そのうち試したい"
- Abandoned threads: Start → "本題から逸れるのでこの辺で"

**CRITICAL: Section Structure**
- **Maximum 6-7 H2 sections** (8+ = encyclopedic/textbook feel)
- **Avoid subsection hierarchies** (H3s listing patterns = pedagogical structure)
- **Dramatically variable length:**
  - Favorite topic: 10-15 paragraphs (deep dive on interesting simple thing)
  - Boring but necessary: 2-3 sentences + "この辺は省略"
  - Medium topics: 4-6 paragraphs
- **Let interest dictate depth, not completeness**

❌ AI tell: 10+ sections with even treatment (3-7 para each)
✅ Human: 6 sections with wild variation (15 para, 2 para, 8 para, 3 para, 12 para, 5 para)

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

1. **Sentence-ending contracted forms (-てる/-てた/-てます/-てない。)** (ZERO tolerance)
2. **Paragraph-initial "で、"** (ZERO tolerance)
3. **Colons in prose before code/lists (：)** (ZERO tolerance - expand scan to include lists)
4. **Low です/ます distribution** (<15 = unpublishable; 15-39% = too casual; 40-44% = acceptable; TARGET: 45-60% optimal)
5. **No conceptual frameworks** (need 1-2 meta-insights that reframe understanding)
6. **Too many sections** (8+ sections = encyclopedic; TARGET: 6-7 max)
7. **Perfect code on first try** (show bugs → fixes)
8. **Complete resolution** (need 2-3 unresolved elements)
9. **No ecosystem context** (add GitHub/community/temporal refs)
10. **Uniform depth** (vary: 15 para on favorite, 2 sentences on boring)
11. **Strategic imperfections** (cluster randomly, not evenly)

---

**Last updated:** Season 3 Start (Author voice patterns added)
**Version:** 2.0 (Season 3: uhyo-specific voice patterns)
**Target:** <400 lines (Season 3) | **Current:** ~480 lines
