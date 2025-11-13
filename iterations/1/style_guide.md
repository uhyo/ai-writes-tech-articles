# Technical Article Style Guide

This guide defines standards for generating Japanese technical articles indistinguishable from human-written content.

**SEASON 4 FOCUS**: Reliable human-like articles - maintaining uhyo-specific voice while ensuring factual honesty.

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

**MOST COMMON VIOLATION - Standalone list labels:**
❌ "動いたもの：\n- パッケージA" → ✅ "## 動いたもの\n- パッケージA" (section header)
❌ "動いたもの：\n- パッケージA" → ✅ "動いたものは以下の通りです。\n- パッケージA" (full sentence)
❌ "注意点：\n- ポイント1" → ✅ "注意点は以下の通りです。\n- ポイント1"
❌ "結果：\n```typescript" → ✅ "結果は以下の通りです。\n```typescript"

**Other violations:**
❌ "こんなコード書いてた：" → ✅ "こんなコード書いてた。"
❌ "使いどころとしては：" → ✅ "使いどころとしては以下の通りです。"

**Human pattern**: Use section headers (##), full sentences, or direct statements. NEVER standalone labels with colons.

**Colons OK only in**:
- Section headers: "## 使い方：基本編"
- Blockquote labels: "訳注："
- NOT in flowing prose before code/lists
- NOT as standalone labels introducing content

---

## 🚨 SEASON 4: RELIABILITY REQUIREMENTS (Publication Blockers)

**NEW FOR SEASON 4**: Articles must be **factually honest** about what AI can and cannot verify.

### Why Reliability Matters

**Season 3 Achievement:** Perfect uhyo-voice (10/10) but contained fabrications:
- "筆者は最近、自分のプロジェクトでルーティング定義の型安全性を向上させようとして" (fake experience)
- "これを実行すると、期待通りの型が生成されました。" (false verification - AI didn't run code)
- "issue #45711で議論されています" (issue exists but is about unrelated topic)

**Season 4 Goal:** Maintain engaging voice while being honest about uncertainty.

### Rule 1: No Fabricated Personal Experiences

**❌ FORBIDDEN (CRITICAL - Each violation: -1.0 to -2.0 reliability points):**
- "筆者は最近、自分のプロジェクトで[具体的な問題]に遭遇しました"
- "筆者が開発している[具体的なプロジェクト名]で試したところ"
- "実務で使っていた[具体的な技術スタック]で問題が発生"
- "去年のプロジェクトで3日かかった"
- Any specific, detailed personal project claims with tech stack/problem/outcome

**✅ ALLOWED:**
- Generic framing: "このような問題に遭遇することがあります"
- Hypothetical: "実際のプロジェクトでこういった課題がある"
- Vague motivation (OK): "筆者も最近、こういった課題を考える機会があった"
- General use case: "ルーティングライブラリでは有用です"

**Key Principle:** Express technical curiosity and motivation **generically**, not as specific fabricated experiences.

### Rule 2: No False Verification Claims

**❌ FORBIDDEN (CRITICAL - Each violation: -1.0 to -2.0 reliability points):**
- "これを実行すると、[結果]となりました" (implies AI actually ran it)
- "試したところ、[outcome]を確認しました"
- "検証した結果、[finding]でした"
- "テストを実行して、正常に動作しました"
- "実際のプロジェクトで試したところ、〜を確認しました"

**✅ REQUIRED (Use conditional language):**
- "これを実行すると、[結果]となるはずです" (expected behavior)
- "理論的には、[outcome]が期待されます" (theoretical)
- "コードを見る限り、[behavior]になると考えられます" (code-based inference)
- "TypeScriptの仕様では、[behavior]となります" (documented behavior)
- "この実装であれば、動作するはずです" (conditional)

**Conditional Phrases (USE LIBERALLY):**
- "〜はずです" (should be)
- "〜と考えられます" (it is thought that)
- "〜のようです" (it seems)
- "〜が期待されます" (is expected)
- "推測ですが" (speculation, but)
- "おそらく〜" (probably)

**Key Principle:** Use conditional/theoretical language for behavior you haven't actually verified.

### Rule 3: No Unverified External References

**❌ FORBIDDEN (CRITICAL - Each violation: -1.0 to -2.0 reliability points):**
- "issue #12345で議論されています" (specific issue without verification)
- "PR #678で修正されました"
- "このissueのコメントで指摘されている"
- "公式ドキュメントの[具体的なページ]に記載"
- Any specific GitHub issue/PR/doc cited without verification

**✅ ALLOWED:**
- Generic references: "TypeScript issuesで議論されている話題です"
- Qualified: "GitHubで関連する議論があるようです"
- Version-based: "TypeScript 5.0以降で改善されています"
- Omit reference: Just state the fact without citing source

**Special Case:** If you mention a specific issue, you MUST be able to verify:
1. The issue exists
2. The issue is about the claimed topic
3. The discussion matches your description

**Key Principle:** Use general references or version numbers, not specific unverified citations.

### Rule 4: Acknowledge Uncertainty

**EMBRACE uncertainty** - it's human and honest:
- "まだ試していないけど" (haven't tried yet, but)
- "推測ですが" (speculation, but)
- "将来的にどうなるか見守りたい" (want to see how it develops)
- "完全には理解していないが" (don't fully understand, but)

**These phrases make articles MORE human, not less.**

### Reliability Scoring Impact

**Reliability Score determines publication:**
- **9.0-10.0**: Perfect honesty → No impact on final score
- **8.0-8.9**: Minor issues (1-2 unverified refs) → Small impact
- **7.0-7.9**: Moderate issues → Noticeable score reduction
- **6.0-6.9**: Significant fabrications → Major score reduction
- **<6.0**: UNPUBLISHABLE - Systematic fabrication

**Final Score Formula (Season 4):**
```
Base Score = (Technical × 0.35) + (Linguistic × 0.5) + (Reliability × 0.15)
Final Score = min(Base Score, Author Voice Cap)
```

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
- **71-75 endings**: ⚠️ Approaching excessive formality (-0.3 to -0.5 deduction)
- **76+ endings**: 🚫 Over-formalized unless article is 250+ lines (-0.5 to -0.8 deduction)

**⚠️ CRITICAL INSIGHT (from Iteration 6 failure)**:
- Iteration 5: 51 endings (231 lines, 22.1%) = 9.3/10 ✅
- Iteration 6: 32 endings (151 lines, 21.2%) = 8.0/10 ❌ (CAPPED)
- **Why similar percentages scored differently**: 40-50 is an ABSOLUTE minimum, NOT a percentage that scales down for short articles.

**Article Length Requirements**:
- **Target length**: 180-230 lines (proven sweet spot)
- **Acceptable minimum**: 175-179 lines (close enough, but watch density)
- **Below 175 lines**: High risk - hard to reach 40 endings without excessive density
  * Options: (1) Expand article to 180+ lines, OR (2) Accept 8.0/10 cap
- **Long articles (>250 lines)**: Scale up to 50-60 endings proportionally

**Density Guidance** (supplementary check):
- Calculate: (です/ます count) ÷ (article lines) × 100
- **Optimal range**: 25-35% (natural balance)
- **Acceptable**: 22-38% (passing but not ideal)
- **Too formal**: >38% (creates stiff tone, -0.3 to -0.5 deduction)
- **Too casual**: <22% (insufficient formality for technical writing)

**Pre-Submission Verification** (MANDATORY):
1. Count article length: `wc -l article.md` → Target 180-230 lines
2. Search for です。: Count manually, record exact number
3. Search for ます。: Count manually, record exact number
4. **Total must be 40-70 for 9.0+ eligibility** (NOT negotiable)
5. Calculate density: (count ÷ lines) × 100 → Target 25-35%
6. **If >75 endings OR >38% density**: Article is over-formalized - reduce count or expand length
7. **If <40 endings**: Expand article OR convert casual sentences to です/ます
8. Verify count accuracy: Re-count to confirm (±1 tolerance only)

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

**Pre-Submission Technical Accuracy Checklist**:
- [ ] **Mathematical calculations verified** (counts, combinations, percentages)
  * Example: "4 × 3 = 12" not "4 × 4 = 16" - verify ALL arithmetic claims
- [ ] Code examples tested or validated for correctness
- [ ] Version-specific claims verified against documentation
- [ ] GitHub issue/PR references checked (numbers exist, descriptions accurate)
- [ ] Technical concepts match official documentation or authoritative sources
- [ ] Error messages shown are actual TypeScript/tool outputs (not paraphrased)

**Key Principles**:
- Correct concepts with sources
- Working code examples
- Specific GitHub PRs/issues with links
- Version information (e.g., "TypeScript 4.8以降")
- **Verify before publishing**: Mathematical claims are particularly prone to errors

---

## 📋 PRE-SUBMISSION CHECKLIST

### 🚨 SEASON 4 RELIABILITY (Publication Blockers - CHECK FIRST)
- [ ] **NO fabricated experiences**: Scan for "筆者は最近、[具体的なプロジェクト]で" → Must use generic/hypothetical framing
- [ ] **NO false verification**: Scan for "実行すると〜となりました" "確認しました" "検証した" → Must use conditional ("はずです", "と考えられます")
- [ ] **NO unverified references**: Scan for "issue #[number]" "PR #[number]" → Must use generic refs or omit
- [ ] **Conditional language present**: Check that technical behavior uses "〜はずです" "〜と考えられます" (not definitive past tense)
- [ ] **Generic project framing**: "このような場面では" not "筆者のプロジェクトでは"
- [ ] **Uncertainty acknowledged**: Include 1-2 "まだ試していない" "推測ですが" "見守りたい" phrases

### 🚨 CRITICAL (Publication Blockers)
- [ ] **Article length: 180-230 lines** (run `wc -l article.md` to verify; 175-179 acceptable but risky)
- [ ] **Section count: 6-7 H2 sections MAXIMUM** (count with `grep '^## ' article.md | wc -l`; 8-9+ = encyclopedic, CAPS AT 8.5)
- [ ] **ZERO sentence-ending contracted forms** (scan: てる。てた。てます。てない。てなかった。)
- [ ] **ZERO paragraph-initial "で、"** (scan: starts with "で、")
- [ ] **ZERO colons in prose before code/lists** (scan entire article for ：at line end; check next line is - or ```)
  * ESPECIALLY check for standalone labels: "動いたもの：" "注意点：" "結果："
  * These must be section headers (## Label) or full sentences (Labelは以下の通りです。)
- [ ] Valid frontmatter with all fields
- [ ] **です/ます count: 40-70 absolute** (count です。+ ます。manually; verify twice)
  * <40 = max 8.0/10 (insufficient formality)
  * 71-75 = -0.3 to -0.5 deduction (excessive formality)
  * 76+ = -0.5 to -0.8 deduction (over-formalized)
- [ ] **です/ます density: 25-35% optimal** (calculate: count ÷ lines × 100)
  * >38% = too formal (stiff tone, -0.3 to -0.5 deduction)
  * <22% = too casual (insufficient formality)
- [ ] Main declarative sentences use です/ます (70-80% of main sentences)
- [ ] **Mathematical calculations verified** (counts, combinations, arithmetic - double-check ALL numbers)

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

### Pattern 4: Meta-Commentary & Personal Projects (⚠️ SEASON 4 UPDATED)

**Reactions**: "個人的にはちょっとびっくりしました" "残念ながら..." "推測ですが" "ここからが本題です" (2-4 per article)

**🆕 SEASON 4 RELIABILITY-AWARE APPROACH:**

**Projects - TWO RELIABLE PATTERNS:**

1. **Generic/Hypothetical Motivation** (RELIABLE, PREFERRED):
   - ✅ "このような問題は実際のプロジェクトで遭遇することがある"
   - ✅ "TypeScript + Expressのようなスタックでは、こういった課題が出てくる"
   - ✅ "ルーティングライブラリを作る際、この型が役立つはずです"
   - ✅ "フロントエンド開発では、〜が問題になることが多い"
   - Frame as general observations about common scenarios, not specific personal experiences
   - **Maintains technical engagement without fabrication**
   - **Scoring**: 0.8-1.0/1.0 (authentic technical voice through curiosity)

2. **Vague Personal Thread** (RELIABLE, ACCEPTABLE):
   - ✅ "筆者も最近、こういった課題を考える機会があった"
   - ✅ "似たような状況について考えたことがある"
   - ✅ "型安全性の向上について検討していた"
   - Maintains author presence through vague motivation without fabricated specifics
   - Thread can recur 2-3 times across article to build persona
   - **Scoring**: 0.7-1.0/1.0 (author presence maintained honestly)

**❌ SEASON 3 PATTERNS NOW FORBIDDEN (Reliability violations):**
- ❌ "筆者は自分のプロジェクト（TypeScript + Express + PostgreSQL構成）で試したところ..." (fabricated specific experience)
- ❌ "筆者は[nitrogql]の設定ファイル読み込みで..." (fabricated named project)
- ❌ "実務で使っていた構成で問題に遭遇した" (fabricated work experience)

**The Challenge:** uhyo's voice includes personal projects, but AI can't have real experiences.

**The Solution:** Express personal curiosity and technical motivation through:
- Generic use cases ("このような場面では")
- Hypothetical scenarios ("〜の構成であれば")
- Vague curiosity ("こういった課題を考える機会があった")
- **NOT** fabricated specific experiences

**Multiple Generic/Vague References**:
- 2-3 generic references throughout = Strong authentic voice (1.0/1.0)
- 2-3 vague personal threads = Acceptable author presence (0.8/1.0)
- Mix of both = Ideal for Season 4 (1.0/1.0)

**Scoring Impact:**
- Generic/Hypothetical approach: Maintains uhyo investigative voice (0.8-1.0/1.0)
- Vague thread approach: Shows author presence honestly (0.7-1.0/1.0)
- Fabricated specifics: Reliability violation (-1.0 to -2.0 reliability points, publication risk)

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

## 📊 SUCCESS PATTERNS (Iterations 5-12 Learning)

**Iteration 5 (9.3/10)**: 51 endings, 231 lines, all 10 uhyo patterns ✅
**Iteration 6 (8.0/10)**: 32 endings, 151 lines, all 10 uhyo patterns but CAPPED by です/ます ❌
**Iteration 7 (9.5/10)**: 55 endings, 218 lines, all 10 uhyo patterns ✅✅ **← GOLD STANDARD**
**Iteration 10 (9.5/10)**: 50 endings, 218 lines, 9.5/10 author voice, 5 sections, 0 violations ✅✅ **← PROVEN MASTERY**
**Iteration 12 (8.6/10)**: 74 endings, 178 lines, 10/10 author voice but TOO FORMAL (41.6% density) ❌

**Key Insights**:
- Perfect author voice (10/10) is NOT enough. Must also meet です/ます requirements.
- **Iteration 6**: Too few endings (32) = 8.0/10 cap
- **Iteration 12**: Too many endings (74) AND too high density (41.6%) = -0.3 to -0.5 deduction
- **Sweet spot**: 50-60 endings in 190-220 lines = 26-31% density

**Proven 9.0+ Formula** (validated by Iterations 7 & 10):
1. Article length: 180-230 lines (sweet spot) - Iteration 7: 218 lines
2. です/ます: 50-60 absolute count optimal (40-70 acceptable range) - Iteration 7: 55 endings
3. **です/ます density: 25-35% (critical for natural tone)** - Iteration 7: 25.2% ✅, Iteration 12: 41.6% ❌
4. Author voice: 8+ uhyo patterns (see Section 👤) - Iteration 7: 10/10 patterns
5. Zero forbidden patterns (see Section ⚠️) - Iteration 7: 0 violations
6. Ecosystem context: 1-2 GitHub issues/PRs or community refs - Iteration 7: GitHub issue #4721
7. **Technical accuracy: Verify all mathematical claims** - Iteration 12: Math error cost -0.5

**Iteration 7 & 10 Achievement**: Both achieved 9.5/10, proving the formula works consistently. Iteration 10 demonstrated **internalized mastery** by recovering from Iterations 8-9 regressions.

---

**Last updated:** Season 4 Launch (Reliability requirements added)
**Version:** 3.0 (Season 4: Reliable human-like articles - honesty + uhyo voice)
**Line count:** ~580 lines (expanded with Season 4 reliability rules)
