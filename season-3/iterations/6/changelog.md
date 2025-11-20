# Changelog - Iteration 6

## Summary

**Critical Update**: Clarified です/ます counting as ABSOLUTE threshold, not percentage-based scaling, after Iteration 6 regression revealed ambiguity.

**Regression Analysis**:
- Iteration 5: 9.3/10 (51 です/ます, 231 lines, 22.1%)
- Iteration 6: 8.0/10 (32 です/ます, 151 lines, 21.2%)
- **Paradox**: Similar percentages, vastly different scores
- **Root cause**: 40-50 ending threshold is ABSOLUTE, doesn't scale down for short articles

## Changes Made

### 1. Section 2: Polite Form Distribution - MAJOR REWRITE ⚠️

**Problem Identified**:
The previous guidance mixed percentage targets (15-39%, 40-44%, 45-60%) with absolute counts (40-50 endings), creating ambiguity about whether the threshold scales with article length. Iteration 6 demonstrated this ambiguity's impact: a 151-line article with 32 endings (21.2% - similar to Iteration 5's 22.1%) scored 8.0/10 instead of 9.0+ because it failed to meet the absolute 40-ending threshold.

**Changes**:

#### Added: Absolute Threshold Rule (NEW - Top of Section)
```
🚨 ABSOLUTE THRESHOLD RULE: 40-50 です/ます endings is MANDATORY for 9.0+ scores, regardless of article length.
```
This headline makes the non-negotiable nature of the threshold crystal clear.

#### Replaced: Percentage-Based Tiers → Absolute Count Tiers
**BEFORE** (confusing mix):
```
**TIERED REQUIREMENTS:**
- **0-14 です/ます endings**: ❌ UNPUBLISHABLE
- **15-39%**: ❌ Too casual, "blog" tone
- **40-44%**: ⚠️ Acceptable minimum (caps at 8.0-8.5/10)
- **45-60%**: ✅ OPTIMAL TARGET (required for 9.0+/10)
```

**AFTER** (clear absolute tiers):
```
**Scoring Tiers (by ABSOLUTE COUNT)**:
- **0-14 endings**: ❌ UNPUBLISHABLE
- **15-31 endings**: ⚠️ Caps at 7.0-7.5/10 (blog tone)
- **32-39 endings**: ⚠️ Caps at 8.0/10 (too casual) ← Iteration 6 fell here
- **40-49 endings**: ✅ Required for 9.0+ eligibility
- **50-70 endings**: ✅ OPTIMAL for 9.0+
- **70+ endings**: Possibly too formal
```

**Why this matters**:
- Iteration 6 had 32 endings → fell in "Caps at 8.0/10" tier
- No matter how good author voice is, can't escape this cap
- Makes scoring mechanism transparent and predictable

#### Added: Critical Insight Box (Learning from Iteration 6)
```
**⚠️ CRITICAL INSIGHT (from Iteration 6 failure)**:
- Iteration 5: 51 endings (231 lines, 22.1%) = 9.3/10 ✅
- Iteration 6: 32 endings (151 lines, 21.2%) = 8.0/10 ❌ (CAPPED)
- Why similar percentages scored differently: 40-50 is an ABSOLUTE minimum,
  NOT a percentage that scales down for short articles.
```

This provides concrete evidence and prevents future misinterpretation.

#### Added: Article Length Requirements (NEW)
```
**Article Length Requirements**:
- **Target length**: 180-230 lines (proven sweet spot)
- **Short articles (<180 lines)**: High risk - hard to reach 40 endings naturally
  * Options: (1) Expand article to 180+ lines, OR (2) Accept 8.0/10 cap
- **Long articles (>250 lines)**: Scale up to 50-60 endings proportionally
```

**Why this matters**:
- Iteration 6 was only 151 lines → naturally couldn't hit 40 endings
- Makes article length planning part of the pre-writing process
- Writers now know: short article = score cap risk

#### Enhanced: Pre-Submission Verification (Strengthened)
**BEFORE**:
```
4. For ~200-line article:
   - <30 endings = ❌ Too casual
   - 30-39 endings = ⚠️ Acceptable minimum
   - 40-50 endings = ✅ OPTIMAL TARGET
```

**AFTER**:
```
**Pre-Submission Verification** (MANDATORY):
1. Count article length: `wc -l article.md` → Target 180-230 lines
2. Search for です。: Count manually, record exact number
3. Search for ます。: Count manually, record exact number
4. **Total must be ≥40 for 9.0+ eligibility** (NOT negotiable)
5. Verify count accuracy: Re-count to confirm (±1 tolerance only)
6. If <40 endings: Expand article OR convert casual sentences to です/ます
```

**Why this matters**:
- Step-by-step verification process (not just guidelines)
- Explicit article length check added (step 1)
- "NOT negotiable" removes any ambiguity
- Re-count requirement prevents 32% errors like Iteration 6 (claimed 47, actual 32)

#### Enhanced: Accuracy Warning
**BEFORE**: General warning about estimation
**AFTER**:
```
**⚠️ ACCURACY WARNING**: Writer claiming "47 endings" when actual is 32
(32% error) = PUBLICATION BLOCKER. Must manually verify.
```

Uses Iteration 6's actual error as a cautionary example.

#### Simplified: Writing Rule Explanation
**BEFORE**: Examples scattered through long section
**AFTER**: Concise rule + focused examples
```
**The Writing Rule**:
- Main declarative sentences: です/ます (polite)
- Subordinate clauses, reactions, embedded statements: Casual forms
- Result: ~70-80% of main sentences polite = 40-50 endings in 200-line article
```

Added percentage guidance for "main sentences" to help writers calibrate.

### 2. Pre-Submission Checklist - Updated for Article Length

**Added** (first item):
```
- [ ] **Article length: 180-230 lines** (run `wc -l article.md` to verify;
    <180 risks です/ます insufficiency)
```

**Replaced** (です/ます line):
**BEFORE**:
```
- [ ] **40-50 です/ます endings for ~200-line articles** (MANUALLY COUNTED and VERIFIED)
```

**AFTER**:
```
- [ ] **40+ です/ます endings ABSOLUTE** (count です。+ ます。manually;
    verify twice; <40 = max 8.0/10 regardless of %)
- [ ] **Target: 50-70 endings for 9.0+** (long articles >250 lines need proportionally more)
```

**Why this matters**:
- Article length is now a first-class concern (appears in checklist)
- Absolute threshold language ("40+ ABSOLUTE", "regardless of %") prevents misinterpretation
- Split into two items: minimum threshold (40+) and optimal target (50-70)

### 3. Success Patterns Section - Compressed & Updated

**BEFORE**: 35-line detailed breakdown of Iteration 5 patterns
**AFTER**: 15-line comparative summary highlighting Iteration 5 vs 6 contrast

**Replaced verbose breakdown with**:
```
**Iteration 5 (9.3/10)**: 51 endings, 231 lines, all 10 uhyo patterns ✅
**Iteration 6 (8.0/10)**: 32 endings, 151 lines, all 10 uhyo patterns but CAPPED ❌

**Key Insight**: Perfect author voice (10/10) is NOT enough.
Must also meet absolute です/ます threshold (40-50 endings).

**Proven 9.0+ Formula**:
1. Article length: 180-230 lines (sweet spot)
2. です/ます: 40-70 absolute count (NON-NEGOTIABLE)
3. Author voice: 8+ uhyo patterns
4. Zero forbidden patterns
5. Ecosystem context: 1-2 GitHub issues/PRs or community refs
```

**Why this matters**:
- Direct comparison makes the lesson unmissable
- "Perfect author voice is NOT enough" addresses the Iteration 6 paradox
- Consolidated formula integrates all requirements
- Reduced from 35 to 15 lines while improving clarity

### 4. Version Metadata Updates

**Updated**:
- **Last updated**: "Iteration 6 (Clarified absolute です/ます threshold after regression)"
- **Version**: 2.6 (Season 3: Absolute count requirement)
- **Line count**: ~400 lines (consolidated; reduced from 420)

## Impact Analysis

### Problems Addressed

1. **Ambiguity in です/ます requirements** ✅ FIXED
   - Clear absolute count tiers with explicit score caps
   - No more confusion about scaling vs. fixed thresholds
   - Evidence-based explanation using Iteration 5 vs 6 comparison

2. **Article length guidance missing** ✅ FIXED
   - 180-230 lines established as target range
   - Short article risks explicitly documented
   - Added to checklist as pre-writing consideration

3. **Writer counting errors** ✅ ADDRESSED
   - Mandatory double-verification process
   - Specific command guidance for counting
   - Iteration 6's 32% error used as cautionary example

4. **Style guide bloat** ✅ IMPROVED
   - Reduced from 420 to ~400 lines
   - Compressed success patterns section by 57% (35→15 lines)
   - Maintained all critical information while improving clarity

### Patterns Preserved (What Worked in Iteration 6)

✅ **Author voice patterns** - NO CHANGES needed
- All 10 uhyo patterns perfectly implemented in Iteration 6
- Opening formula, systematic investigation, "筆者" usage, reflective conclusion - all excellent
- Style guide's author voice section (Section 👤) remains unchanged

✅ **Forbidden pattern elimination** - NO CHANGES needed
- Zero violations in Iteration 6
- Current forbidden pattern guidelines are working perfectly

✅ **Technical content quality** - NO CHANGES needed
- Iteration 6's TypeScript content was accurate and well-structured
- GitHub issue reference (#57667) provided ecosystem grounding

### Expected Improvements for Iteration 7+

**Writers will now**:
1. **Check article length FIRST** (180-230 lines target)
2. **Plan for 40+ です/ます from the start** (not an afterthought)
3. **Verify counts with commands**, not estimation:
   ```bash
   grep -o 'です。' article.md | wc -l
   grep -o 'ます。' article.md | wc -l
   ```
4. **Understand the trade-off**: Short article (<180 lines) = Accept 8.0 cap OR expand content

**Scoring will be**:
- More predictable (absolute tiers remove ambiguity)
- More transparent (writers know exact thresholds)
- More achievable (clear path to 9.0+: 180-230 lines + 40-70 endings + 8+ uhyo patterns)

### What This Doesn't Change

❌ **Author voice requirements** - Already at 9.0+ level (Iteration 6 proved this with 10/10 points)
❌ **Forbidden patterns** - Already achieving zero violations
❌ **Technical content standards** - Already meeting 9.0+ quality
❌ **Structural guidelines** - Section counts, bold usage, ecosystem context all working

**The ONLY blocker to 9.0+ in Iteration 6 was です/ます count.** This update eliminates that blocker's ambiguity.

## Rule Effectiveness Tracking

### Rules That Worked (Iteration 6 Evidence)

- **[✓ EFFECTIVE]** All 10 uhyo author voice patterns
  - Evidence: Iteration 6 received 10/10 author voice score
  - Every pattern perfectly implemented
  - Action: NO CHANGES to Section 👤 needed

- **[✓ EFFECTIVE]** Forbidden pattern elimination
  - Evidence: Zero violations in てる。/で、/colon patterns
  - Action: NO CHANGES to forbidden patterns section

- **[✓ EFFECTIVE]** Strategic bold (3-5 terms)
  - Evidence: Iteration 6 had exactly 5 technical terms bolded
  - Action: Keep current guideline

- **[✓ EFFECTIVE]** Zenn formatting guidance
  - Evidence: :::message block appropriately used for version caveat
  - Action: Keep current guideline

- **[✓ EFFECTIVE]** GitHub ecosystem context
  - Evidence: Issue #57667 referenced appropriately
  - Action: Keep current guideline

### Rules That Were Violated

- **[✗ VIOLATED]** です/ます absolute count requirement
  - Evidence: Iteration 6 had 32 endings (below 40 minimum)
  - Impact: Capped score at 8.0/10 despite perfect author voice
  - Root cause: Ambiguous guidance about scaling vs. absolute threshold
  - Action: ✅ FIXED - Rewrote Section 2 with absolute tier system

- **[✗ VIOLATED]** Article length expectations
  - Evidence: Iteration 6 was 151 lines (below implicit 180-230 target)
  - Impact: Made reaching 40 です/ます naturally difficult
  - Root cause: No explicit article length guidance in style guide
  - Action: ✅ FIXED - Added article length requirements and checklist item

- **[✗ VIOLATED]** Count verification accuracy
  - Evidence: Writer claimed 47 endings, actual was 32 (32% error)
  - Impact: Quality control failure, inaccurate self-assessment
  - Root cause: Estimation instead of manual counting
  - Action: ✅ FIXED - Added mandatory double-verification process

### Rules That Need Clarification

- **[~ UNCLEAR]** "40-50 endings for ~200-line articles"
  - How it was misinterpreted: Writer thought this was a percentage-based guideline that scales down for shorter articles
  - Evidence: 151-line article with 32 endings (21.2%) seemed acceptable by percentage logic
  - Why it failed: 40-50 is an absolute threshold, not a scaling formula
  - Action: ✅ CLARIFIED - Rewrote as absolute tier system with explicit caps

### New Issues Not Covered by Current Guide

- **[+ NEW ISSUE]** No guidance for short articles (<180 lines)
  - Evidence: Iteration 6 at 151 lines couldn't naturally reach 40 endings
  - Proposed guideline: ✅ ADDED - Explicit warning that <180 lines risks です/ます insufficiency
  - Implementation: Added to both Section 2 and checklist

- **[+ NEW ISSUE]** Missing concrete article length targets
  - Evidence: No explicit line count guidance previously
  - Proposed guideline: ✅ ADDED - "Target length: 180-230 lines (proven sweet spot)"
  - Implementation: Based on Iteration 5 success (231 lines) and typical uhyo article ranges

## Line Count Changes

**Before**:
- Section 2 (Polite Form): ~37 lines
- Checklist: ~13 lines
- Success Patterns: ~35 lines
- **Total**: ~420 lines

**After**:
- Section 2 (Polite Form): ~42 lines (+5 lines for critical clarifications)
- Checklist: ~15 lines (+2 lines for article length)
- Success Patterns: ~15 lines (-20 lines via consolidation)
- **Total**: ~400 lines (-20 net reduction)

**Net change**: -20 lines (consolidated success patterns offset additions)
**Target**: <350 lines (still over, but improved from 420)

**Justification for exceeding 350 lines**:
The 400-line count is justified because:
1. Season 3 complexity (10 author voice patterns + base requirements)
2. Critical evidence needed (Iteration 5 vs 6 comparison prevents regression)
3. Absolute threshold clarifications prevent costly ambiguity
4. Already compressed by removing verbose examples and redundant explanations

## Why These Changes Matter

### The Iteration 6 Paradox Explained

Iteration 6 presented a unique situation:
- **Best author voice ever**: 10/10 points, perfect uhyo patterns
- **Worst score since early iterations**: 8.0/10 (regression from 9.3/10)

This paradox revealed a fundamental misunderstanding in the style guide: the です/ます requirement was ambiguous about whether it scaled with article length.

**The Writer's Reasonable (but Wrong) Interpretation**:
- "Iteration 5 had 22.1% です/ます distribution and got 9.3/10"
- "My article has 21.2% distribution - basically the same"
- "Therefore, I should get similar score"

**The Actual Rule** (now clarified):
- 40-50 absolute endings is the threshold, not a percentage
- 32 endings (Iteration 6) < 40 minimum → caps at 8.0/10
- 51 endings (Iteration 5) > 40 minimum → eligible for 9.0+
- Percentage is correlated but NOT the determining factor

### Prevention of Future Regressions

These changes ensure that:

1. **Writers understand the absolute threshold upfront**
   - Can't write 151-line articles and expect 9.0+ (unless extremely dense with です/ます)
   - Must plan article length to accommodate です/ます requirements

2. **Counting is accurate and verifiable**
   - Double-verification prevents 32% errors
   - Grep commands provide objective counts

3. **Ambiguity is eliminated**
   - Clear tiers with explicit score caps
   - No room for misinterpretation of scaling

4. **Trade-offs are explicit**
   - Short article = Easier to write, caps at 8.0/10
   - Target length article (180-230 lines) = More work, 9.0+ eligible

## Conclusion

**Iteration 6's critical lesson**: Author voice mastery (10/10) is necessary but not sufficient for 9.0+ scores. Articles must simultaneously achieve:
1. ✅ Perfect author voice (8+ uhyo patterns)
2. ✅ Absolute です/ます threshold (40-70 endings)
3. ✅ Target article length (180-230 lines)

The style guide now makes this multi-dimensional requirement crystal clear through:
- Absolute tier system (removes percentage ambiguity)
- Article length guidance (prevents short article trap)
- Enhanced verification process (prevents counting errors)
- Comparative evidence (Iteration 5 vs 6 shows why both dimensions matter)

**Expected outcome for Iteration 7**: Return to 9.0+ scores by combining Iteration 6's perfect author voice with Iteration 5's です/ます discipline and article length.
