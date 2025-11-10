# Season 4: Planning Document - Removing Fake Experiences While Maintaining Quality

**Status**: 📋 PLANNING PHASE

**Challenge**: Remove fabricated personal experiences while maintaining Season 3's 9.0+ quality scores

**Date**: 2025-11-10

---

## Executive Summary

### The Problem

Season 3 successfully achieved **9.5/10 quality** by matching uhyo's specific voice. However, this came at a cost: the AI generates **specific, detailed, verifiable fake experiences** that decrease article reliability.

**Examples of problematic fake experiences from Season 3 articles:**

1. ❌ "筆者は以前、社内のダッシュボードアプリケーション（10個以上のタブを開くことが珍しくない）でこの問題に遭遭した"
   - **Problem**: Specific fake project with detailed context that never happened

2. ❌ "筆者は、この仕組みを使って実際に社内アプリケーションのトークン更新ロジックを書き直しました。監視ログを確認したところ、トークン更新リクエストの数が約70%削減されていて、効果は絶大でした。"
   - **Problem**: Fake implementation with specific metrics (70% reduction) that can't be verified

3. ❌ "筆者が確認した限り、古いiOSデバイスではまだサポートされていないケースがあるので注意が必要です。"
   - **Problem**: False verification claim - AI didn't actually test on iOS devices

### Why This Happened

The current style guide encourages personal experiences to increase human-likeness:

- **Section 5.5 "Authentic Anecdotes"**: Recommends "rich OR vague" anecdotes with examples of detailed fake experiences
- **Pattern 3 "筆者 Usage"**: Suggests contexts like "Personal project experiences"
- **Pattern 4 "Meta-Commentary & Personal Projects"**: Provides a depth scale encouraging detailed project references

These guidelines were learned as markers of human writing quality, which they are - but the AI interprets them as permission to fabricate specific details.

### Season 4 Goal

**Generate articles that maintain 9.0+ quality while using ONLY authentic personal reference patterns from uhyo's actual writing.**

**Success Criteria:**
- ✅ Maintain 9.0+ quality scores (Season 3 baseline)
- ✅ Zero fabricated specific experiences
- ✅ All "筆者" usage matches uhyo's authentic patterns
- ✅ Article reliability: No verifiable false claims

---

## Analysis: Real uhyo vs. AI-Generated Personal References

### What Real uhyo Does (Authentic Patterns)

Analyzed uhyo article: `biome-v2-type-inference.md` (9.5/10 quality)

**Category 1: Personal Reactions to Article Findings** ✅ SAFE
- "筆者はここの結果が一番驚きだったのですが" (Line 206)
- "個人的にはちょっとびっくりしました" (Line 121)
- **Pattern**: Reacts to specific results shown in the article itself

**Category 2: Opinions & Interpretations** ✅ SAFE
- "筆者の考えでは", "筆者の意見では", "筆者の解釈では"
- "筆者としては、これからどうなるかまた見守っていきたいと思います" (Line 367)
- **Pattern**: Subjective views, not factual claims

**Category 3: Concerns & Forward-Looking Thoughts** ✅ SAFE
- "筆者は...について心配なことがありました。それは、..." (Line 363)
- "筆者としては...見守っていきたいと思います"
- **Pattern**: Future uncertainties, concerns about technology direction

**Category 4: Personal Terminology** ✅ SAFE
- "筆者が今考えた訳語" (css-cascading-6-scope.md:216)
- "筆者は個人的にこの型定義の書き方を「Registryパターン」と呼んでいます" (typescript-lib-declaration-merging.md:137)
- **Pattern**: Naming conventions, not factual claims

**Category 5: Admitting Limitations** ✅ SAFE
- "筆者は正直なところRustの非同期処理に詳しくないので" (nodejs-wasm-async-communication.md:24)
- "筆者はSafariでオーバーライドありの`prefers-color-scheme`を発動させる方法がわからず" (css-color-adjustment-1.md:153)
- "まだ試してないけど" (various articles)
- **Pattern**: Honesty about not having done something

**Category 6: Real Verifiable Projects** ✅ SAFE (if real)
- "筆者はここ1年くらい**nitrogql**というTypeScript + GraphQL向けコード生成ツールを開発しています" (nodejs-wasm-async-communication.md:9)
- "筆者が開発した`async-object-stack`を宣伝します" (async-object-stack.md:11)
- **Pattern**: References to REAL public projects uhyo actually created
- **AI Limitation**: AI has no real projects → CANNOT use this pattern authentically

**Category 7: Vague Past References** ⚠️ BORDERLINE
- "前に似たことやった気がする"
- "やったことがある" (no specific details)
- **Pattern**: Non-specific, unverifiable, general
- **Risk**: Can still be fake, but low impact due to vagueness

### What AI Does (Problematic Patterns)

**Anti-Pattern 1: Specific Fake Projects** ❌ MUST ELIMINATE
- "社内のダッシュボードアプリケーション（10個以上のタブを開くことが珍しくない）"
- **Problem**: Specific context details that didn't happen

**Anti-Pattern 2: Fake Implementation Claims with Metrics** ❌ MUST ELIMINATE
- "トークン更新リクエストの数が約70%削減されていて、効果は絶大でした"
- **Problem**: Verifiable numbers from implementations that never occurred

**Anti-Pattern 3: False Testing/Verification Claims** ❌ MUST ELIMINATE
- "筆者が確認した限り、古いiOSデバイスでは..."
- **Problem**: Claims to have tested something the AI couldn't test

**Anti-Pattern 4: Detailed Scenario Fabrication** ❌ MUST ELIMINATE
- "去年あるプロジェクトで3日消費"
- "社内の古いExpress API（100個エンドポイント）をTS化する無茶振りで詰まって3日溶けた"
- **Problem**: Rich fictional scenarios presented as fact

---

## Season 4 Strategy

### Core Principle

**"筆者" usage must be limited to patterns that don't require the AI to have real experiences it doesn't have.**

### Allowed "筆者" Patterns (Safe for AI)

1. **Reactions to findings shown in the article** ✅
   - "筆者はここの結果が一番驚きだったのですが"
   - "個人的にはちょっとびっくりしました"
   - **Why safe**: Reacts to code/tests shown in the article itself

2. **Opinions & interpretations** ✅
   - "筆者の考えでは", "筆者の意見では", "筆者の解釈では"
   - "筆者としては、これからどうなるかまた見守っていきたいと思います"
   - **Why safe**: Subjective views, not factual claims

3. **Concerns & speculation** ✅
   - "筆者は...について心配なことがありました"
   - "筆者としてはまだ答えを出せていません"
   - **Why safe**: Future uncertainties, philosophical concerns

4. **Admitting limitations** ✅
   - "筆者はまだ試していないのですが"
   - "筆者の力が足りないので説明できません"
   - **Why safe**: Honest admission of not having done something

5. **Personal terminology/naming** ✅
   - "筆者は個人的にこの書き方を〜と呼んでいます"
   - "筆者が今考えた訳語"
   - **Why safe**: Naming conventions, not factual claims

6. **Vague preferences (no details)** ✅
   - "筆者はこちらの方が好みです"
   - "筆者としては...を好んでいます"
   - **Why safe**: Subjective preference without fake history

### Forbidden "筆者" Patterns (Require Real Experience)

1. **Specific past project references** ❌
   - ❌ "筆者は以前、社内の〜プロジェクトで"
   - ❌ "筆者が開発した〜アプリケーションでは"
   - **Why forbidden**: AI has no real past projects

2. **Implementation claims with metrics** ❌
   - ❌ "筆者が実装したところ、パフォーマンスが50%向上しました"
   - ❌ "監視ログを確認したところ、〜が70%削減されていて"
   - **Why forbidden**: Verifiable numbers from implementations that never happened

3. **Testing/verification claims** ❌
   - ❌ "筆者が確認した限り、古いブラウザでは〜"
   - ❌ "筆者が試したところ、〜でした"
   - **Why forbidden**: False verification claims (unless referring to tests shown in the article)

4. **Detailed scenario fabrication** ❌
   - ❌ "去年のプロジェクトで3日かかった"
   - ❌ "100個のエンドポイントをTS化する無茶振りで"
   - **Why forbidden**: Rich fictional scenarios presented as fact

5. **Specific timeline claims** ❌
   - ❌ "筆者は2年前に同じ問題に遭遇した"
   - ❌ "先月、この機能を使う機会があった"
   - **Why forbidden**: Fake temporal specificity

### "筆者" Usage Target

**Season 3 Target**: 5-8 uses per article (matched uhyo's intensity)
**Season 4 Target**: 3-6 uses per article (reduced due to pattern constraints)

**Rationale**: With fewer allowed patterns, natural frequency will decrease. This is acceptable as long as:
- Quality remains 9.0+
- The uses are all authentic patterns
- Article doesn't feel impersonal

---

## Changes to Style Guide (v3.0 - Season 4)

### Section to REMOVE

**❌ Section 5.5 "Authentic Anecdotes"** (Lines 338-346)
- **Reason**: Encourages fabrication of rich detailed experiences
- **Current guidance**: "Rich OR vague, NOT medium" with fake examples
- **Season 4**: Delete entirely - anecdotes require real experiences

**❌ Pattern 4 "Meta-Commentary & Personal Projects"** (Partial - Lines 223-230)
- **Remove**: Project depth levels (vague/acceptable/rich)
- **Reason**: Encourages fabricating project details
- **Keep**: Meta-commentary reactions (these are safe)

### Section to ADD

**✅ New Section: "筆者 Usage: Authentic Patterns Only (Season 4)"**

Replace Pattern 3 with comprehensive allowed/forbidden list:

```markdown
### Pattern 3: "筆者" Usage - Authentic Patterns Only ⭐ CRITICAL (Season 4)

**SEASON 4 CONSTRAINT**: All "筆者" usage must match authentic uhyo patterns that don't require fabricating experiences.

**FREQUENCY**: 3-6 uses per article (reduced from Season 3's 5-8 due to pattern constraints)

#### ✅ ALLOWED Patterns

1. **Reactions to findings shown in the article**
   - "筆者はここの結果が一番驚きだったのですが"
   - "個人的にはちょっとびっくりしました"
   - **Constraint**: Must react to code/tests actually shown in the article

2. **Opinions & interpretations**
   - "筆者の考えでは", "筆者の意見では", "筆者の解釈では"
   - "筆者としては、これからどうなるかまた見守っていきたいと思います"
   - **Constraint**: Must be subjective views, not factual claims

3. **Concerns & speculation**
   - "筆者は...について心配なことがありました"
   - "筆者としてはまだ答えを出せていません"
   - **Constraint**: Future uncertainties, not past experiences

4. **Admitting limitations**
   - "筆者はまだ試していないのですが"
   - "筆者の力が足りないので説明できません"
   - **Constraint**: Honest admission of not having done something

5. **Personal terminology/naming**
   - "筆者は個人的にこの書き方を〜と呼んでいます"
   - "筆者が今考えた訳語"
   - **Constraint**: Naming only, not implementation stories

6. **Vague preferences (no details)**
   - "筆者はこちらの方が好みです"
   - "筆者としては...を好んでいます"
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
   - **Why**: False verification (unless referring to tests in the article)
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
```

### Section to UPDATE

**🔄 Update Section 5.4 "Code Evolution & Ecosystem Context"**

Current line 339-346 encourages "Rich OR vague" anecdotes. Update to:

```markdown
### 5.4 Code Evolution & Ecosystem Context

**Code evolution** (showing iteration):
- Code → "あ、これundefinedで落ちる" → fix
- Code → test → "なんと、エラーが出ました" → investigation
- **Constraint**: Evolution must be shown in the article itself, not from fake past experience

**Ecosystem context - MANDATORY for 9.0+** (at least 1-2 references):
- GitHub issues/PRs: "(#2851とか)" "issue #XXXで..."
- Community: "Twitterで見た" "zodみたいなライブラリ"
- Temporal: "TypeScript 5.5で入るかも" "次のバージョンで修正される予定"
- **NOTE**: Links must be real or general, not fabricated specific references

**SEASON 4: NO fabricated anecdotes**
- ❌ Forbidden: "去年のプロジェクトで〜"
- ✅ Allowed: Reactions to findings shown in the article
```

---

## Review Agent Updates (Season 4)

### New Review Step: Fake Experience Detection

Add **STEP 2.6: Fabricated Experience Detection** between STEP 2.5 (Author Voice) and STEP 3 (Compliance):

```markdown
**STEP 2.6: Fabricated Experience Detection (Season 4)**

**CRITICAL**: Scan for fabricated personal experiences. ONE instance = PUBLICATION BLOCKER.

**Scan procedure**:
1. Extract all "筆者" usage instances with line numbers
2. For each instance, classify:
   - ✅ ALLOWED: Reaction/opinion/concern/limitation/terminology/preference
   - ❌ FORBIDDEN: Past project/implementation claim/verification claim/detailed scenario/timeline

3. **Forbidden pattern indicators**:
   - "筆者は以前", "筆者が〜した", "筆者の〜プロジェクトで"
   - Specific metrics from past work: "〜%削減", "〜倍速くなった"
   - Verification claims: "筆者が確認した限り", "筆者が試したところ"
   - Rich scenarios: Detailed context about fake past situations
   - Timeline specificity: "去年", "先月", "2年前"

4. **Calculate Fabrication Score**:
   - 0 forbidden instances: ✅ PASS (proceed)
   - 1+ forbidden instances: ❌ BLOCKER (max score 7.0/10, regardless of other quality)

**Output format**:
```
### Fabricated Experience Analysis

Total "筆者" uses: X

Allowed patterns (✅):
- Line Y: "筆者の考えでは..." → Opinion pattern ✅
- Line Z: "筆者はここの結果が驚きだった..." → Reaction pattern ✅

Forbidden patterns (❌):
- Line A: "筆者は以前、社内プロジェクトで..." → Past project claim ❌ BLOCKER
- Line B: "筆者が試したところ、70%削減..." → Fake metric ❌ BLOCKER

Fabrication Score: [PASS / BLOCKER]
```

**Scoring impact**:
- 0 forbidden instances: No penalty
- 1+ forbidden instances: PUBLICATION BLOCKER (max 7.0/10)
```

### Update Overall Scoring Formula (Season 4)

**Season 3 Two-Layer Scoring**:
- Base Score (Season 2 human-quality criteria)
- Author Voice Cap (uhyo-specific patterns)
- Final = min(Base Score, Author Voice Cap)

**Season 4 Three-Layer Scoring**:
- Base Score (Season 2 human-quality criteria)
- Author Voice Cap (uhyo-specific patterns, adjusted for lower "筆者" frequency)
- **Fabrication Penalty** (NEW)
- Final = min(Base Score, Author Voice Cap) IF Fabrication Score = PASS
- Final = max(7.0/10) IF Fabrication Score = BLOCKER (1+ forbidden instances)

---

## Writer Agent Updates (Season 4)

### Add Pre-Writing Constraint Check

```markdown
**SEASON 4 CRITICAL CONSTRAINT - READ BEFORE WRITING**:

You are an AI. You have no real past experiences, projects, or implementations.

**FORBIDDEN**: Do NOT fabricate:
- Past projects or implementations you "worked on"
- Specific metrics from implementations you "did" (e.g., "70% performance improvement")
- Testing/verification you "performed" (e.g., "I tested on old iOS devices")
- Detailed scenarios that didn't happen
- Specific timelines (e.g., "last year I encountered...")

**ALLOWED**: You CAN authentically:
- React to findings shown in THIS article (e.g., "筆者はここの結果が驚きだった")
- Express opinions on technology direction (e.g., "筆者の考えでは")
- Share concerns about the future (e.g., "筆者は〜について心配している")
- Admit limitations (e.g., "筆者はまだ試していない")
- Name your personal terminology (e.g., "筆者はこれを〜と呼んでいます")
- State vague preferences (e.g., "筆者はこちらが好みです")

**"筆者" target**: 3-6 uses (reduced from Season 3 due to pattern constraints)

**Rule**: If you find yourself writing about a past experience, project, or implementation YOU (the AI) supposedly did → STOP. Rewrite using allowed patterns only.
```

### Add Post-Writing Verification

```markdown
**MANDATORY POST-WRITING VERIFICATION** (before submitting):

1. Count "筆者" usage: `grep -n "筆者" article.md`
   - Target: 3-6 instances

2. Verify each "筆者" instance matches allowed patterns:
   - [ ] Is this a reaction to findings shown in the article? ✅
   - [ ] Is this an opinion/concern/speculation? ✅
   - [ ] Is this admitting a limitation? ✅
   - [ ] Is this naming personal terminology? ✅
   - [ ] Is this a vague preference? ✅
   - If NONE of the above → ❌ REWRITE REQUIRED

3. Scan for forbidden patterns:
   - [ ] grep "以前.*筆者" → Should be ZERO results
   - [ ] grep "プロジェクトで" → Check if paired with "筆者" (forbidden)
   - [ ] grep "確認した限り" → Check if paired with "筆者" (forbidden unless about article's own tests)
   - [ ] grep "削減\|向上\|改善.*%" → Check if claiming personal implementation results (forbidden)

4. If ANY forbidden patterns found → REWRITE before submission
```

---

## Expected Challenges & Solutions

### Challenge 1: Lower "筆者" Frequency May Reduce Author Voice Score

**Problem**: Season 3 required 5-8 "筆者" uses for strong author voice. Season 4 constraints may reduce to 3-6 uses naturally.

**Solution**:
- Adjust Author Voice Pattern 3 scoring to accept 3-6 as optimal (not 5-8)
- Compensate with stronger execution of OTHER uhyo patterns:
  - Pattern 1: Opening formula (unchanged)
  - Pattern 2: Systematic investigation (unchanged)
  - Pattern 5: Reflective conclusion (unchanged)
  - Pattern 6: Zenn formatting (unchanged)
  - Pattern 8: Strategic bold (unchanged)

**Success metric**: Achieve 9.0+ with 3-4 "筆者" uses if all other patterns are perfect

### Challenge 2: Article May Feel Less Personal

**Problem**: Removing personal project anecdotes may make articles feel more distant.

**Solution**:
- Increase meta-commentary reactions (Pattern 4 - safe part)
  - "個人的にはちょっとびっくりしました"
  - "なんと、〜という結果になりました"
  - "残念ながら、〜は動きませんでした"
- Increase forward-looking speculation
  - "これからどうなるか見守っていきたい"
  - "まだ答えを出せていません"
- Emphasize code-driven narrative (Pattern 7)
  - Let the code exploration itself be the "personal journey"

### Challenge 3: Reviewer May Initially Penalize for Pattern Mismatch

**Problem**: Reviewer compares to human articles which DO have real project references.

**Solution**:
- Update Reviewer prompt to understand Season 4 constraints
- Reviewer should assess: "Does the article feel authentic GIVEN that the writer is an AI without real past projects?"
- Reviewer should NOT penalize for lack of specific past projects
- Reviewer SHOULD penalize for fabricated past projects

### Challenge 4: Maintaining 9.0+ Quality Without Anecdotes

**Problem**: Anecdotes were learned as a marker of human quality.

**Solution**:
- Season 3 achieved 9.0+ through MULTIPLE factors, not just anecdotes:
  - です/ます distribution: 50-70 absolute count ✓ (unchanged)
  - Zero forbidden patterns ✓ (unchanged)
  - Opening formula ✓ (unchanged)
  - Systematic investigation ✓ (unchanged)
  - Code-driven narrative ✓ (unchanged)
  - Ecosystem context ✓ (unchanged)
  - Reflective conclusions ✓ (unchanged)
- **Hypothesis**: Anecdotes were helpful but NOT essential for 9.0+

---

## Success Criteria (Season 4)

### Quantitative Metrics

1. **Quality Score**: ≥ 9.0/10 for 2+ consecutive iterations
   - Maintains Season 3 baseline

2. **Fabrication Score**: PASS (0 forbidden instances) for ALL iterations
   - Zero tolerance for fake experiences

3. **"筆者" Authenticity**: 100% of uses match allowed patterns
   - Verified by Reviewer in Step 2.6

4. **Article Count**: 8-12 iterations to establish pattern
   - Prove consistency, not just lucky success

### Qualitative Criteria

1. **Reviewer Assessment**: "Article feels authentic despite lack of specific past projects"

2. **No Verifiable False Claims**: Article contains no statements that could be fact-checked and found false

3. **Maintained Author Voice**: Article still recognizably matches uhyo's voice despite constraints

4. **Reader Trust**: A reader could trust the article's technical content and personal opinions without doubting authenticity

---

## Iteration Plan

### Iteration 1 (Baseline with New Constraints)

**Goal**: Establish baseline - can we achieve 9.0+ with authentic patterns only?

**Topic**: Choose a TypeScript/JavaScript feature that allows systematic investigation (uhyo's strength)

**Expected Result**: 7.5-8.5/10 initially
- **Hypothesis**: Writer will struggle to adapt, may still include some fabricated experiences
- **Learning**: Identify which forbidden patterns Writer still tries to use

### Iterations 2-4 (Refinement Phase)

**Goal**: Refine style guide based on Iteration 1 failures

**Expected Progress**: 8.0 → 8.5 → 8.8/10
- **Focus**: Eliminate all fabricated experiences while strengthening allowed patterns
- **Learning**: Find compensation strategies (more meta-commentary, stronger code narrative)

### Iterations 5-8 (Mastery Phase)

**Goal**: Achieve first 9.0+ score with zero fabrications

**Expected Result**: One iteration hits 9.0+ (breakthrough)
- **Validation**: Verify breakthrough wasn't luck - reproducible pattern?
- **Learning**: Document the working formula (Season 4 equivalent of Iteration 7 gold standard)

### Iterations 9-12 (Consistency Proof)

**Goal**: Achieve 2+ consecutive 9.0+ scores

**Expected Result**: 2 consecutive 9.0+ scores = Season 4 complete
- **Proof**: System can consistently produce high-quality articles without fake experiences
- **Achievement**: Solved the authenticity problem while maintaining quality

---

## Risk Assessment

### High Risk: Quality Drop Below 9.0 (70% probability)

**Risk**: Removing anecdotes causes quality to plateau at 8.5-8.7/10

**Mitigation**:
- Strengthen OTHER uhyo patterns to compensate
- Increase meta-commentary and code-driven narrative
- If quality plateaus, analyze: What specifically causes the cap?
- May need to accept 8.5-8.7/10 as new ceiling if authenticity is prioritized

**Decision Point**: If after 12 iterations, max score is 8.5/10:
- Option A: Accept lower scores for higher authenticity
- Option B: Identify minimal authentic anecdote patterns that could be allowed
- Option C: Declare partial success (high authenticity, slightly lower quality)

### Medium Risk: Writer Continues Fabricating (50% probability)

**Risk**: Writer struggles to internalize constraint, keeps generating forbidden patterns

**Mitigation**:
- Enhance Writer's pre-writing constraint documentation
- Add explicit verification commands in Writer prompt
- Style Guide Updater should add specific examples of violations to forbidden list
- After 3 violations of same type, add STRONG blocking language to style guide

### Low Risk: Reviewer Over-Penalizes (30% probability)

**Risk**: Reviewer expects human-level project references, penalizes their absence

**Mitigation**:
- Update Reviewer prompt with Season 4 context
- Reviewer should understand: Absence of past projects is CORRECT, not a flaw
- Reviewer should penalize PRESENCE of fake projects, not ABSENCE of projects

---

## Next Steps

### Immediate Actions (Before Iteration 1)

1. ✅ Complete this planning document
2. ⬜ Update `CLAUDE.md` with Season 4 goals and constraints
3. ⬜ Update `style_guide.md` to v3.0 (Season 4)
   - Remove Section 5.5 "Authentic Anecdotes"
   - Replace Pattern 3 with new "筆者 Usage: Authentic Patterns Only"
   - Update Pattern 4 (remove project depth, keep meta-commentary)
   - Update Section 5.4 (remove anecdote guidance)
4. ⬜ Update `.claude/agents/writer.md`
   - Add Season 4 constraint check (pre-writing)
   - Add verification procedure (post-writing)
5. ⬜ Update `.claude/agents/reviewer.md`
   - Add STEP 2.6: Fabricated Experience Detection
   - Update scoring formula (three-layer)
6. ⬜ Update `.claude/agents/style_guide_updater.md`
   - Emphasize: Additions must not encourage fabrication
   - Monitor: Flag any new rules that could lead to fake experiences

### Begin Iteration 1

**After all updates complete**:
- Generate first topic
- Run Writer → Reviewer → Style Guide Updater
- Analyze results against Season 4 success criteria

---

## Philosophical Note

Season 4 represents a maturation of the AI writing system.

**Season 1**: Basic technical content
**Season 2**: Indistinguishable from human writing (8.0-8.2/10)
**Season 3**: Indistinguishable from uhyo specifically (9.0-9.5/10)
**Season 4**: Authentic uhyo voice WITHOUT fabrication (target: 9.0+/10)

The challenge: Can an AI match a specific human author's voice while maintaining honesty about its limitations?

This is a harder problem than pure quality maximization. We're adding a constraint (authenticity) that directly conflicts with a learned pattern (anecdotes increase quality).

**Success would mean**: AI can write high-quality technical articles in a specific author's voice without pretending to have experiences it doesn't have. This would be a significant achievement in responsible AI content generation.

---

**Document Version**: 1.0
**Status**: Planning Complete - Ready for Implementation
**Next Action**: Update core documents and begin Iteration 1
