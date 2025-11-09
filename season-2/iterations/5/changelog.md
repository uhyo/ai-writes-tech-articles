# Changelog: Style Guide v1.7 (Iteration 5)

## Executive Summary

**Version:** 1.6 → 1.7
**Focus:** Conceptual frameworks + expanded forbidden patterns
**Impact:** Surgical improvements addressing the last critical gap between AI and human articles

**Key Achievement from Iteration 5:**
- ✅ **POLITE FORM DISTRIBUTION FIXED!** (21% → 61.5%)
- The v1.6 clarifications successfully achieved the target range (40-60%)
- This proves the style guide's quantitative approach works

**Remaining Gaps Addressed in v1.7:**
1. Missing conceptual frameworks (0 in Iteration 5 article)
2. Last forbidden pattern violation (line 418: "使ってないけど。")
3. Too many sections (12 vs target 6-7)
4. Anecdotes too "medium-detail" (need rich OR vague extremes)

---

## Changes Made

### 1. Expanded Forbidden Pattern #1: Negative Contracted Forms (CRITICAL)

**Issue Found:** Line 418 of Iteration 5 article contained "使ってないけど。" - a sentence-ending negative contracted form not explicitly listed in forbidden patterns.

**Change:**
```diff
-### ❌ FORBIDDEN PATTERN #1: Sentence-ending -てる/-てた/-てます
+### ❌ FORBIDDEN PATTERN #1: Sentence-ending contracted forms

 **NEVER end a sentence (marked with 。) with these contracted forms:**

 ❌ "書いてた。" → ✅ "書いていました。" or "書きました。"
 ❌ "使ってる。" → ✅ "使っています。" or "使います。"
 ❌ "構成されてる。" → ✅ "構成されています。"
 ❌ "思ってる。" → ✅ "思っています。" or "思います。"
+❌ "使ってない。" → ✅ "使っていません。" or "使いません。" ⚠️ **NEW**
+❌ "書いてなかった。" → ✅ "書いていませんでした。"

-**Rule**: If -てる/-てた/-てます comes RIGHT BEFORE 。or 、at sentence end → FORBIDDEN
+**Rule**: If -てる/-てた/-てます/-てない/-てなかった comes RIGHT BEFORE 。or 、at sentence end → FORBIDDEN
```

**Updated Checklist:**
```diff
-ZERO sentence-ending -てる/-てた/-てます (scan: てる。てた。てます。)
+ZERO sentence-ending contracted forms (scan: てる。てた。てます。てない。てなかった。)
```

**Rationale:**
- Negative forms (-てない/-てなかった) are contractions of -ていない/-ていなかった
- These follow the same pattern as -てる/-てた (contracted progressive forms)
- Human articles NEVER end sentences with these forms
- The Iteration 5 violation shows this needs explicit coverage
- This is the LAST remaining forbidden pattern gap

**Impact:** CRITICAL - Eliminates the final forbidden pattern violation type. With this fix, articles can achieve 9.0+ scores.

---

### 2. Elevated Conceptual Frameworks (HIGH-VALUE ADDITION)

**Issue Found:**
- Iteration 5 article: 0 conceptual frameworks
- Human samples: 100% have 1-2 conceptual frameworks
- This is a consistent pattern difference across ALL iterations

**Change:**
```diff
-### 5.3 Conceptual Frameworks
+### 5.3 Conceptual Frameworks ⭐ HIGH-VALUE AUTHENTICITY MARKER

-Introduce higher-level concepts that REFRAME the discussion, not just explain HOW:
+**Human articles (100% of samples) introduce 1-2 higher-level concepts that REFRAME understanding, not just explain mechanics.**

-✅ Examples: "Promiseが一級市民ではなかった" "記憶領域を必要としないフック"
+✅ **Examples from human articles:**
+- "Promiseが一級市民ではなかった" (react-use-rfc.md)
+- "記憶領域を必要としないフック" (react-use-rfc.md)
+- "バンドルという工程それ自体が遅い" (native-esm-era.md)

-Create by: Notice pattern → name it (not in docs) → use it to explain why → reference later
+**How to create conceptual frameworks:**
+1. Notice a pattern or constraint in the technology
+2. Name it using terms NOT in official documentation
+3. Use it to explain WHY things work the way they do (not just HOW)
+4. Reference it later as conceptual shorthand
+
+**What this is NOT:**
+❌ Explaining step-by-step how something works
+❌ Describing official API behavior
+❌ Teaching patterns from documentation
+
+**What this IS:**
+✅ Creating meta-insights that reframe understanding
+✅ Naming implicit constraints or design philosophies
+✅ Revealing "why" through higher-level concepts
+
+**Target:** 1-2 conceptual frameworks per article (0 = major AI tell)
```

**Added to BASIC QUALITY checklist:**
```diff
 ### ✅ BASIC QUALITY
+- [ ] 1-2 conceptual frameworks (meta-insights that reframe understanding)
```

**Added to TOP AI TELLS:**
```diff
+5. **No conceptual frameworks** (need 1-2 meta-insights that reframe understanding)
```

**Rationale:**
- This is the difference between "teaching how" (AI) and "sharing insight" (human)
- Human writers naturally create higher-level concepts to explain "why"
- AI tends to stay at the practical "how-to" level
- This pattern appeared in 100% of human samples but 0% of AI iterations
- Elevating this from a minor point to a HIGH-VALUE marker signals its importance

**Impact:** HIGH - Addresses the most significant remaining authenticity gap. This is what separates encyclopedic explanations from insightful technical writing.

---

### 3. Strengthened Section Count Guidance (CRITICAL)

**Issue Found:**
- Iteration 5 article: 12 H2 sections
- Human articles: typically 6-7 sections
- Too many sections creates "comprehensive guide" feel vs "personal exploration"

**Change:**
```diff
-### 5.6 Non-Linear Structure
+### 5.6 Non-Linear Structure & Section Count

 **Don't tie everything together neatly:**
 [existing content...]

 **MANDATORY: 2-3 unresolved elements:**
 [existing content...]

-**Sections:** 6-7 H2 max, dramatically variable length (1-2 para vs 15+ para)
+**CRITICAL: Section Structure**
+- **Maximum 6-7 H2 sections** (8+ = encyclopedic/textbook feel)
+- **Avoid subsection hierarchies** (H3s listing patterns = pedagogical structure)
+- **Dramatically variable length:**
+  - Favorite topic: 10-15 paragraphs (deep dive on interesting simple thing)
+  - Boring but necessary: 2-3 sentences + "この辺は省略"
+  - Medium topics: 4-6 paragraphs
+- **Let interest dictate depth, not completeness**
+
+❌ AI tell: 10+ sections with even treatment (3-7 para each)
+✅ Human: 6 sections with wild variation (15 para, 2 para, 8 para, 3 para, 12 para, 5 para)
```

**Updated BASIC QUALITY checklist:**
```diff
-[ ] 6-7 H2 sections max
+[ ] **Maximum 6-7 H2 sections** (8+ = too granular/encyclopedic)
+[ ] No subsection hierarchies (H3 pattern lists = textbook structure)
```

**Added to TOP AI TELLS:**
```diff
+6. **Too many sections** (8+ sections = encyclopedic; TARGET: 6-7 max)
```

**Rationale:**
- 12 sections = comprehensive reference guide (encyclopedic)
- 6-7 sections = selective exploration (personal article)
- Iteration 5's structure felt like a textbook chapter, not a blog post
- The previous guidance was too soft ("6-7 H2 max" in checklist only)
- Strengthening this with explicit "CRITICAL" designation and concrete examples
- H3 subsections create hierarchical structure (pedagogical pattern)

**Impact:** MEDIUM-HIGH - Fixes the "too comprehensive" problem. Forces selective depth rather than comprehensive coverage.

---

### 4. Enhanced Anecdote Guidance with Spectrum (MEDIUM)

**Issue Found:**
- Iteration 5 anecdotes: "3時間くらい悩んだ記憶がある" (medium-detail)
- Human articles: Rich specifics OR vague uncertainty (extremes)
- The "safe middle ground" is an AI tell

**Change:**
```diff
 ### 5.5 Authentic Anecdotes

 **Not all stories need happy endings:**
 [existing content...]

-**Rich details OR vague, not medium:**
-- ❌ Medium: "去年あるプロジェクトで3日消費"
-- ✅ Rich: "社内の古いExpress API（100個エンドポイント）をTS化する無茶振りで詰まって3日溶けた"
-- ✅ Vague: "前に似たことやった気がする" "たぶん2019年くらい？"
+**Rich details OR vague, NOT medium:**
+
+**The spectrum:**
+- ❌ **Medium (AI tendency)**: "去年あるプロジェクトで3日消費" "3時間くらい悩んだ記憶がある"
+- ✅ **Rich (vivid)**: "社内の古いExpress API（100個エンドポイント）をTS化する無茶振りで詰まって3日溶けた"
+- ✅ **Vague (casual)**: "前に似たことやった気がする" "たぶん2019年くらい？"
+
+**Key insight:** Avoid "safe middle ground" where details are specific enough to be factual but not specific enough to be vivid. Go to extremes:
+- Rich: Name the project, count the files, describe the pain
+- Vague: Fuzzy memory, uncertain timeframe, no details
+
+❌ "最近のプロジェクトで使った" → medium, forgettable
+✅ "先月の配送管理システムリプレイスで50画面をTS化した" → rich, memorable
+✅ "前どこかで見た気がする" → vague, authentic uncertainty
```

**Rationale:**
- Iteration 5's "3時間くらい悩んだ記憶がある" is exactly the medium-detail problem
- It's specific enough to sound factual, but not specific/vivid enough to be memorable
- Humans go to extremes: either rich vivid details OR fuzzy vague recollections
- The expanded examples show the contrast more clearly
- Added actionable guidance: "Name the project, count the files, describe the pain"

**Impact:** MEDIUM - Improves anecdote authenticity. The spectrum visualization helps identify the "medium" trap.

---

## Summary of Changes

### Critical (Publication Blockers)
1. **Expanded forbidden patterns** to include -てない/-てなかった forms
   - Fixes last violation type from Iteration 5
   - Now covers ALL contracted sentence-ending forms

### High-Value Authenticity Markers
2. **Elevated conceptual frameworks** from minor point to HIGH-VALUE marker
   - Addresses the #1 remaining gap vs human articles
   - 100% of human samples have this, 0% of AI iterations had it
   - Detailed guidance on creating meta-insights vs explaining mechanics

3. **Strengthened section count** from soft suggestion to CRITICAL requirement
   - Maximum 6-7 sections (not 8-12)
   - Avoid subsection hierarchies (H3 pattern lists)
   - Dramatic depth variation by interest, not completeness

### Quality Improvements
4. **Enhanced anecdote spectrum** with vivid examples
   - Identifies "medium-detail" as AI tendency
   - Shows rich vs vague extremes clearly
   - Actionable guidance on creating vivid details

---

## Expected Impact on Next Iteration

### What Should Improve
1. **Zero forbidden pattern violations** (with -てない coverage)
2. **1-2 conceptual frameworks present** (now HIGH-VALUE marker)
3. **6-7 sections instead of 10+** (stronger guidance)
4. **Rich OR vague anecdotes** (not medium-detail)
5. **Maintained polite form 40-60%** (v1.6 success continues)

### Target Score: 8.7-9.0/10

**If all improvements achieved:**
- No forbidden patterns: +0.5 (from 8.0 to 8.5)
- 1-2 conceptual frameworks: +0.2 (major authenticity boost)
- 6-7 sections with depth variation: +0.2 (structure improvement)
- Rich/vague anecdotes: +0.1 (minor but visible)

**Total potential: 9.0/10** (publication quality achieved)

---

## Success Story: Polite Form Distribution

**Iteration 3:** 0 です/ます (0%) → 9.2/10 (WRONG score - reviewer error)
**Iteration 4:** 17 です/ます (21%) → 7.5/10 (correct score, but below target)
**Iteration 5:** 56 です/ます (61.5%) → **8.0/10 (SUCCESS!)**

The v1.6 two-tier system (MINIMUM 15+ vs TARGET 40-60%) achieved exactly the desired outcome:
- Clear quantitative targets work
- Concrete examples help execution
- "Main sentences polite, subordinate casual" rule is actionable

**Key Insight:** The jump from 21% to 61.5% proves that clear, quantitative guidelines with concrete examples drive measurable improvement.

---

## What Makes v1.7 Different

**v1.6 (Iteration 4):** Fixed the polite form crisis (TARGET clarification)
**v1.7 (Iteration 5):** Addresses authenticity depth (conceptual frameworks) + last critical violations

**Philosophy shift:**
- v1.6: Quantitative fixes (count です/ます)
- v1.7: Qualitative depth (create meta-insights)

**Maturity level:**
- The style guide is now approaching optimal state
- Forbidden patterns: fully covered (affirmative + negative forms)
- Structure patterns: strongly enforced (6-7 sections)
- Authenticity markers: deeply explained (conceptual frameworks elevated)
- Remaining work: **execution consistency**, not rule discovery

---

## Confidence Assessment

**High confidence changes:**
1. Expanded forbidden patterns (-てない forms) → **100% confident**
   - Clear violation found in Iteration 5
   - Follows same logic as existing forbidden patterns
   - Zero-tolerance is appropriate

2. Conceptual frameworks elevation → **95% confident**
   - 100% of human samples have this
   - 0% of AI iterations had this
   - Clear, actionable guidance provided
   - This is THE remaining authenticity gap

**Medium-high confidence changes:**
3. Strengthened section count → **85% confident**
   - 12 sections clearly too many
   - 6-7 is human baseline
   - May need fine-tuning based on topic complexity

4. Enhanced anecdote guidance → **80% confident**
   - Medium-detail is observable AI tendency
   - Rich vs vague extremes are clear in human samples
   - Execution may vary by writer interpretation

---

## Lines of Code

**Previous:** ~305 lines (v1.6)
**Current:** ~325 lines (v1.7)
**Target:** <350 lines
**Remaining budget:** 25 lines

The style guide is approaching its final form. v1.7 adds ~20 lines, primarily expanding the conceptual frameworks section (HIGH-VALUE addition worth the space).

---

## Next Iteration Predictions

**If Writer follows v1.7 guide successfully:**

**Expected scores:**
- Critical requirements: 38-40/40 (near-perfect with -てない fix)
- Authenticity markers: 28-30/30 (conceptual frameworks add major value)
- Human-like patterns: 16-18/20 (section count + frameworks boost)
- Technical quality: 10/10 (maintained)

**Total: 92-98/100 → 9.2-9.8/10**

**Likely outcome:** 8.7-9.0/10 (conservative estimate accounting for execution variability)

**Status:** Publication-quality threshold (8.5+) should be achieved in Iteration 6 if v1.7 guidance is followed.

---

## Changelog Metadata

**Date:** Iteration 5 review completion
**Style Guide Version:** 1.6 → 1.7
**Changes:** 4 major updates (1 critical, 2 high-value, 1 quality improvement)
**Lines added:** ~20 lines
**Focus:** Conceptual depth + final forbidden pattern coverage
**Expected impact:** +0.7-1.0 point improvement in next iteration
