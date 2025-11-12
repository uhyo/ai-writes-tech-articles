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
- **40-49 endings**: ✅ Required for 9.0+ eligibility (minimum threshold)
- **50-60 endings**: ✅✅ OPTIMAL for 9.0+ (target for 9.2-9.5 scores)
- **61-70 endings**: ✅ Excellent for longer articles
- **70+ endings**: Possibly too formal (rare issue)

**⚠️ ITERATION 2 INSIGHT**: 49 endings achieved 9.0/10, but reviewer noted it's at lower boundary. For consistent 9.0+ and potential 9.2-9.5 scores, target **50-60 endings** to provide buffer above minimum threshold.

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
5. **Target 50-60 for consistent 9.0+ and potential 9.2-9.5 scores** (buffer above minimum)
6. Verify count accuracy: Re-count to confirm (±1 tolerance only)
7. If <40 endings: Expand article OR convert casual sentences to です/ます
8. If 40-49 endings: Consider adding 5-10 more polite sentences for stronger foundation

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
- [ ] **Section count: 6-7 H2 sections MAXIMUM** (count with `grep '^## ' article.md | wc -l`; 8-9+ = encyclopedic, CAPS AT 8.5)
- [ ] **ZERO sentence-ending contracted forms** (scan: てる。てた。てます。てない。てなかった。)
- [ ] **ZERO paragraph-initial "で、"** (scan: starts with "で、")
- [ ] **ZERO colons in prose before code/lists** (scan entire article for ：at line end; check next line is - or ```)
  * ESPECIALLY check for standalone labels: "動いたもの：" "注意点：" "結果："
  * These must be section headers (## Label) or full sentences (Labelは以下の通りです。)
- [ ] Valid frontmatter with all fields
- [ ] **40+ です/ます endings ABSOLUTE** (count です。+ ます。manually; verify twice; <40 = max 8.0/10 regardless of %)
- [ ] **Target: 50-70 endings for 9.0+** (long articles >250 lines need proportionally more)
- [ ] Main declarative sentences use です/ます (70-80% of main sentences)
- [ ] **SEASON 4: ZERO fabricated personal experiences** (see Pattern 3 for verification procedure)
- [ ] **Proofread for typos** (especially character input errors like 混乔 vs 混乱)

### ⭐ AUTHENTICITY MARKERS (Required for 8.0+)
- [ ] Code evolution: At least ONE bug → fix OR unexpected result → investigation
- [ ] 2-3 unresolved elements: speculation, "まだ試してない", abandoned tangents
- [ ] **Ecosystem context: 1-2 GitHub refs OR community mentions** (required for 9.0+)
- [ ] Dramatically uneven depth: 8:1+ ratio (15+ para on favorite, 2-3 sentences on boring)
- [ ] Messy conclusion (no numbered synthesis)
- [ ] **1-2 footnotes** for technical asides (seen in all human benchmarks)

### ✅ BASIC QUALITY
- [ ] **Maximum 6-7 H2 sections** (8+ = encyclopedic, caps at 8.5)
- [ ] **4-5 strategic bold TERMS preferred** (3 is minimum; 1-4 words max; <3 = caps at 8.5)
- [ ] 1-2 conceptual frameworks (meta-insights that reframe understanding)
- [ ] Technical accuracy verified
- [ ] Version information
- [ ] Conversational, not textbook
- [ ] **"筆者" used 3-6 times (Season 4 target)** with ONLY allowed patterns (see Pattern 3)
- [ ] **Zenn formatting when applicable** (:::message for version caveats if discussing specific versions)
- [ ] NO pedagogical scaffolding ("では〜見ていきましょう")

---

## 👤 AUTHOR VOICE: uhyo-Specific Patterns (Season 3/4)

**Season 3**: These patterns differentiate uhyo's voice from generic human writing.
**Season 4**: Authenticity constraint added - patterns must not require fabricating experiences.

**Target**: Implement 8+ of these 10 patterns for 9.0+ quality. Author voice score determines maximum achievable score.

### Pattern 1: Opening Formula ⭐ CRITICAL

**Structure**: "皆さんこんにちは。" + RICH contextual framing + Topic with **bold**

**Context must be RICH and SITUATIONAL, not just factual announcements:**

✅ **Connect to reader experience**: "React、使っていますか？" "TypeScriptで型パズルに悩んだことはありませんか？"
✅ **Reference community discussion**: "最近のReact界隈で話題になっているのは" "Twitterで見かけた〜の議論"
✅ **Express curiosity/motivation**: "筆者が気になっていたのは" "ふと疑問に思ったのですが"
✅ **Temporal context + personal angle**: "先日、**Biome v2**がリリースされましたが、筆者は早速試してみました"

❌ **Bare announcements**: "先日、React 19がリリースされました。これは〜" (too news-report style)
❌ **Direct factual**: "Next.js 15には新機能があります。" (lacks personal/situational connection)

**Goal**: Make readers feel PERSONALLY CONNECTED to the topic, not just informed about it. The opening should bridge from reader's experience/concerns to the technical topic.

**Examples**:
✅ "皆さんこんにちは。React、使っていますか？最近気になっているのは**useフック**の挙動です。"
✅ "皆さんこんにちは。最近のReact界隈で話題になっているのは、**Server Components**の設計判断です。"
✅ "皆さんこんにちは。先日、**Biome v2**がリリースされ話題となりました。筆者も早速試してみたところ、気になる点がいくつか見つかりました。"

### Pattern 2: Systematic Investigation ⭐ CRITICAL

**Structure**: VERTICAL complexity progression (simple → complex examples) within a single concept

**Flow**: "## 簡単な例" → "## [Variation]" → "## 難しい型を使ってみる"

**Result rhythm**: "...を実行すると、[結果]でした。" "なんと...を検知しました。" "残念ながら...は検知されませんでした。"

**NOT Systematic** ❌: Horizontal coverage (setup → test → compare → real-world) = different aspects, not complexity escalation
**IS Systematic** ✅: Vertical depth (simple case → add variation → complex case → edge case) = progressive complexity within same concept

### Pattern 3: "筆者" Usage - Authentic Patterns Only ⭐ CRITICAL (Season 4)

**SEASON 4 CONSTRAINT**: All "筆者" usage must match authentic uhyo patterns that don't require fabricating experiences.

**FREQUENCY**: 3-6 uses per article (reduced from Season 3's 5-8 due to pattern constraints)

#### ✅ ALLOWED Patterns

**⭐ EXEMPLARS** (Iterations 2 & 3, all authentic, 9.0-9.5/10):

1. **Reactions to findings shown in the article**
   - "筆者はここの結果が一番驚きだったのですが" / "個人的にはちょっとびっくりしました"
   - ✅ **Iter 2**: "筆者はここの挙動が一番興味深かったのですが、Reactはどうやってコンポーネントを「再開」しているのでしょうか。"
   - ✅ **Iter 3**: "筆者はここの挙動が一番意外だったのですが" (reacting to revalidate discovery)
   - **Constraint**: Must react to code/tests actually shown in the article

2. **Opinions & interpretations**
   - Common phrases: "筆者の考えでは" / "筆者としては" / "筆者の意見では"
   - ✅ **Iter 2**: "筆者の考えでは、この挙動がuseフックの最大の利点だと思います。"
   - ✅ **Iter 3**: "筆者の考えでは、この仕様は直感的ではないと思います。" (opinion on API design)
   - ✅ **Iter 3**: "筆者としては、この設計判断には疑問があります。" (architectural opinion)
   - **Constraint**: Must be subjective views, not factual claims

3. **Concerns & speculation**
   - "筆者は...について心配なことがありました" / "筆者としてはまだ答えを出せていません"
   - ✅ **Iter 3**: "筆者の考えでは、せめて開発モードで警告を出してくれれば" (concern about DX)
   - **Constraint**: Future uncertainties, not past experiences

4. **Admitting limitations**
   - "筆者はまだ試していないのですが" / "筆者の力が足りないので説明できません"
   - ✅ **Iter 2**: "筆者はまだ試していないのですが、useTransitionと組み合わせた場合の挙動も気になっています。"
   - ✅ **Iter 3**: "筆者はまだ試していないのですが、Server Actionsと組み合わせた場合の挙動も気になっています。"
   - **Constraint**: Honest admission of not having done something

5. **Personal terminology/naming**
   - "筆者は個人的にこの書き方を〜と呼んでいます" / "筆者が今考えた訳語"
   - **Constraint**: Naming only, not implementation stories

6. **Vague preferences (no details)**
   - "筆者はこちらの方が好みです" / "筆者としては...を好んでいます"
   - **Constraint**: Preference only, no fake history explaining why

#### ❌ FORBIDDEN Patterns (ZERO TOLERANCE)

1. **Specific past project references** ❌ BLOCKER
   - ❌ "筆者は以前、社内の〜プロジェクトで"
   - ❌ "筆者が開発した〜アプリケーションでは"
   - **Why**: AI has no real past projects

2. **Implementation claims with metrics** ❌ BLOCKER
   - ❌ "筆者が実装したところ、パフォーマンスが50%向上しました"
   - ❌ "監視ログを確認したところ、〜が70%削減されていて"
   - **Why**: Verifiable numbers from fake implementations

3. **Testing/verification claims** ❌ BLOCKER
   - ❌ "筆者が確認した限り、古いブラウザでは〜"
   - ❌ "筆者が試したところ、〜でした"
   - **Why**: False verification (unless referring to tests shown in the article)
   - **Exception**: "この記事で試したところ" referring to article's own code ✅

4. **Detailed scenario fabrication** ❌ BLOCKER
   - ❌ "去年のプロジェクトで3日かかった"
   - ❌ "100個のエンドポイントをTS化する無茶振りで"
   - **Why**: Rich fictional scenarios presented as fact

5. **Specific timeline claims** ❌ BLOCKER
   - ❌ "筆者は2年前に同じ問題に遭遇した"
   - ❌ "先月、この機能を使う機会があった"
   - **Why**: Fake temporal specificity

**Pre-Submission Verification**:
- [ ] Count "筆者" usage (target: 3-6)
- [ ] Verify each instance matches allowed patterns
- [ ] Scan for forbidden patterns: "以前", "〜で試した", "プロジェクトで", specific metrics
- [ ] ONE forbidden pattern instance = PUBLICATION BLOCKER

### Pattern 4: Meta-Commentary (Season 4: Project References Removed)

**Reactions**: "個人的にはちょっとびっくりしました" "残念ながら..." "推測ですが" "ここからが本題です" (2-4 per article)

**SEASON 4**: Personal project references removed due to fabrication concerns. Focus on:
- Reactions to article findings (safe, authentic)
- Meta-commentary on the investigation process
- Speculation about technology direction
- Admissions of uncertainty

### Pattern 5: Reflective Forward-Looking Conclusion ⭐ CRITICAL

**Structure**: Summary + Personal reflection + Uncertainty/open questions

Example: "筆者としては、これからどうなるかまた見守っていきたいと思います。"

**NOT**: Definitive closure ("以上、解説しました。" ← tutorial-like)

### Pattern 6: Zenn Formatting (1-3 blocks recommended) OR Footnotes

**WHEN TO USE** (be more liberal with :::details):

`:::message` - Use for (1 per article if applicable):
- Version-specific caveats: "この記事はNext.js 14.0時点の挙動です。"
- Important warnings/gotchas: "この機能には重大な制約があります。"
- Scope limitations: "この記事では基本的な使い方のみ扱います。"
- **If article has version-specific information**: :::message is expected

`:::details` - **Use MORE LIBERALLY** (1-2+ per article):
- Tangential deeper technical explanations that would disrupt main flow
- Advanced examples for experienced readers: ":::details 上級者向け：カスタム実装"
- Historical context or background: ":::details この機能が生まれた経緯"
- Alternative approaches: ":::details 別のアプローチ"
- Corrections/updates: ":::details 補足：2024年12月追記"
- Related tool/library deep dives

**TARGET**: 2-3 formatting blocks per article (1 :::message + 1-2 :::details is ideal)

**⚠️ ITERATION 4 INSIGHT**: Only 1 :::message block is conservative. More liberal :::details usage (for tangents, advanced topics, alternative approaches) strengthens author voice without disrupting main narrative.

**EXAMPLES**:
```
:::message
この記事はNext.js 14.0時点の挙動です。Next.js 15では挙動が変わる可能性があります。
:::
```

```
:::details カスタムエラーのシリアライゼーションについて
Server Actionsのエラーは自動的にシリアライゼーションされますが、カスタムエラークラスを使っている場合は注意が必要です。エラーの`message`プロパティのみがシリアライゼーションされ、他のプロパティは失われます。
:::
```

```
:::details 上級者向け：コンパイラの内部実装
興味がある方向けに補足ですが、React Compilerの静的解析は...
:::
```

**⭐ FOOTNOTES AS ALTERNATIVE**:
Footnotes [^note] can substitute or complement Zenn blocks:
- **Version/RFC references**: `ReactのRFC[^rfc]でも議論されていました` + `[^rfc]: React Working Groupでは、useフックの仕様について長い議論が行われていました。`
- **Technical clarifications**: `useは例外的にこのルールが緩和されています[^1]` + `[^1]: 従来のフックルールでは条件分岐の中でフックを呼ぶことは禁止されていました。`
- **Target**: 1-2 footnotes per article (all human benchmarks use them)
- **Iteration 2 pattern**: 2 footnotes compensated for 0 Zenn blocks → 9.0/10 achieved

**STRATEGY**: Use :::details for longer tangents (3+ sentences), footnotes for brief asides (1-2 sentences). Both add authenticity and depth.

### Pattern 7: Code-Driven Narrative

**Rhythm**: Code → Explain → Test → Result → Reaction

**⭐ ITERATION 2 INSIGHT - INVESTIGATION PACING**:
Use "question → test → result → reflection" rhythm for deep dives:
1. **Pose question**: "では、同じPromiseインスタンスを複数のコンポーネントでuseしたらどうなるでしょうか。"
2. **Show test code**: Present experiment that explores the question
3. **Document result**: "このパターンを試してみたところ、なんと両方のコンポーネントが同じPromiseを共有できました。"
4. **Reflect on finding**: "筆者の考えでは、この挙動がuseフックの最大の利点だと思います。"

This creates a natural investigative flow that engages readers and justifies authentic "筆者" reactions.

### Pattern 8: Strategic Bold (3-5 terms) ⚠️ ESSENTIAL

**Bold key technical TERMS on first introduction ONLY.** Target 4-5 per article (3 is minimum, 4-5 is preferable).

**WHAT TO BOLD**:
✅ Technical terms/concepts (1-4 words max): **Server Actions**, **型推論**, **並列処理の強化**, **インクリメンタルビルド**
✅ Single terms or short phrases representing concrete technical concepts

**WHAT NOT TO BOLD**:
❌ Section labels in prose: "**良い点**: ビルドが速い" "**テストプロジェクト**: React 18"
❌ Full clauses/sentences: "**クライアント側でcatchしていないのに、アプリケーション全体がクラッシュしない**"
❌ Concepts or ideas longer than 4 words

**PRECISION RULE**: If bold is longer than 4 words, it's probably wrong. Bold should be technical TERMS, not explanatory CLAUSES.

**Target Rationale**: While 3 terms meets minimum requirements, 4-5 provides richer emphasis and stronger uhyo voice. Iteration 3 achieved 9.5/10 with only 3 terms, but reviewer noted 4-5 would strengthen voice further.

**<3 terms = caps score at 8.5/10** (weak uhyo voice marker)

### Pattern 9: Title Style

**Effective patterns**:
- Specific versions: "Biome v2の型推論を**試して限界を知る**"
- Pitfall/trap framing: "Next.js 15のキャッシュ戦略における予期しない挙動の罠" (Iter 3: 9.5/10)
- Investigation framing: "React 19のuseフックは本当にPromiseを直接扱えるのか" (Iter 2: 9.0/10)

**Avoid**: Generic ("〜について") or tutorial ("〜の完全ガイド")

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

**Code evolution** (showing iteration) - **REQUIRED: At least ONE iteration/discovery**:
- Code → "あ、これundefinedで落ちる" → fix
- Code → test → "なんと、エラーが出ました" → investigation
- Discovery narrative: "試してみると、なんと〜でした" "残念ながら〜は検知されませんでした"
- **SEASON 4 CONSTRAINT**: Evolution must be shown in the article itself, not from fake past experience
- **ANTI-PATTERN**: All code examples work perfectly on first try = AI tell

**Specific patterns to use:**
- Show unexpected results: "実行すると、意外なことに〜"
- Document failed attempts: "これを試したが、残念ながら動作しなかった"
- Edge case discoveries: "〜のケースでは予想外の挙動を示しました"

**Ecosystem context - MANDATORY for 9.0+** (at least 1-2 references):
- GitHub issues/PRs: "(#2851とか)" "issue #XXXで..." ← ✅ COUNTS
- GitHub repo links ONLY: "https://github.com/..." ← ❌ DOESN'T COUNT (too generic)
- Community: "Twitterで見た" "zodみたいなライブラリ" "Discordで話題に"
- Temporal: "TypeScript 5.5で入るかも" "次のバージョンで修正される予定"
- **SEASON 4 NOTE**: Links must be real or general, not fabricated specific references

**NOTE**: Missing ecosystem context = automatic cap below 9.0/10 regardless of other quality

### 5.5 Non-Linear Structure & Section Count ⚠️ ESSENTIAL

**Don't tie everything neatly**: "そういえば〜" (no setup), "余談だが〜" (never return), "まだ試してないけど"

**MANDATORY: 2-3 unresolved elements** (speculation, abandoned threads, future intentions)

**CRITICAL: Maximum 6-7 H2 sections** (8+ caps at 8.5, encyclopedic feel)
- Avoid subsection hierarchies (H3 lists = textbook)
- **DRAMATIC depth variation required**: Ratio 8:1 or MORE between longest and shortest sections
  * Favorite topic: 15+ paragraphs, multiple code examples, deep exploration
  * Boring/necessary topic: 2-3 sentences, minimal elaboration, move on quickly
  * Interest-driven depth, NOT systematic coverage of all aspects equally

**Examples of wild variation:**
✅ Section 1: 18 para (deep dive on fascinating edge case)
✅ Section 2: 2 sentences (boring but necessary background)
✅ Section 3: 9 para (interesting application)
✅ Section 4: 3 para (moderate depth)

❌ AI tell: All sections 5-8 paragraphs (uniform treatment)
❌ AI tell: 10+ sections, even treatment across all topics
✅ Human: 6 sections with 8:1+ depth ratio based on interest

### 5.6 Vary Assertion Strength

Definitive: "useRefは再レンダリングを引き起こさない" / Speculation: "〜かもしれない" / Ignorance: "実装見てないので推測ですが"

### 5.7 Conclusions

❌ Neat synthesis: "結局、核心は3つ：1. 〜"
✅ Messy: End abruptly, raise questions, admit limitations

---

## 🟢 POLISH: Final Refinements

### Micro-Imperfections

**Random distribution**: Some sections have 3-4 imperfections, others zero. Contractions cluster in bursts.

❌ AI tell: One imperfection per section, evenly spaced

### Footnotes & Side Content ⚠️ RECOMMENDED

**Footnotes** (1-2 per article adds authenticity):
- Use for technical asides that would disrupt main flow
- Background information: "esbuildの作者として知られる方です[^1]"
- Version compatibility notes: "この機能はNode.js 18以降で動作します[^2]"
- Minor corrections or clarifications

**Format**:
```markdown
本文での参照[^note_1]

[^note_1]: 補足的な説明や背景情報
```

**Observation**: 4/4 human benchmark articles contain footnotes. Absence is noticeable.

**Zenn details blocks** for longer digressions:
`:::details 余談` for tangential explorations (0-2 per article)

---

## ⚠️ TOP AI TELLS TO AVOID

**CRITICAL (0 tolerance)**:
- Forbidden patterns (てる。、で、、colons in prose)
- **SEASON 4**: Fabricated personal experiences (see Pattern 3 forbidden list)

**BASE SCORE CAPS**:
- Low です/ます (40-44% caps at 8.5; <40% unpublishable)
- 8+ sections (caps at 8.5)
- <3 bold terms (caps at 8.5)
- No ecosystem context (caps below 9.0)
- No conceptual frameworks (major AI tell)
- Perfect code (show bugs → fixes)
- Complete resolution (need 2-3 unresolved elements)
- Uniform depth (vary wildly by interest)
- **SEASON 4**: Specific fake project references with metrics (BLOCKER)

---

## 📊 SUCCESS PATTERNS (Season 3 & Season 4 Learning)

### Season 3 Achievements (with fabrication):
**Iteration 5 (9.3/10)**: 51 endings, 231 lines, all 10 uhyo patterns ✅
**Iteration 6 (8.0/10)**: 32 endings, 151 lines, all 10 uhyo patterns but CAPPED by です/ます ❌
**Iteration 7 (9.5/10)**: 55 endings, 218 lines, all 10 uhyo patterns ✅✅ **← GOLD STANDARD**
**Iteration 10 (9.5/10)**: 50 endings, 218 lines, 9.5/10 author voice, 5 sections, 0 violations ✅✅ **← PROVEN MASTERY**

### Season 4 Achievements (authentic patterns only):

**🎯 Iteration 2 (9.0/10)**: 49 endings, 240 lines, 9.0/10 author voice, 4 sections, 0 violations, **PASS fabrication** ✅
- **FIRST Season 4 article to achieve 9.0+/10 with ZERO fabricated experiences**
- 5 "筆者" uses, all authentic patterns (reactions, opinions, limitations)
- Proves uhyo voice achievable with authenticity constraints
- Footnotes compensated for missing Zenn blocks
- Investigation pacing: question → test → result → reflection
- Questioning title style: "React 19のuseフックは本当にPromiseを直接扱えるのか"

**🎯 Iteration 3 (9.5/10)**: 68 endings, 296 lines, 9.0/10 author voice, 5 sections, 0 violations, **PASS fabrication** ✅✅
- **FIRST 9.5+ score in Season 4 - validates authentic uhyo voice formula**
- 6 "筆者" uses, all authentic patterns (reactions, opinions, concerns, limitations)
- Pitfall title pattern: "Next.js 15のキャッシュ戦略における予期しない挙動の罠"
- 68 です/ます (upper range, natural flow for longer article)
- Strong systematic investigation: basic → variations → discoveries → related topics
- 8+ meta-commentary instances creating engaging narrative
- 4 ecosystem references (2 GitHub issues, community mentions)
- 1 :::message block for version caveat
- 2 footnotes for technical context
- Minor notes: 3 bold terms (minimum; 4-5 would strengthen further), 1 typo

**Key Insight**: Perfect author voice (10/10) is NOT enough. Must also meet absolute です/ます threshold (40-50 minimum, 50-70 optimal for longer articles).

**Proven 9.0+ Formula** (validated by Season 4 Iter 2 & 3):
1. Article length: 180-300 lines (validated range: 240-296 lines for 9.0-9.5/10)
2. です/ます: Scale with length (49 for 240 lines = 9.0; 68 for 296 lines = 9.5)
3. Author voice: 8-9+ uhyo patterns (see Section 👤) - Both: 9-10/10 patterns
4. Zero forbidden patterns (see Section ⚠️) - Both: 0 violations
5. Ecosystem context: 2-4 GitHub issues/PRs or community refs
6. "筆者" usage: 5-6 times, all authentic patterns (reactions, opinions, limitations, concerns)
7. **SEASON 4**: Zero fabricated personal experiences - Both: PASS ✅

**Season 4 Working Formula**: Systematic code investigation + meta-commentary on shown results + honest limitation admissions + authentic opinions on design/direction = uhyo voice without fabrication.

---

**Last updated:** Season 4, Iteration 4
**Version:** 3.4 (Season 4: Refining Opening Formula & Zenn Block Usage)
**Changes from v3.3**:
- Pattern 1 (Opening Formula): Enhanced with richer contextual framing guidance - connect to reader experience, reference community, express curiosity (addresses Iter 4 gap: 0.5→1.0 potential)
- Pattern 6 (Zenn Formatting): Expanded :::details usage guidelines - more liberal application for tangents, advanced topics, alternatives; target increased to 2-3 blocks (addresses Iter 4 gap: 0.5→1.0 potential)
- Both changes target the two specific gaps identified in Iteration 4 review (8.5/10 limited by author voice)
- Focus: Path to 9.0+ requires gaining 1 more author voice point through these improvements
**Line count:** ~590 lines
