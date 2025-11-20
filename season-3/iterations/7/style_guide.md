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

🚨 **ABSOLUTE THRESHOLD RULE**: 40-50 です/ます endings is MANDATORY for 9.0+ scores, regardless of article length.

**Scoring Tiers (by ABSOLUTE COUNT)**:
- **0-14 endings**: ❌ UNPUBLISHABLE (publication blocker)
- **15-31 endings**: ⚠️ Caps at 7.0-7.5/10 (blog tone)
- **32-39 endings**: ⚠️ Caps at 8.0/10 (too casual for technical article)
- **40-49 endings**: ✅ Required for 9.0+ eligibility (target zone)
- **50-70 endings**: ✅ OPTIMAL for 9.0+ (preferred range)
- **70+ endings**: Possibly too formal (rare issue)

**⚠️ CRITICAL INSIGHT (from Iteration 6 failure)**:
- Iteration 5: 51 endings (231 lines, 22.1%) = 9.3/10 ✅
- Iteration 6: 32 endings (151 lines, 21.2%) = 8.0/10 ❌ (CAPPED)
- **Why similar percentages scored differently**: 40-50 is an ABSOLUTE minimum, NOT a percentage that scales down for short articles.

**Article Length Requirements**:
- **Target length**: 180-230 lines (proven sweet spot)
- **Short articles (<180 lines)**: High risk - hard to reach 40 endings naturally
  * Options: (1) Expand article to 180+ lines, OR (2) Accept 8.0/10 cap
- **Long articles (>250 lines)**: Scale up to 50-60 endings proportionally

**Pre-Submission Verification** (MANDATORY):
1. Count article length: `wc -l article.md` → Target 180-230 lines
2. Search for です。: Count manually, record exact number
3. Search for ます。: Count manually, record exact number
4. **Total must be ≥40 for 9.0+ eligibility** (NOT negotiable)
5. Verify count accuracy: Re-count to confirm (±1 tolerance only)
6. If <40 endings: Expand article OR convert casual sentences to です/ます

**⚠️ ACCURACY WARNING**: Writer claiming "47 endings" when actual is 32 (32% error) = PUBLICATION BLOCKER. Must manually verify.

**The Writing Rule**:
- **Main declarative sentences**: です/ます (polite)
- **Subordinate clauses, reactions, embedded statements**: Casual forms
- **Result**: ~70-80% of main sentences polite = 40-50 endings in 200-line article

**Examples**:
- です/ます: "TypeScript 5.0では新機能が追加されました。"
- Casual (reaction): "この機能、最初見たとき「便利じゃん」と思った。"
- です/ます: "これにより推論が改善されます。"
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
- [ ] **Article length: 180-230 lines** (run `wc -l article.md` to verify; <180 risks です/ます insufficiency)
- [ ] **ZERO sentence-ending contracted forms** (scan: てる。てた。てます。てない。てなかった。)
- [ ] **ZERO paragraph-initial "で、"** (scan: starts with "で、")
- [ ] **ZERO colons in prose before code/lists** (scan: ：followed by ``` or -)
- [ ] Valid frontmatter with all fields
- [ ] **40+ です/ます endings ABSOLUTE** (count です。+ ます。manually; verify twice; <40 = max 8.0/10 regardless of %)
- [ ] **Target: 50-70 endings for 9.0+** (long articles >250 lines need proportionally more)
- [ ] Main declarative sentences use です/ます (70-80% of main sentences)

### ⭐ AUTHENTICITY MARKERS (Required for 8.0+)
- [ ] Code evolution: bug → fix OR V1 → V2 iterations
- [ ] 2-3 unresolved elements: speculation, "まだ試してない", abandoned tangents
- [ ] **Ecosystem context: 1-2 GitHub refs OR community mentions** (required for 9.0+)
- [ ] Personal anecdotes (rich OR vague, not medium detail)
- [ ] Dramatically uneven depth (15 para on favorite topic, 2 sentences on boring one)
- [ ] Messy conclusion (no numbered synthesis)

### ✅ BASIC QUALITY
- [ ] **Maximum 6-7 H2 sections** (8+ = encyclopedic, caps at 8.5)
- [ ] **3-5 strategic bold TERMS** (1-4 words max; no full clauses; <3 = caps at 8.5)
- [ ] 1-2 conceptual frameworks (meta-insights that reframe understanding)
- [ ] Technical accuracy verified
- [ ] Version information
- [ ] Conversational, not textbook
- [ ] **"筆者" used 5-6 times (optimal)** or 3-4 times (borderline) for uhyo voice
- [ ] **Zenn formatting when applicable** (:::message for version caveats if discussing specific versions)
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

**Structure**: VERTICAL complexity progression (simple → complex examples) within a single concept

**Flow**: "## 簡単な例" → "## [Variation]" → "## 難しい型を使ってみる"

**Result rhythm**: "...を実行すると、[結果]でした。" "なんと...を検知しました。" "残念ながら...は検知されませんでした。"

**NOT Systematic** ❌: Horizontal coverage (setup → test → compare → real-world) = different aspects, not complexity escalation
**IS Systematic** ✅: Vertical depth (simple case → add variation → complex case → edge case) = progressive complexity within same concept

### Pattern 3: "筆者" Usage (5-6 typical, 3-8 acceptable) ⭐ CRITICAL

**FREQUENCY GUIDANCE**:
- **Optimal**: 5-6 uses (most characteristic of uhyo voice)
- **Borderline**: 3-4 uses (technically passing but weak author presence)
- **Maximum**: 8 uses (upper acceptable limit)

**Use "筆者" for**:
- Personal project experiences: "筆者は[nitrogql]の開発中に..."
- Subjective reactions: "筆者はここの結果が一番驚きだったのですが"
- Personal beliefs: "筆者は...について心配なことがありました。"
- Forward-looking: "筆者としては、...見守っていきたいと思います。"

**NOT for**: Generic statements ("筆者は、TypeScriptは便利だと思います" ← too generic)

**⚠️ INTENSITY MATTERS**: 3 uses meets minimum but reduces author voice score. Target 5-6 for authentic uhyo intensity.

### Pattern 4: Meta-Commentary & Personal Projects

**Reactions**: "個人的にはちょっとびっくりしました" "残念ながら..." "推測ですが" "ここからが本題です" (2-4 per article)

**Projects - DEPTH REQUIRED**: Three acceptable levels:
- ❌ Insufficient: "筆者が使っていたカスタムプラグイン" (vague, no context)
- ✓ Acceptable: "筆者は自分のプロジェクト（TypeScript + Express + PostgreSQL構成）で試したところ、ネイティブモジュールで問題に遭遇した" (tech stack + specific problem + outcome)
- ✅ Rich (ideal): "筆者は[nitrogql]の設定ファイル読み込みで[specific problem]があり、[solution]を試したところ[result]だった（宣伝）"
- ✅ Central: Entire article about personal project (like nitrogql-beta-release)

**Note**: Acceptable-level project references (tech stack + problem + outcome) can achieve 9.0+ scores when other patterns are strong

### Pattern 5: Reflective Forward-Looking Conclusion ⭐ CRITICAL

**Structure**: Summary + Personal reflection + Uncertainty/open questions

Example: "筆者としては、これからどうなるかまた見守っていきたいと思います。"

**NOT**: Definitive closure ("以上、解説しました。" ← tutorial-like)

### Pattern 6: Zenn Formatting (0-2 blocks)

**WHEN TO USE**:
- `:::message` for version-specific caveats or important warnings (use when article discusses specific versions)
- `:::details 補足的な話` for tangential explorations that would disrupt main flow
- **If article has version-specific information**: :::message is expected (not optional)
- **If no natural use case**: Absence is acceptable

**EXAMPLES**:
```
:::message
この記事はNext.js 14.0時点の挙動です。Next.js 15では挙動が変わる可能性があります。
:::
```

```
:::details カスタムエラーのシリアライゼーションについて
Server Actionsのエラーは...
:::
```

**FREQUENCY**: 0-2 blocks per article (1 is most natural when applicable)

### Pattern 7: Code-Driven Narrative

**Rhythm**: Code → Explain → Test → Result → Reaction

### Pattern 8: Strategic Bold (3-5 terms) ⚠️ ESSENTIAL

**Bold key technical TERMS on first introduction ONLY.** 3-5 per article.

**WHAT TO BOLD**:
✅ Technical terms/concepts (1-4 words max): **Server Actions**, **型推論**, **並列処理の強化**, **インクリメンタルビルド**
✅ Single terms or short phrases representing concrete technical concepts

**WHAT NOT TO BOLD**:
❌ Section labels in prose: "**良い点**: ビルドが速い" "**テストプロジェクト**: React 18"
❌ Full clauses/sentences: "**クライアント側でcatchしていないのに、アプリケーション全体がクラッシュしない**"
❌ Concepts or ideas longer than 4 words
❌ Generic descriptive phrases

**PRECISION RULE**: If bold is longer than 4 words, it's probably wrong. Bold should be technical TERMS, not explanatory CLAUSES.

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

**Ecosystem context - MANDATORY for 9.0+** (at least 1-2 references):
- GitHub issues/PRs: "(#2851とか)" "issue #XXXで..." ← ✅ COUNTS
- GitHub repo links ONLY: "https://github.com/..." ← ❌ DOESN'T COUNT (too generic)
- Community: "Twitterで見た" "zodみたいなライブラリ" "Discordで話題に"
- Temporal: "TypeScript 5.5で入るかも" "次のバージョンで修正される予定"

**NOTE**: Missing ecosystem context = automatic cap below 9.0/10 regardless of other quality

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

## 📊 SUCCESS PATTERNS (Iterations 5-6 Learning)

**Iteration 5 (9.3/10)**: 51 endings, 231 lines, all 10 uhyo patterns ✅
**Iteration 6 (8.0/10)**: 32 endings, 151 lines, all 10 uhyo patterns but CAPPED by です/ます ❌

**Key Insight**: Perfect author voice (10/10) is NOT enough. Must also meet absolute です/ます threshold (40-50 endings).

**Proven 9.0+ Formula**:
1. Article length: 180-230 lines (sweet spot)
2. です/ます: 40-70 absolute count (NON-NEGOTIABLE)
3. Author voice: 8+ uhyo patterns (see Section 👤)
4. Zero forbidden patterns (see Section ⚠️)
5. Ecosystem context: 1-2 GitHub issues/PRs or community refs

---

**Last updated:** Iteration 6 (Clarified absolute です/ます threshold after regression)
**Version:** 2.6 (Season 3: Absolute count requirement)
**Line count:** ~400 lines (consolidated success patterns)
