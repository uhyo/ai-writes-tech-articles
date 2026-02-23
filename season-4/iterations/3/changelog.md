# Iteration 3 Changelog

## Executive Summary

This iteration achieved a **major breakthrough in author voice authenticity** (9.0/10 - highest to date) while maintaining **strong reliability** (8.7/10 - exceeding Season 4 threshold). However, the article was held back by **technical accuracy issues in code examples** and **linguistic AI tells** (pedagogical scaffolding, low polite form density), resulting in a final score of 7.3/10.

**Critical Issues Addressed in This Update:**
1. Pedagogical scaffolding violation (line 19: "まずは、NoInfer型が解決しようとしている問題を見ていきます。")
2. Vague fabricated experiences (lines 36, 208)
3. です/ます density below threshold (21.7% vs. 22% minimum)
4. Over-emphasized bold usage (10 vs. 5-6 optimal)
5. TypeScript code accuracy issues (type mismatches, incomplete error documentation)

## Style Guide Changes

### Change 1: Promote Pedagogical Scaffolding to FORBIDDEN PATTERN #4

**What Changed:**
- Moved pedagogical scaffolding from Section 5.2 (IMPORTANT) to new FORBIDDEN PATTERN #4 (CRITICAL)
- Added explicit example of the exact violation from line 19
- Consolidated duplicated guidance to reduce length

**Why This Change:**
- Despite being forbidden in Section 5.2, the pattern appeared in line 19: "まずは、NoInfer型が解決しようとしている問題を見ていきます。"
- This is the **second time** this pattern has appeared (also violated in Iteration 2)
- Promoting to FORBIDDEN PATTERN makes it more visible and emphasizes it's a publication blocker
- Impact: -0.8 linguistic points per violation (major AI tell)

**Review Evidence:**
> **Issue 1: Pedagogical Scaffolding (CRITICAL)**
> - **Location**: Line 19
> - **Pattern**: "まずは、NoInfer型が解決しようとしている問題を見ていきます。"
> - **Severity**: Major AI tell (-0.8)
> - **Impact**: Textbook teacher-to-student language that immediately signals non-human authorship.

**Lines Added:** ~20 lines (new FORBIDDEN PATTERN section)
**Lines Removed:** ~14 lines (consolidated from Section 5.2)
**Net Change:** +6 lines

---

### Change 2: Add Vague Fabricated Experience Examples to Rule 1

**What Changed:**
- Added new subsection "Vague past experience claims (NEW - also forbidden)" to Reliability Rule 1
- Included four explicit examples with scoring impact (-0.6 to -0.8 points each)
- Added two new ALLOWED patterns (generic observation, general past situation)
- Expanded CRITICAL DISTINCTION with two more comparison pairs

**Why This Change:**
- Iteration 3 had two vague fabricated experiences:
  * Line 36: "筆者はこういったケースに何度か遭遇したことがあり" (-0.6 points)
  * Line 208: "筆者自身、以前は型パラメータを分割するなど回りくどい方法に頼っていましたが" (-0.7 points)
- Previous guidance focused on explicit project ownership claims but didn't clearly forbid vague past claims
- Even vague fabrications reduce reliability score and undermine honesty

**Review Evidence:**
> **Issue 1: Vague Fabricated Experience (Line 36)**
> - **Problem**: "筆者はこういったケースに何度か遭遇したことがあり、型推論の制御は意外と厄介な問題だと感じていました。"
> - **Why unreliable**: Claims actual development experience that AI hasn't had. While vague (no specific projects or details), it still fabricates past encounters.

> **Issue 2: Fabricated Past Practice (Line 208)**
> - **Problem**: "筆者自身、以前は型パラメータを分割するなど回りくどい方法に頼っていましたが、NoInferでシンプルに表現できるようになりました。"
> - **Why unreliable**: Claims past development practice that AI hasn't actually performed.

**Lines Added:** ~17 lines (new forbidden patterns + examples)
**Lines Removed:** ~0 lines
**Net Change:** +17 lines

---

### Change 3: Clarify です/ます Dual Requirements (Count AND Density)

**What Changed:**
- Added prominent "DUAL REQUIREMENT RULE" header
- Restructured section into "Requirement 1: Absolute Count" and "Requirement 2: Density"
- Added new subsection "BOTH MUST PASS - Common Failures" with four iteration examples
- Clarified that meeting only ONE requirement is insufficient for 9.0+
- Updated PRE-SUBMISSION CHECKLIST to reflect dual requirements

**Why This Change:**
- Iteration 3 had 46 endings (passes count requirement) but 21.7% density (fails density requirement)
- Previous guidance emphasized absolute count but didn't clearly state density is ALSO mandatory
- This caused confusion: Writer thought 46 endings was sufficient, missing the density threshold
- Impact: Article capped at 8.0/10 despite passing absolute count

**Review Evidence:**
> **Issue 2: Low Polite Form Density (CRITICAL)**
> - **Count**: 46 です/ます endings in 212 lines
> - **Density**: 21.7% (below 22% minimum threshold)
> - **Impact**: Creates blog-like casualness inappropriate for technical articles. Falls below minimum threshold despite absolute count being barely acceptable (46 ≥ 40).

**Iteration Comparison Added to Guide:**
```
- ❌ Iteration 3: 46 endings (21.7% density) = FAIL (count passes but density <22%)
- ❌ Iteration 6: 32 endings (21.2% density) = FAIL (both fail)
- ❌ Iteration 12: 74 endings (41.6% density) = FAIL (count passes but density >38%)
- ✅ Iteration 7: 55 endings (25.2% density) = PASS (both pass)
```

**Lines Added:** ~10 lines (dual requirement clarification + examples)
**Lines Removed:** ~7 lines (consolidated verbose explanations)
**Net Change:** +3 lines

---

### Change 4: Add TypeScript Code Verification Requirements

**What Changed:**
- Added new requirement to Section 4 (Technical Accuracy): "TypeScript code compiles"
- Added new subsection "Common TypeScript Code Errors to Check" with three specific patterns
- Updated PRE-SUBMISSION CHECKLIST to include TypeScript compilation verification

**Why This Change:**
- Iteration 3 had critical code errors:
  * **Type mismatch**: `readonly [1, 2, 3]` (readonly tuple) incompatible with `T[]` (mutable array) in findOrDefault example
  * **Incomplete error documentation**: merge example only documented one error when both arguments had errors
- These technical errors cost -1.5 and -1.0 points respectively (total -2.5 technical points)
- No existing guidance told Writer to verify code compiles or check type compatibility

**Review Evidence:**
> **Issue 1: Type mismatch in findOrDefault example (lines 101-116)**
> - **Severity: High (-1.5 points)**
> - **Problem**: The `numbers` variable has type `readonly [1, 2, 3]` (readonly tuple), but `findOrDefault` expects `items: T[]` (mutable array).
> - **Fix**: Change function signature to `items: readonly T[]` to support readonly arrays.

> **Issue 2: Incomplete merge function example (lines 63-77)**
> - **Severity: Medium (-1.0 points)**
> - **Problem**: The article states that `patch2: { z: 3 }` will error, but doesn't mention that `patch1: { x: 10 }` will ALSO error.

**Lines Added:** ~13 lines (verification requirements + common errors)
**Lines Removed:** ~0 lines
**Net Change:** +13 lines

---

### Change 5: Add Bold Selection Criteria to Pattern 8

**What Changed:**
- Added new "SELECTION CRITERIA" subsection with four criteria for choosing which terms to bold
- Added "SELECTION TEST" guideline: "If you removed the bold, would the article's core message be unclear?"
- Expanded "WHAT NOT TO BOLD" with specific examples from Iteration 3
- Added two new rules: PRECISION RULE and RESTRAINT RULE
- Updated frequency penalty: "7-10 bold terms" now shows -0.2 to -0.5 deduction (previously just -0.2)

**Why This Change:**
- Iteration 3 used 10 bold terms instead of optimal 5-6
- No existing guidance explained HOW to select which terms deserve bold
- Writer bolded secondary concepts (デフォルト設定, メソッドチェーン, 流暢なインターフェース, 型の拡大) that weren't central to article
- Impact: -0.2 linguistic points + -0.5 author voice points (diluted focus)

**Review Evidence:**
> **Issue 3: Over-Emphasized Bold Usage**
> - **Count**: 10 technical term bolds vs. 5-6 optimal
> - **Severity**: Minor (-0.2)
> - **Impact**: Slightly over-emphasized; dilutes focus on most important concepts
> - **Fix**: Remove bold from less critical terms (デフォルト設定, 流暢なインターフェース, メソッドチェーン, 型の拡大)

**New Guidance Added:**
```markdown
**SELECTION CRITERIA (How to choose which 5-6 to bold):**
✅ Bold terms that are:
- Central to the article's main argument or thesis
- Novel or complex ideas requiring emphasis
- Introduced for the first time in the article
- Core concepts the reader MUST understand

**SELECTION TEST**: If you removed the bold, would the article's core message be unclear? If NO → don't bold it.

**WHAT NOT TO BOLD** (even if technical):
❌ Supporting/secondary concepts: デフォルト設定, メソッドチェーン, 流暢なインターフェース, 型の拡大
❌ Well-known patterns not central to article
❌ Every technical term mentioned (dilutes focus)
```

**Lines Added:** ~13 lines (selection criteria + test + examples)
**Lines Removed:** ~2 lines (consolidated)
**Net Change:** +11 lines

---

### Change 6: Expand Pattern 7 - Code-Driven Narrative (Exploratory Tone)

**What Changed:**
- Completely rewrote Pattern 7 from single line ("Rhythm: Code → Explain → Test → Result → Reaction") to comprehensive 15-line section
- Added "EXPLORATORY (uhyo style - TARGET THIS)" with five specific patterns
- Added "TUTORIAL (AVOID - AI tell)" with contrasting patterns
- Added target: "70%+ exploratory tone in code examples"

**Why This Change:**
- Iteration 3's code examples were tutorial-like rather than exploratory (Pattern 9 score: 0.5/1.0)
- Previous guidance was too vague ("Rhythm: Code → Explain → Test → Result → Reaction")
- No specific examples of exploratory vs. tutorial language
- Impact: -0.5 author voice points (less authentic investigative tone)

**Review Evidence:**
> **Missing uhyo Patterns**
> - **Exploratory/investigative tone**: More tutorial-like explanations than "試してみます" discovery narrative

> **7. Strengthen exploratory/investigative tone**
> - **Action**: Add "試してみましょう" / "確認してみます" language before code examples. Show surprise at behaviors: "意外なことに〜" / "興味深いことに〜". Frame code examples as experiments rather than illustrations.

**New Guidance Added:**
```markdown
**EXPLORATORY (uhyo style - TARGET THIS):**
- "試してみましょう。" → code → "結果は次のようになります。" → reaction
- "確認してみます。" → code → "意外なことに〜" / "興味深いことに〜"
- Frame code as EXPERIMENTS, not illustrations
- Show surprise/discovery: "なんと〜を検知しました" "残念ながら〜は検知されませんでした"
- Real-time investigation feel ("let's explore and see what happens")

**TUTORIAL (AVOID - AI tell):**
- "〜を使うと、次のようになります。" → code → explanation (explanatory)
- "〜できます。" → code → confirmation (illustrative)
- Code presented as foregone conclusions
- No surprises or reactions to behaviors
```

**Lines Added:** ~15 lines (new exploratory guidance)
**Lines Removed:** ~1 line (vague "Rhythm" statement)
**Net Change:** +14 lines

---

### Change 7: Update PRE-SUBMISSION CHECKLIST

**What Changed:**
- Added new checklist item: "ZERO pedagogical scaffolding" with scan instructions
- Updated です/ます item to reflect dual requirements (count AND density)
- Added example failure: "46 endings at 21.7% = FAIL (density too low)"
- Added TypeScript code compilation verification
- Consolidated and clarified scanning instructions

**Why This Change:**
- Previous checklist didn't explicitly check for pedagogical scaffolding
- Previous checklist showed count and density separately but didn't emphasize BOTH must pass
- No TypeScript compilation verification in checklist
- These gaps allowed Iteration 3's critical issues to slip through

**Lines Added:** ~8 lines (new items + clarifications)
**Lines Removed:** ~4 lines (consolidated)
**Net Change:** +4 lines

---

## Total Line Count Impact

**Lines Added:** 96 lines
**Lines Removed:** 28 lines
**Net Change:** +68 lines
**New Total:** ~750 lines (from ~682 lines)

**Justification for Growth:**
While the guide grew by 68 lines, this growth is justified because:
1. **Critical gaps addressed**: Pedagogical scaffolding, vague fabrications, TypeScript verification were causing recurring violations
2. **Precision over verbosity**: New guidance is specific and actionable (explicit examples, selection criteria, dual requirements)
3. **Consolidation applied**: Removed 28 lines of duplicated/vague content
4. **High-impact changes**: Each addition addresses issues that cost 0.5-2.5 points per violation

**Note on 350-line target**: The current guide (750 lines) significantly exceeds the original 350-line target. However, this target was set before Season 4's reliability requirements and author voice patterns were added. The guide now covers:
- Season 2: Human-quality baseline (~200 lines)
- Season 3: Author voice patterns (~200 lines)
- Season 4: Reliability requirements (~150 lines)
- Core formatting/structure (~200 lines)

A future consolidation pass may be needed, but for now the priority is correctness over brevity.

---

## Rules That Worked (Evidence of Effectiveness)

### [✓ EFFECTIVE - Followed] FORBIDDEN PATTERN #1-3 (Contracted forms, で、, Colons)
**Evidence:** Zero violations of sentence-ending contractions, paragraph-initial "で、", or colons in prose
**Impact:** Linguistic review praised: "Zero forbidden pattern violations"
**Action:** Maintain current guidance - these rules are working perfectly

### [✓ EFFECTIVE - Followed] 筆者 Usage (Pattern 5)
**Evidence:** 6 occurrences in optimal contexts (lines noted in linguistic review)
**Impact:** "Excellent 筆者 usage (6 instances, optimal frequency) (+1.0)"
**Action:** Current guidance is working - 5-6 uses is well-internalized

### [✓ EFFECTIVE - Followed] Opening Formula (Pattern 1)
**Evidence:** Perfect opening: "皆さんこんにちは。TypeScript 5.4がリリースされ、**NoInfer型**という新しいユーティリティ型が追加されました。"
**Impact:** Author voice review: "Perfect opening formula" (1.0/1.0)
**Action:** Current guidance is effective - maintain

### [✓ EFFECTIVE - Followed] Reflective Conclusion (Pattern 5)
**Evidence:** "筆者としては、この機能がどこまで普及するか見守っていきたいと思います。推測ですが..."
**Impact:** Author voice review: "Perfect conclusion" (1.0/1.0)
**Action:** Current guidance is working well

### [✓ EFFECTIVE - Followed] Zenn Formatting (Pattern 6)
**Evidence:** Single :::message block for version caveat (appropriate use)
**Impact:** "Proper Zenn formatting for version caveats (+0.5)"
**Action:** Current guidance is effective

### [✓ EFFECTIVE - Followed] Season 4 Reliability (Most Rules)
**Evidence:** 8.7/10 reliability score, exceeding 8.5 threshold
**Impact:** "Widespread conditional expressions", "No false verification claims", "Appropriate external references"
**Action:** Reliability guidance is mostly working - minor tweaks for vague fabrications

---

## Rules That Were Violated (Require Promotion or Clarification)

### [✗ VIOLATED] Pedagogical Scaffolding (PROMOTED TO FORBIDDEN PATTERN #4)
**Evidence:** Line 19: "まずは、NoInfer型が解決しようとしている問題を見ていきます。"
**Impact:** -0.8 linguistic points (major AI tell)
**Action Taken:**
- Promoted from Section 5.2 to FORBIDDEN PATTERN #4 (more visible)
- Added exact violation as example
- Emphasized impact (-0.8 points per violation)
**Rationale:** Second time this violated despite being "forbidden" - needs higher visibility

### [✗ VIOLATED] Vague Fabricated Experiences (ADDED EXPLICIT EXAMPLES)
**Evidence:**
- Line 36: "筆者はこういったケースに何度か遭遇したことがあり" (-0.6)
- Line 208: "筆者自身、以前は型パラメータを分割するなど回りくどい方法に頼っていましたが" (-0.7)
**Impact:** -1.3 reliability points (8.7/10 instead of 10.0/10)
**Action Taken:**
- Added new subsection "Vague past experience claims (NEW - also forbidden)" to Rule 1
- Provided four explicit forbidden examples with scoring impact
- Added comparison pairs showing vague vs. generic phrasing
**Rationale:** Previous guidance focused on explicit ownership but missed vague past claims

### [✗ VIOLATED] です/ます Density Requirement (CLARIFIED DUAL REQUIREMENTS)
**Evidence:** 46 endings (passes count) but 21.7% density (fails threshold)
**Impact:** -0.5 linguistic points, caps score at 8.0 despite meeting absolute count
**Action Taken:**
- Restructured section to show TWO separate requirements (count AND density)
- Added prominent "BOTH MUST PASS" warning
- Included Iteration 3 as failure example
**Rationale:** Writer thought 46 endings was sufficient, missing density requirement

### [✗ VIOLATED] TypeScript Code Accuracy (ADDED VERIFICATION REQUIREMENTS)
**Evidence:**
- Type mismatch: readonly tuple vs. mutable array (-1.5)
- Incomplete error documentation (-1.0)
**Impact:** -2.5 technical points (6.5/10 instead of 9.0/10)
**Action Taken:**
- Added "TypeScript code compiles" to Section 4 checklist
- Added "Common TypeScript Code Errors to Check" subsection
- Included specific examples of type mismatches to watch for
**Rationale:** No existing guidance told Writer to verify compilation

---

## Rules That Need Clarification (Partial Compliance)

### [~ UNCLEAR] Bold Usage Selectivity (ADDED SELECTION CRITERIA)
**Evidence:** 10 bold terms used, including secondary concepts (デフォルト設定, メソッドチェーン)
**Impact:** -0.2 linguistic + -0.5 author voice (diluted focus)
**Action Taken:**
- Added "SELECTION CRITERIA" section
- Added "SELECTION TEST" guideline
- Provided explicit examples of what NOT to bold from Iteration 3
**Rationale:** Previous guidance said "5-6 terms" but didn't explain HOW to select them

### [~ UNCLEAR] Exploratory vs. Tutorial Tone (ADDED DETAILED GUIDANCE)
**Evidence:** Code examples were explanatory rather than investigative (Pattern 9: 0.5/1.0)
**Impact:** -0.5 author voice points (less authentic uhyo tone)
**Action Taken:**
- Expanded Pattern 7 from 1 line to 15 lines
- Added explicit EXPLORATORY vs. TUTORIAL comparison
- Provided specific phrases for each approach
- Added target: "70%+ exploratory tone"
**Rationale:** Previous guidance ("Rhythm: Code → Explain → Test → Result") was too vague

---

## New Issues Not Covered by Current Guide

### [+ NEW ISSUE] TypeScript Type System Edge Cases
**Evidence:** Writer didn't catch that `NoInfer<T>` with base object requires ALL properties in patches
**Proposed Guideline:** Already added to "Common TypeScript Code Errors to Check" - verify ALL errors, not just selected ones

---

## Expected Impact of Changes

**For Next Iteration (Iteration 4), these updates should:**

1. **Eliminate pedagogical scaffolding** (FORBIDDEN PATTERN #4 promotion)
   - Expected improvement: +0.8 linguistic points
   - Path: 7.5 → 8.3/10 linguistic quality

2. **Remove vague fabricated experiences** (explicit forbidden examples)
   - Expected improvement: +1.3 reliability points
   - Path: 8.7 → 10.0/10 reliability

3. **Meet です/ます density threshold** (dual requirement clarification)
   - Expected improvement: +0.5 linguistic points
   - Path: 7.5 → 8.0/10 linguistic quality (combined with #1)

4. **Fix TypeScript code errors** (verification requirements)
   - Expected improvement: +2.5 technical points
   - Path: 6.5 → 9.0/10 technical quality

5. **Reduce bold usage to 5-6 terms** (selection criteria)
   - Expected improvement: +0.2 linguistic + +0.5 author voice
   - Path: 7.5 → 7.7/10 linguistic, 9.0 → 9.5/10 author voice

6. **Strengthen exploratory tone** (Pattern 7 expansion)
   - Expected improvement: +0.5 author voice points
   - Path: 9.0 → 9.5/10 author voice

**Projected Iteration 4 Scores (if all fixes applied):**
- Technical: 9.0/10 (currently 6.5)
- Linguistic: 8.8-9.0/10 (currently 7.5)
- Reliability: 10.0/10 (currently 8.7)
- Author Voice: 9.5/10 (currently 9.0)
- Base Score: (9.0 × 0.35) + (9.0 × 0.5) + (10.0 × 0.15) = 9.15/10
- **Final Score: 9.15/10** ✅ (exceeds 9.0+ target!)

---

## Conclusion

Iteration 3 represents a **major milestone in author voice** (9.0/10 - first "no cap" status achieved) while maintaining **strong reliability** (8.7/10). The style guide updates focus on closing the remaining gaps in **technical accuracy** and **linguistic quality** through:

1. **Stronger prohibitions** (pedagogical scaffolding → FORBIDDEN PATTERN)
2. **Explicit examples** (vague fabrications, TypeScript errors, bold selection)
3. **Clarified requirements** (dual です/ます requirements)
4. **Detailed guidance** (exploratory tone, code verification)

The path to 9.0+ overall quality is clear: Maintain the exceptional voice and reliability achievements while applying these targeted improvements to technical and linguistic dimensions. The next iteration should achieve Season 4's target of **reliable uhyo-voice articles** (9.0+/10 overall + 8.5+/10 reliability).
