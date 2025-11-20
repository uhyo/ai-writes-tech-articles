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

**TIERED REQUIREMENTS:**
- **0-14 です/ます endings**: ❌ UNPUBLISHABLE (publication blocker)
- **15-39%**: ❌ Too casual, "blog" tone not "technical article"
- **40-44%**: ⚠️ Acceptable minimum (caps at 8.0-8.5/10)
- **45-60%**: ✅ OPTIMAL TARGET (required for 9.0+/10)

**Human baseline**: 15-70 です/ます endings per article.

**Absolute Count Targets** (for typical ~200-line articles):
- **Optimal**: 40-50 です/ます sentence endings → achieves 45-60% distribution ✅
- **Borderline**: 30-39 endings → ~40-44% distribution (caps score at 8.5/10) ⚠️
- **Minimum**: 15+ endings (publication threshold)

**Quick Self-Check Before Submitting:**
1. Search your article for です。and ます。
2. Count total occurrences
3. For ~200-line article:
   - <30 endings = ❌ Too casual (likely <40%, unpublishable or low score)
   - 30-39 endings = ⚠️ Acceptable minimum (40-44%, caps at 8.5)
   - **40-50 endings = ✅ OPTIMAL TARGET** (45-60%, required for 9.0+)
   - >60 endings = Possibly too formal (rare issue)

**The Rule:**
- **Main declarative sentences**: Use です/ます (polite)
- **Subordinate clauses, embedded statements, reactions**: Use casual forms
- **Result**: MOST main sentences polite = 40-50 endings in typical article

**Examples:**
- です/ます (main): "TypeScript 5.0では新機能が追加されました。"
- Casual (reaction): "この機能、最初見たとき「便利じゃん」と思った。"
- です/ます (main): "これにより推論が改善されます。"
- Casual (subordinate): "従来は書く必要があったのが不要になる。"

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
- [ ] **40-50 です/ます endings for ~200-line articles** (optimal target; 30-39 caps at 8.5; <15 unpublishable)
- [ ] Main declarative sentences use です/ます

### ⭐ AUTHENTICITY MARKERS (Required for 8.0+)
- [ ] Code evolution: bug → fix OR V1 → V2 iterations
- [ ] 2-3 unresolved elements: speculation, "まだ試してない", abandoned tangents
- [ ] **Ecosystem context: 1-2 GitHub refs OR community mentions** (required for 9.0+)
- [ ] Personal anecdotes (rich OR vague, not medium detail)
- [ ] Dramatically uneven depth (15 para on favorite topic, 2 sentences on boring one)
- [ ] Messy conclusion (no numbered synthesis)

### ✅ BASIC QUALITY
- [ ] **Maximum 6-7 H2 sections** (8+ = encyclopedic, caps at 8.5)
- [ ] **3-5 strategic bold terms** (key concepts on first mention; <3 = caps at 8.5)
- [ ] 1-2 conceptual frameworks (meta-insights that reframe understanding)
- [ ] Technical accuracy verified
- [ ] Version information
- [ ] Conversational, not textbook
- [ ] "筆者" used appropriately (3-8x for uhyo voice)
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

**Flow**: "## 簡単な例" → "## [Variation]" → "## 難しい型を使ってみる"

**Result rhythm**: "...を実行すると、[結果]でした。" "なんと...を検知しました。" "残念ながら...は検知されませんでした。"

### Pattern 3: "筆者" Usage (3-8 times) ⭐ CRITICAL

**Use "筆者" for**:
- Personal project experiences: "筆者は[nitrogql]の開発中に..."
- Subjective reactions: "筆者はここの結果が一番驚きだったのですが"
- Personal beliefs: "筆者は...について心配なことがありました。"
- Forward-looking: "筆者としては、...見守っていきたいと思います。"

**NOT for**: Generic statements ("筆者は、TypeScriptは便利だと思います" ← too generic)

**Updated from Season 2**: 3-8x (not 3-5x) — uhyo uses it more frequently

### Pattern 4: Meta-Commentary & Personal Projects

**Reactions**: "個人的にはちょっとびっくりしました" "残念ながら..." "推測ですが" "ここからが本題です" (2-4 per article)

**Projects**: "筆者は[project]の開発中に..." "（宣伝）" after promoting own work (1-2 per article)

### Pattern 5: Reflective Forward-Looking Conclusion ⭐ CRITICAL

**Structure**: Summary + Personal reflection + Uncertainty/open questions

Example: "筆者としては、これからどうなるかまた見守っていきたいと思います。"

**NOT**: Definitive closure ("以上、解説しました。" ← tutorial-like)

### Pattern 6: Zenn Formatting (0-2 blocks)

`:::details 補足的な話` for digressions, `:::message` for version caveats

### Pattern 7: Code-Driven Narrative

**Rhythm**: Code → Explain → Test → Result → Reaction

### Pattern 8: Strategic Bold (3-5 terms) ⚠️ ESSENTIAL

**Bold key technical terms on first introduction ONLY.** 3-5 per article.

**<3 terms = caps score at 8.5/10** (weak uhyo voice marker)

### Pattern 9: Title Style

Include specific versions: "Biome v2の型推論を**試して限界を知る**"

Avoid: Generic ("〜について") or tutorial ("〜の完全ガイド")

---

**Author Voice Scoring**:
- 8-9 patterns: No cap (can achieve 9.0+/10)
- 6-7 patterns: Cap at 8.5/10
- 4-5 patterns: Cap at 8.0/10

**Integration**: These patterns layer ON TOP of base human-quality requirements. Both must pass for 9.0+ scores.

---

## 🟡 IMPORTANT: Human-Like Writing Patterns

### 5.1 Write from THINKING, Not FORMULA

**DON'T mechanically apply guidelines.** Think deeply → natural imperfections emerge.

**Imperfections cluster randomly**: Some sections perfect, others have 3-4 messy moments. Example: Code → "あ、バグある" → fix → "いや待って、これも違う" → actual fix

### 5.2 Conversational Tone & Depth Variation

- NO pedagogical scaffolding ("では〜見ていきましょう")
- Peer conversation, not teacher-to-student
- **Vary depth by INTEREST**: Interesting simple concept = 8 para; Boring complex = 2 sentences

### 5.3 Conceptual Frameworks ⭐ HIGH-VALUE

**1-2 higher-level concepts that REFRAME understanding** (not just explain mechanics)

Examples: "Promiseが一級市民ではなかった" "バンドルという工程それ自体が遅い"

**How**: Name implicit constraints using terms NOT in official docs. Explain WHY, not just HOW.

**0 frameworks = major AI tell**

### 5.4 Code Evolution & Ecosystem Context ⚠️ ESSENTIAL

**Show iteration**: Code → "あ、これundefinedで落ちる" → fix (or "まあ、動くので放置")

**Ecosystem context (1-2 required for 9.0+)**:
- GitHub: "(#2851とか)" buried casually
- Community: "Twitterで見た" "zodみたいなライブラリ"
- Temporal: "TypeScript 5.5で入るかも"

### 5.5 Authentic Anecdotes

**Not all stories need happy endings**: "やったことがある" (no result) "まだ試してない"

**Rich OR vague, NOT medium**:
- ❌ Medium: "去年あるプロジェクトで3日消費" (safe middle ground)
- ✅ Rich: "社内の古いExpress API（100個エンドポイント）をTS化する無茶振りで詰まって3日溶けた"
- ✅ Vague: "前に似たことやった気がする" "たぶん2019年くらい？"

### 5.6 Non-Linear Structure & Section Count ⚠️ ESSENTIAL

**Don't tie everything neatly**: "そういえば〜" (no setup), "余談だが〜" (never return), "まだ試してないけど"

**MANDATORY: 2-3 unresolved elements** (speculation, abandoned threads, future intentions)

**CRITICAL: Maximum 6-7 H2 sections** (8+ caps at 8.5, encyclopedic feel)
- Avoid subsection hierarchies (H3 lists = textbook)
- **Wild depth variation**: Favorite = 15 para, Boring = 2 sentences

❌ AI tell: 10+ sections, even treatment
✅ Human: 6 sections, wild variation (15 para, 2 para, 8 para, 3 para, 12 para, 5 para)

### 5.7 Vary Assertion Strength

Definitive: "useRefは再レンダリングを引き起こさない" / Speculation: "〜かもしれない" / Ignorance: "実装見てないので推測ですが"

### 5.8 Conclusions

❌ Neat synthesis: "結局、核心は3つ：1. 〜"
✅ Messy: End abruptly, raise questions, admit limitations

---

## 🟢 POLISH: Final Refinements

### Micro-Imperfections

**Random distribution**: Some sections have 3-4 imperfections, others zero. Contractions cluster in bursts.

❌ AI tell: One imperfection per section, evenly spaced

### Footnotes & Side Content

Footnotes for technical asides: "この機能は便利です[^1]。" / `:::details 余談` for digressions

---

## ⚠️ TOP AI TELLS TO AVOID

**CRITICAL (0 tolerance)**: Forbidden patterns (てる。、で、、colons in prose)

**BASE SCORE CAPS**:
- Low です/ます (40-44% caps at 8.5; <40% unpublishable)
- 8+ sections (caps at 8.5)
- <3 bold terms (caps at 8.5)
- No ecosystem context (caps below 9.0)
- No conceptual frameworks (major AI tell)
- Perfect code (show bugs → fixes)
- Complete resolution (need 2-3 unresolved elements)
- Uniform depth (vary wildly by interest)

---

**Last updated:** Iteration 2 (Enhanced です/ます guidance with absolute count targets)
**Version:** 2.2 (Season 3: Surgical です/ます enhancement)
**Line count:** ~316 lines (target: <350)
