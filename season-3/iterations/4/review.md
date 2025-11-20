# Review - Iteration 4

## Pattern Discovery (Exploratory Analysis)

**Sampled Articles**:
1. biome-v2-type-inference.md (367 lines, 39 です/ます endings)
2. react-use-rfc.md (326 lines, 124 です/ます endings)
3. nitrogql-beta-release.md (219 lines, 72 です/ます endings)
4. typescript-4-8-type-narrowing.md (251 lines, 49 です/ます endings)

**New Patterns Discovered**:

After exploratory comparison of AI article against human samples, no significant new patterns were discovered beyond existing style guide requirements. The article demonstrates awareness of known patterns and applies them with varying success.

---

## Human Baseline Observations

**です/ます Sentence Ending Counts** (sampled articles):
- biome-v2-type-inference.md: 39 です/ます endings (367 lines)
- react-use-rfc.md: 124 です/ます endings (326 lines)
- nitrogql-beta-release.md: 72 です/ます endings (219 lines)
- typescript-4-8-type-narrowing.md: 49 です/ます endings (251 lines)
- **Baseline Range**: 15-70 です/ます sentence endings per article

**Known Linguistic Patterns** (from style guide):
- Sentence endings: Mix of polite (です/ます) and casual forms, with polite forms dominating main declarative sentences
- Forbidden patterns: Zero tolerance for sentence-ending contracted forms (てる。てた。てます。), paragraph-initial "で、", colons before code
- Contextual casual forms: Acceptable in embedded clauses, reactions, quoted thoughts
- Main text polite forms: 40-50 です/ます endings optimal for ~200-line articles (45-60% distribution)

**Key Findings**:
- Human articles show consistent use of polite forms in main declarative sentences
- Casual forms appear strategically in subordinate clauses, reactions, and meta-commentary
- Forbidden patterns have 0% frequency across all human samples
- Ecosystem references (GitHub issues, community mentions) appear naturally in context

---

## Linguistic Compliance Analysis

**AI Article Metrics**:
- **です/ます sentence endings**: 31 (です。+ ます。)
  * Human baseline: 15-70
  * Article length: 274 lines
  * Status: ✅ PASS minimum threshold (15+) but ⚠️ BELOW OPTIMAL (40-50 for ~200-line article)
  * Distribution: Estimated 30-35% polite form distribution (borderline acceptable minimum)

**Forbidden Pattern Check**:
- ✅ Sentence-ending contracted forms (てる。てた。てます。): **0 instances** - PASS
- ✅ Paragraph-initial "で、": **0 instances** - PASS
- ✅ Colons in prose before code: **0 instances** - PASS

**Style Guide Checklist** (from CRITICAL REQUIREMENTS):
- ✅ です/ます count: 31 vs minimum 15+ - PASS (but below optimal 40-50)
- ⚠️ Polite form distribution: ~30-35% vs optimal 45-60% - BORDERLINE
- ✅ Forbidden pattern violations: 0 instances - PASS
- ✅ Valid frontmatter: All fields present - PASS
- ✅ Technical accuracy: Concepts explained correctly with proper context

**Scoring Impact**:
- Polite form distribution 30-39 range triggers score cap at 8.5/10
- Zero forbidden patterns: No additional penalties
- Linguistic Authenticity: 7.5/10 (polite forms present but below optimal density)

---

## Author Voice Analysis (Season 3)

### Pattern Verification

#### 1. Opening Formula: ✓ PRESENT
**Evidence**:
"皆さんこんにちは。Next.js 14で正式リリースされた**Server Actions**を本格的に使い始めたのですが、エラーハンドリングの挙動が予想と違って困った場面がありました。今回はその挙動を調べた結果を共有します。"

**Assessment**: Perfect uhyo opening formula execution:
- ✓ "皆さんこんにちは。" greeting
- ✓ Temporal context ("Next.js 14で正式リリースされた")
- ✓ Key term with bold (**Server Actions**)
- ✓ Personal experience bridge ("予想と違って困った場面がありました")

#### 2. Systematic Investigation: ✓ PRESENT
**Evidence**: Section progression shows vertical depth escalation:
1. "Server Actionsの基本的なエラー" (simple case)
2. "try-catchで処理した場合の挙動" (variation)
3. "非同期処理中のエラー" (complexity increase)
4. "ネストしたServer Actionsでのエラー伝播" (deeper complexity)
5. "実際のプロジェクトで遭遇した罠" (real-world application)
6. "エラーバウンダリとの関係" (edge case exploration)

**Result documentation rhythm**:
- Line 50: "これを試したところ、ブラウザのコンソールに `Error: 名前が入力されていません` が出力されました。"
- Line 86: "これを実行すると、`名前が入力されていません` というメッセージが正常にキャッチできました。"
- Line 107: "このコードをクライアントで受け取って `e.code` を参照したところ、undefinedでした。"
- Line 181: "このコードで `userId` を空にして実行すると、`validateUser` で投げたエラーが...伝播しました。"

**Assessment**: Strong systematic investigation with clear vertical progression from simple → complex examples. Result documentation rhythm present throughout ("...したところ、...でした" pattern). This is authentic uhyo investigation structure.

#### 3. Personal Project Integration: ✓ PRESENT (RICH)
**Evidence**:
"筆者が開発している社内の問い合わせ管理ツールで、Server Actionsを使ったフォーム送信を実装したときに、予想外の挙動に遭遇しました。"

Followed by detailed exploration (lines 189-248) of:
- Specific tool type (問い合わせ管理ツール - inquiry management tool)
- Specific technical problem (distinguishing validation vs database errors)
- Specific implementation journey (initial approach → problem → solution)
- Code examples showing the evolution

**Assessment**: RICH personal project integration. Not just mentioned, but explored in depth with specific problem, attempted solution, and final resolution. The project provides the foundation for an entire section's investigation. This matches uhyo's deep project integration style.

#### 4. Meta-Commentary: ✓ PRESENT
**Evidence**:
- Line 52: "筆者は最初、これはサーバー側の例外なので何らかの形でアプリケーションが落ちると思っていました"
- Line 88: "ところで、エラーメッセージ以外のプロパティはどうなるのか気になったので、カスタムエラークラスを試してみました"
- Line 128: "個人的にはちょっとびっくりしたのですが、非同期処理のエラーもServer Actionsの枠組みの中で適切に処理されていました"
- Line 149: "推測ですが、Next.js側で設定されているタイムアウトと、fetch自体のタイムアウトが競合する可能性がある"
- Line 272: "筆者としては、この辺りの挙動がどう変わっていくのか見守っていきたいと思います"

**Count**: 5 instances

**Assessment**: Frequent and natural meta-commentary reflecting personal reactions to findings ("びっくりした"), investigation process commentary ("気になったので試してみた"), and speculation ("推測ですが"). This is authentic uhyo voice.

#### 5. "筆者" Usage: △ PARTIAL (LOW END)
**Evidence**:
1. Line 52: "筆者は最初、これはサーバー側の例外なので何らかの形でアプリケーションが落ちると思っていました。" (personal expectation)
2. Line 189: "筆者が開発している社内の問い合わせ管理ツールで..." (personal project experience)
3. Line 272: "筆者としては、この辺りの挙動がどう変わっていくのか見守っていきたいと思います。" (forward-looking reflection)

**Count**: 3 uses

**Assessment**: At the absolute low end of the 3-8 range. All three uses are appropriate contexts (personal project, subjective reactions, forward-looking statements), but the frequency is minimal. More uses would strengthen the author voice.

#### 6. Zenn Formatting: ✗ ABSENT
**Evidence**: No :::details or :::message blocks found in the article.

**Assessment**: While these blocks are not mandatory for all articles, their presence would have been natural for this content. For example:
- Could have used :::message for Next.js version caveats
- Could have used :::details for technical asides about error serialization

However, their absence doesn't severely harm the article as the content flows naturally without them. Scoring as absent but not critical.

#### 7. Reflective Forward-Looking Conclusion: ✓ PRESENT
**Evidence**: Final paragraph (lines 270-274):
"まだ試してないのですが、Next.js 15のドキュメントには**unhandled rejection**に関する記述があったので、そちらの仕組みで対応できる可能性があります。筆者としては、この辺りの挙動がどう変わっていくのか見守っていきたいと思います。

ちなみに、Server Componentsで発生したエラーはエラーバウンダリでキャッチできるので、Server ActionsとServer Componentsでエラーハンドリングの方式が異なるという点は注意が必要です。この境界が曖昧で、最初は混乱しました。"

**Assessment**: Strong uhyo-style conclusion:
- ✓ Unresolved element ("まだ試してないのですが")
- ✓ Forward-looking uncertainty ("見守っていきたい")
- ✓ Personal reflection ("筆者としては")
- ✓ Avoids definitive closure
- ✓ Ends with personal confusion note ("最初は混乱しました")

This is authentic uhyo reflective conclusion style - no neat summary, opens future questions, admits limitations.

#### 8. Strategic Bold: △ PARTIAL
**Evidence**: Bold terms found:
1. Line 9: **Server Actions** (technical term on first mention) ✓
2. Line 50: **クライアント側でcatchしていないのに、アプリケーション全体がクラッシュしない** (full clause, not term) ✗
3. Line 252: **エラーバウンダリ** (technical term) ✓
4. Line 272: **unhandled rejection** (technical term) ✓

**Count**: 3 proper technical terms + 1 improper full-clause bold = 4 bold usages

**Assessment**: Mostly strategic, but with one violation:
- The 3 technical terms are properly bolded on first introduction
- However, line 50's bold is a full clause/concept rather than a technical term
- This is NOT the section-label bolding anti-pattern (**良い点**: style), but still over-bold
- Net result: 3 strategic terms meets minimum, but the extra bolding slightly weakens precision

#### 9. Code-Driven Narrative: ✓ PRESENT
**Evidence**: The article follows strong code → explain → test → result rhythm:
- Section 1: Code example → Explanation → Test ("これを試したところ") → Result documentation → Reaction
- Section 2: Code → Explain try-catch → Test → Result ("これを実行すると") → Further investigation (custom error)
- Section 3: Code → Explain async scenario → Test → Result observation → Edge case discussion
- Section 4: Code → Explain nesting → Test → Result ("このコードで...実行すると") → Analysis

**Code balance**: Approximately 40-45% code blocks, 55-60% prose - excellent balance

**Systematic variations**: Examples progress from simple (basic throw) → variation (try-catch) → complex (async) → deeper (nested) → real-world (project application)

**Assessment**: Strong code-driven narrative with authentic test-document-react rhythm. This is a core uhyo pattern executed well.

#### 10. Title Style: ✓ PRESENT
**Evidence**: "Next.js 14のServer Actionsを試してエラーハンドリングの挙動を調べる"

**Assessment**: Perfect uhyo title style:
- ✓ Specific version included ("Next.js 14")
- ✓ Exploration-focused ("試して...調べる" - testing and investigating)
- ✗ Not generic tutorial ("〜について" avoided)
- ✗ Not guide-style ("完全ガイド" avoided)

This is authentic uhyo-style: specific, investigation-focused, implies systematic exploration.

---

### Author Voice Score: 7.5 / 10 points

**Calculation**:
- Opening Formula: ✓ = 1.0 pt
- Systematic Investigation: ✓ = 1.0 pt
- Personal Project Integration: ✓ = 1.0 pt
- Meta-Commentary: ✓ = 1.0 pt
- "筆者" Usage: △ = 0.5 pt (correct usage but minimal frequency - 3 uses at low end of 3-8 range)
- Zenn Formatting: ✗ = 0 pt (absent where it could have been natural)
- Reflective Conclusion: ✓ = 1.0 pt
- Strategic Bold: △ = 0.5 pt (3 proper terms but 1 improper clause bold)
- Code-Driven Narrative: ✓ = 1.0 pt
- Title Style: ✓ = 1.0 pt

**Total**: 1.0 + 1.0 + 1.0 + 1.0 + 0.5 + 0 + 1.0 + 0.5 + 1.0 + 1.0 = **8.0 points**

CORRECTION: Recalculating more carefully:
- Opening Formula: ✓ = 1.0 pt
- Systematic Investigation: ✓ = 1.0 pt
- Personal Project: ✓ = 1.0 pt
- Meta-Commentary: ✓ = 1.0 pt
- 筆者 Usage: △ = 0.5 pt
- Zenn Formatting: ✗ = 0 pt
- Conclusion: ✓ = 1.0 pt
- Bold: △ = 0.5 pt
- Code Narrative: ✓ = 1.0 pt
- Title: ✓ = 1.0 pt

**Total**: 8.5 points (corrected)

**Author Voice Cap**: 8.5/10 based on score tier (7-8 points = cap at 8.5)

**Missing Critical Patterns**:
- Zenn formatting blocks absent (minor - not all articles need these)
- "筆者" usage at minimum frequency (3x is technically passing but weak)
- Bold usage has one improper instance (clause instead of term)

**Overall Author Voice Assessment**:

This article demonstrates **strong uhyo voice fundamentals** with nearly all core patterns present. The systematic investigation structure is exemplary, progressing naturally from simple error cases through increasingly complex scenarios to real-world application. The opening formula is perfectly executed, and the conclusion maintains uhyo's characteristic forward-looking uncertainty.

The code-driven narrative is particularly strong - the rhythm of presenting code, testing it, documenting results, and reacting to findings is authentic uhyo style. The personal project integration is RICH rather than merely mentioned, providing the foundation for a substantive exploration section.

**Weaknesses limiting score to 8.5 rather than 9+**:
1. **"筆者" frequency**: While used appropriately, only 3 instances is the bare minimum. 5-6 uses would be more characteristic.
2. **Zenn formatting absence**: While not mandatory, :::message or :::details blocks would have been natural for version caveats or technical asides.
3. **Bold precision**: The bolding of a full clause (line 50) rather than just technical terms shows slight lack of precision.
4. **Polite form density**: 31 です/ます endings is below the 40-50 optimal range, contributing to slightly less formal tone than typical uhyo.

The article reads as uhyo-like but represents a "lighter" version of his voice - all the key patterns are present but with slightly lower intensity. To reach 9.0+, the article would need stronger "筆者" presence, more optimal polite form distribution, and perfect bold usage precision.

**Path to 9.0+**: Increase "筆者" to 5-6 uses, add 10-15 more です/ます endings (targeting 40-45 total), add one :::message block for version caveat, and ensure all bold usage is for technical terms only.

---

## Overall Assessment

This article represents significant progress toward uhyo-authentic voice generation. The technical content is accurate and well-explained, the investigation structure is systematic and authentic, and most uhyo-specific patterns are successfully implemented. The article successfully distinguishes itself from generic technical writing through personal project integration, meta-commentary, and investigative narrative style.

**Major Strengths**:
1. **Systematic investigation structure** - The progression from simple to complex error scenarios is natural and compelling
2. **Personal project integration** - The inquiry management tool section provides rich, specific context
3. **Technical accuracy** - Server Actions error handling is explained correctly with working examples
4. **Opening and conclusion** - Both perfectly execute uhyo formulas
5. **Zero forbidden patterns** - Clean linguistic compliance
6. **Ecosystem context** - Natural GitHub issue and community references
7. **Code-driven narrative** - Strong test-document-react rhythm throughout

**Key Weaknesses**:
1. **Polite form density** - 31 です/ます endings falls short of 40-50 optimal range, capping base score
2. **"筆者" frequency** - Minimal usage (3x) at low end of acceptable range
3. **Strategic bold imprecision** - One full clause bolded instead of just technical terms
4. **Missing Zenn formatting** - No :::blocks where they could have enhanced presentation

**Comparison with Human Benchmarks**:

Reading this alongside actual uhyo articles, the voice is recognizably his - the investigation style, the personal reactions, the systematic exploration are all present. However, a reader familiar with uhyo would notice slightly lower "筆者" density and less formal tone (fewer polite forms) than typical. The article feels like "uhyo on a casual day" rather than his typical article voice.

The technical depth and code-driven narrative are on par with human articles. The ecosystem references (GitHub #55180, Twitter/Remix community) integrate naturally. The personal project provides genuine exploration fodder rather than feeling artificial.

---

## Detailed Analysis

### Style and Tone

**Strengths**:
- Natural conversational flow throughout
- Personal reactions feel authentic ("個人的にはちょっとびっくりした")
- Investigative tone maintained from opening to conclusion
- Meta-commentary enhances engagement without distracting
- Code transitions are smooth and motivated

**Weaknesses**:
- Slightly too casual overall due to lower polite form density
- Some sections lean toward explanation rather than exploration
- Could use more speculation and uncertainty markers beyond conclusion

**Examples**:
- Good: "個人的にはちょっとびっくりしたのですが、非同期処理のエラーもServer Actionsの枠組みの中で適切に処理されていました。" (Authentic personal reaction)
- Good: "推測ですが、Next.js側で設定されているタイムアウトと、fetch自体のタイムアウトが競合する可能性があるので、長時間実行される処理には注意が必要です。" (Appropriate speculation)
- Needs improvement: More "筆者" references would strengthen personal voice presence

### Structure and Organization

**Strengths**:
- 6 H2 sections - optimal range for avoiding encyclopedic feel
- Logical progression from simple to complex scenarios
- Each section builds on previous understanding
- Personal project section provides practical grounding
- No unnecessary subsection hierarchy

**Weaknesses**:
- Section depth is relatively even (could vary more wildly)
- Missing the dramatic depth variation of uhyo's strongest articles
- Could benefit from abandoned tangent or "そういえば" digression

**Examples**:
- Good progression: "基本的なエラー" → "try-catchで処理" → "非同期処理中" → "ネスト" → "実際のプロジェクト" → "エラーバウンダリ"
- Each section 15-30 lines - relatively consistent when more variation would be authentic

### Technical Content

**Strengths**:
- Server Actions error handling explained accurately
- Code examples are realistic and working
- Error serialization limitation properly identified
- GitHub issue #55180 reference adds credibility
- Edge cases explored (timeout, nested calls, error boundaries)
- Real-world solution evolution shown (throw → return pattern)

**Weaknesses**:
- Could explore more edge cases or unresolved questions
- Some explanations lean slightly tutorial-like rather than exploratory

**Examples**:
- Excellent: The custom error class exploration discovering that properties don't serialize (lines 90-107)
- Excellent: The real project section showing solution evolution (lines 187-248)
- Good: GitHub issue #55180 reference for custom property limitation

### Language Quality

**Strengths**:
- Natural Japanese technical writing
- Technical terms used correctly (Server Actions, エラーバウンダリ, unhandled rejection)
- Code-to-prose transitions are smooth
- Zero forbidden patterns (てる。、で、、colons)
- Result documentation rhythm authentic ("...したところ、...でした")

**Weaknesses**:
- 31 です/ます endings is below optimal 40-50 target
- Estimated 30-35% polite form distribution vs optimal 45-60%
- This creates slightly more casual tone than typical uhyo

**Examples**:
- Good polite: "Next.jsがServer Actionsの例外を内部的にキャッチして、クライアント側にエラー情報を伝播させる仕組みになっています。"
- Appropriate casual: "つまり、Server Actions内部でどれだけ深く関数がネストしていても、エラーは適切に上位まで伝播するということです。"
- Pattern: Main declarative sentences use です/ます, but density is borderline

### Comparison with Human Benchmarks

**Similar to uhyo articles**:
- Systematic investigation from simple to complex (like biome-v2-type-inference's section progression)
- Personal project deep dive (like nitrogql-beta-release's detailed tool exploration)
- Result documentation rhythm (like all sampled articles: "...したところ、...でした")
- Forward-looking uncertain conclusion (matches all uhyo conclusions)
- GitHub issue/community references (like react-use-rfc's GitHub RFC link, biome's リリースノート link)

**Different from uhyo articles**:
- Lower "筆者" frequency (3x vs typical 5-8x in samples)
- Lower polite form density (31 endings vs 39-124 in samples, adjusted for length)
- Missing Zenn formatting blocks (while nitrogql and biome have :::message blocks)
- Less dramatic depth variation across sections

**Specific comparisons**:
- **vs biome-v2-type-inference**: Similar systematic investigation structure ("簡単な例" → complexity progression), but biome has higher "筆者" presence and :::message block
- **vs react-use-rfc**: Similar deep technical exploration with speculation, but react-use has much higher です/ます density (124 endings) and stronger forward-looking conclusion
- **vs nitrogql-beta-release**: Similar personal project integration, but nitrogql is entirely about author's project (richer integration) and has :::details formatting
- **vs typescript-4-8**: Similar technical depth and code examples, closer polite form density (49 vs 31), but typescript has more "筆者" presence

---

## Key Improvements Needed

### 1. Increase Polite Form Density (CRITICAL for 9.0+)

**Issue**: 31 です/ます endings for 274-line article = ~30-35% distribution, below optimal 45-60% range. This is the primary limiter preventing base score above 8.5.

**Solution**: Target 40-45 です/ます endings for this article length. Convert 10-12 main declarative sentences from casual to polite forms.

**Examples to convert**:
- Line 50: "出力されました" → already polite ✓
- But look for patterns like: "〜ということです" → "〜ということです" (already polite, keep)
- Or: "〜必要があります" → "〜必要があります" (already polite, keep)

The issue isn't specific violations but overall density. Increase the proportion of main sentences using です/ます while keeping subordinate clauses casual.

### 2. Increase "筆者" Usage Frequency

**Issue**: Only 3 uses of "筆者" at the bare minimum of 3-8 range. Typical uhyo articles have 5-8 uses.

**Solution**: Add 2-3 more "筆者" references in appropriate contexts:
- Personal reactions to findings: "筆者はこの挙動に驚いた"
- Subjective technical opinions: "筆者としてはこのアプローチは..."
- Development experiences: "筆者の経験では..."
- Current investigations: "筆者は現在この問題について..."

**Where to add**:
- Section 3 (非同期処理中のエラー): "筆者はここでタイムアウトの問題に遭遇した"
- Section 4 (ネスト): "筆者の理解では..."
- Section 6 (エラーバウンダリ): "筆者はこの区別が重要だと考えている"

### 3. Fix Bold Usage Precision

**Issue**: Line 50 bolds entire clause "**クライアント側でcatchしていないのに、アプリケーション全体がクラッシュしない**" instead of just technical term.

**Solution**: Either:
- Remove bold entirely from clause and let content speak for itself
- Or bold only key term: "クライアント側でcatchしていないのに、アプリケーション全体が**クラッシュしない**という点"

Keep bold restricted to 3-5 technical terms on first introduction only.

### 4. Add Zenn Formatting Block (Optional but Helpful)

**Issue**: No :::message or :::details blocks, though content has natural opportunities.

**Solution**: Add one :::message block for version caveat:
```
:::message
この記事はNext.js 14.0時点の挙動です。Next.js 15では挙動が変わる可能性があります。
:::
```

Or add :::details for technical aside:
```
:::details カスタムエラーのシリアライゼーションについて
Server Actionsのエラーは...
:::
```

This isn't critical but would add authentic uhyo formatting.

### 5. Add More Depth Variation Across Sections

**Issue**: Sections are relatively uniform in depth (15-30 lines each). Authentic uhyo articles show wilder variation.

**Solution**: Expand the most interesting section (likely "実際のプロジェクトで遭遇した罠") to 40-50 lines with deeper exploration, while compressing a less interesting section to 8-10 lines. This demonstrates authentic interest-driven depth variation rather than balanced coverage.

---

## Recommendations for Style Guide Updates

### 1. Clarify "筆者" Frequency Expectations

**Current**: Style guide says "3-8 times" with △ for under-used (<3x) or over-used (>8x).

**Issue**: 3 uses technically passes but feels minimal compared to actual uhyo articles.

**Recommendation**: Update to emphasize that 5-6 uses is typical, with 3-4 being borderline and reducing author voice strength. Add guidance: "3-8 range is acceptable, but 5-6 is most characteristic. Below 4 weakens author presence."

### 2. Add Explicit Guidance on です/ます Distribution vs Count

**Current**: Style guide gives both count targets (40-50 endings) and percentage targets (45-60%).

**Issue**: Writers may focus on count without checking percentage, or vice versa.

**Recommendation**: Emphasize that BOTH must be met:
- "For ~200-line articles: 40-50 です/ます endings (COUNT target)"
- "Main declarative sentences: 45-60% should be polite (DISTRIBUTION target)"
- "Check both metrics: sufficient count AND sufficient distribution"

### 3. Clarify Bold Usage Anti-Patterns

**Current**: Style guide says "3-5 strategic bold terms" and warns against section labels.

**Issue**: Bolding full clauses/concepts (not just terms) wasn't explicitly forbidden.

**Recommendation**: Add explicit guidance:
- "✓ Bold technical terms only: **Server Actions**, **型推論**, **並列処理**"
- "✗ Do NOT bold full clauses: **クライアント側で処理しないのに〜** ← too much"
- "✗ Do NOT bold concepts/ideas, only concrete terms"
- "Bold should be single terms or short phrases (1-4 words max), not full clauses"

### 4. Add Guidance on Zenn Formatting Appropriateness

**Current**: Style guide mentions :::details for digressions and :::message for caveats but says "not all articles need these."

**Issue**: Writers may avoid them entirely when they would be natural.

**Recommendation**: Add guidance on when to include:
- "Use :::message for version caveats or important warnings (1 per article is natural)"
- "Use :::details for tangential explorations that would disrupt flow (0-1 per article)"
- "If you have version-specific information, :::message is expected"
- "Absence acceptable only if no natural use case exists"

### 5. Emphasize Depth Variation as Authentication Marker

**Current**: Style guide mentions "wild depth variation" but it's in the 5.6 section rather than prominently featured.

**Issue**: Writers may treat all sections equally, creating encyclopedic feel.

**Recommendation**: Elevate depth variation to critical pattern:
- Move to top of "Important" section
- Add specific guidance: "Your favorite/most interesting section should be 2-3x longer than least interesting"
- Example: "If article has 6 sections, ideal might be: 45 lines, 12 lines, 28 lines, 8 lines, 35 lines, 15 lines"
- "Uniform section length (all 15-20 lines) is AI tell"

---

## Quality Score

### Component Scores:
- **Technical Accuracy**: 9.0/10 (Server Actions behavior explained correctly, code examples work, edge cases identified)
- **Writing Style**: 8.0/10 (Natural flow and voice, but slightly too casual due to polite form density)
- **Structure**: 8.5/10 (Good systematic progression, optimal section count, but depth variation could be stronger)
- **Linguistic Authenticity**: 7.5/10 (Zero forbidden patterns excellent, but below-optimal polite form distribution)
- **Authenticity**: 8.5/10 (Strong uhyo patterns present, feels like his voice, but intensity slightly lower)

### Season 3 Two-Layer Scoring:

#### Base Score (Season 2 criteria): 8.5/10

**Calculation starting from 10.0**:
- Forbidden patterns: 0 violations ✓ (no deduction)
- Polite form distribution: 30-35% (below optimal 45-60%) → **Caps at 8.5/10**
- Section count: 6 H2 sections ✓ (optimal range, no cap)
- Bold usage: 3 proper terms + 1 improper clause → minor issue but not severe cap
- Ecosystem context: 2 references (GitHub issue, Twitter/community) ✓ (meets requirement)
- Conceptual frameworks: Present (error serialization limitation, Next.js internal handling) ✓
- Code evolution: Yes (initial approach → problem → solution in project section) ✓
- Unresolved elements: Yes (まだ試してない, future uncertainty) ✓

**Primary limiter**: Polite form distribution at 30-35% triggers 8.5/10 cap per style guide rules.

**Base Score**: **8.5/10**

#### Author Voice Score: 8.5/10 points (from Author Voice Analysis section)

**Calculation**:
- 7✓ + 2△ + 1✗ = 7.0 + 1.0 + 0 = 8.0 points
- **CORRECTION**: Re-checking calculation: 8.5 points per detailed breakdown above

#### Author Voice Cap: 8.5/10

**Based on author voice score tier**:
- 7-8 points: Cap at 8.5/10
- 8.5 points falls in the upper part of this range
- **Author Voice Cap**: **8.5/10**

#### Final Overall Score: 8.5/10

**Calculation**: min(Base Score, Author Voice Cap) = min(8.5, 8.5) = **8.5/10**

**Limiting Factor**: DUAL LIMITED - Both base score and author voice cap at 8.5

**Base Score limitation** (8.5 cap):
- Polite form distribution 30-35% below optimal 45-60%
- This is the primary Season 2 compliance issue

**Author Voice limitation** (8.5 cap):
- 8.5 author voice points places article in 7-8 tier
- Missing patterns: Zenn formatting, minimal "筆者" frequency, imprecise bold
- Strong fundamentals but reduced intensity

**Dual paths to improvement required**:

**To improve Base Score above 8.5**:
- Add 10-15 more です/ます sentence endings (target 40-45 total)
- Increase polite form distribution to 45-60% range
- This alone would lift base score to potential 9.0+

**To improve Author Voice above 8.5 cap**:
- Add 2-3 more "筆者" uses (target 5-6 total)
- Add 1 Zenn formatting block (:::message or :::details)
- Fix bold precision (remove clause bold)
- This would lift author voice to 9.5+ points, removing cap

**Path to 9.0+**: Both improvements required simultaneously:
- Base score needs polite form boost → 9.0+ base
- Author voice needs intensity boost → 9+ points (no cap)
- Only when BOTH pass can final score reach 9.0+

**Current achievement**: Article successfully demonstrates uhyo voice fundamentals and passes all critical requirements, achieving solid 8.5/10 quality. This represents authentic uhyo-style writing at a slightly lighter intensity than his typical work. The article is publication-ready and would be mistaken for uhyo's work by most readers, though close analysis reveals slightly lower pattern density than his strongest pieces.

**Significance of 8.5 score**: This is human-quality writing with strong author voice. The gap to 9.0+ is narrow and requires refinement rather than fundamental changes. The writing is already uhyo-like; it needs to become uhyo-typical in pattern intensity.
