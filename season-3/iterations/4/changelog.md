# Changelog - Iteration 4

## Summary

Iteration 4 achieved 8.5/10 overall, limited by **dual caps**: both base score (polite form density) and author voice score (pattern intensity) capped at 8.5. The article demonstrated strong uhyo voice fundamentals but with lower intensity than typical. Key improvements focus on **self-checking accuracy**, **pattern intensity guidance**, and **precision refinements**.

**Critical Discovery**: Writer claimed 47 です/ます endings but actual count was 31 - highlighting need for explicit self-verification procedures.

---

## Changes Made to Style Guide

### 1. Enhanced です/ます Self-Checking Procedure (CRITICAL)

**Location**: Section 2 - Polite Form Distribution

**Problem Identified**:
- Writer claimed 47 です/ます endings but actual count was 31
- Gap between self-check and reality triggered publication cap at 8.5/10
- No explicit guidance on verification procedure

**Changes**:
```diff
**Quick Self-Check Before Submitting:**
- 1. Search your article for です。and ます。
+ 1. Search your article for です。and ます。using your editor's find function
- 2. Count total occurrences
+ 2. **CRITICAL**: Manually count EXACT total occurrences (do NOT estimate)
+ 3. Verify your count by searching again and counting carefully
- 3. For ~200-line article:
+ 4. For ~200-line article:
   - <30 endings = ❌ Too casual (likely <40%, unpublishable or low score)
   - 30-39 endings = ⚠️ Acceptable minimum (40-44%, caps at 8.5)
   - **40-50 endings = ✅ OPTIMAL TARGET** (45-60%, required for 9.0+)
   - >60 endings = Possibly too formal (rare issue)
+ 5. **For articles >250 lines**: Scale proportionally (~50-60 endings for 270-line article)
+
+ **⚠️ ACCURACY WARNING**: Do NOT claim "47 endings" without manually verifying. Estimation errors trigger publication blockers.
```

**Rationale**: The 274-line article had only 31 endings when it needed ~50-60 (scaled from 40-50 baseline). The writer's self-check failed to catch this gap. Explicit verification steps prevent future counting errors.

**Impact**: Forces writers to use search tools and manual counting rather than estimation. Prevents claiming incorrect counts that would lead to publication failures.

---

### 2. Clarified "筆者" Frequency Intensity (AUTHOR VOICE)

**Location**: Section "Author Voice: uhyo-Specific Patterns" - Pattern 3

**Problem Identified**:
- Article had only 3 uses of "筆者" (bare minimum of 3-8 range)
- Style guide didn't distinguish between "technically passing" (3-4) and "characteristic" (5-6)
- Contributed to author voice score of 8.5 points instead of 9+

**Changes**:
```diff
- ### Pattern 3: "筆者" Usage (3-8 times) ⭐ CRITICAL
+ ### Pattern 3: "筆者" Usage (5-6 typical, 3-8 acceptable) ⭐ CRITICAL

+ **FREQUENCY GUIDANCE**:
+ - **Optimal**: 5-6 uses (most characteristic of uhyo voice)
+ - **Borderline**: 3-4 uses (technically passing but weak author presence)
+ - **Maximum**: 8 uses (upper acceptable limit)

**Use "筆者" for**:
[...existing examples...]

- **Updated from Season 2**: 3-8x (not 3-5x) — uhyo uses it more frequently
+ **⚠️ INTENSITY MATTERS**: 3 uses meets minimum but reduces author voice score. Target 5-6 for authentic uhyo intensity.
```

**Rationale**: Iteration 4's 3 uses were all contextually appropriate but felt minimal compared to actual uhyo articles. The style guide needed to distinguish minimum compliance (3-4) from authentic intensity (5-6).

**Impact**: Writers now understand that 5-6 is the target, not just "anywhere in 3-8 range." This should increase "筆者" presence in future articles.

---

### 3. Added Explicit Zenn Formatting Guidance (AUTHOR VOICE)

**Location**: Section "Author Voice: uhyo-Specific Patterns" - Pattern 6

**Problem Identified**:
- Article had 0 Zenn formatting blocks (:::message, :::details)
- Article discussed Next.js 14 specifically - natural use case for :::message version caveat
- Style guide said "0-2 blocks" but didn't explain when to use them

**Changes**:
```diff
### Pattern 6: Zenn Formatting (0-2 blocks)

- `:::details 補足的な話` for digressions, `:::message` for version caveats
+ **WHEN TO USE**:
+ - `:::message` for version-specific caveats or important warnings (use when article discusses specific versions)
+ - `:::details 補足的な話` for tangential explorations that would disrupt main flow
+ - **If article has version-specific information**: :::message is expected (not optional)
+ - **If no natural use case**: Absence is acceptable
+
+ **EXAMPLES**:
+ ```
+ :::message
+ この記事はNext.js 14.0時点の挙動です。Next.js 15では挙動が変わる可能性があります。
+ :::
+ ```
+
+ ```
+ :::details カスタムエラーのシリアライゼーションについて
+ Server Actionsのエラーは...
+ :::
+ ```
+
+ **FREQUENCY**: 0-2 blocks per article (1 is most natural when applicable)
```

**Rationale**: The article explicitly discussed "Next.js 14で正式リリースされた" and mentioned "Next.js 15のドキュメント" - perfect use case for a :::message version caveat. Style guide now makes this expectation explicit.

**Impact**: Writers will recognize when :::message is expected (version-specific articles) vs optional (general concept articles).

---

### 4. Refined Strategic Bold Precision Rules (AUTHOR VOICE)

**Location**: Section "Author Voice: uhyo-Specific Patterns" - Pattern 8

**Problem Identified**:
- Article bolded a full clause: "**クライアント側でcatchしていないのに、アプリケーション全体がクラッシュしない**" (entire sentence)
- Should only bold technical TERMS like **Server Actions**, not explanatory clauses
- Style guide warned against section labels but not full clauses

**Changes**:
```diff
### Pattern 8: Strategic Bold (3-5 terms) ⚠️ ESSENTIAL

- **Bold key technical terms on first introduction ONLY.** 3-5 per article.
+ **Bold key technical TERMS on first introduction ONLY.** 3-5 per article.

+ **WHAT TO BOLD**:
+ ✅ Technical terms/concepts (1-4 words max): **Server Actions**, **型推論**, **並列処理の強化**, **インクリメンタルビルド**
+ ✅ Single terms or short phrases representing concrete technical concepts
+
+ **WHAT NOT TO BOLD**:
- **CRITICAL**: Do NOT bold section labels in prose (**良い点**:, **プロジェクト構成**:, **気になる点**: = ❌ wrong)
+ ❌ Section labels in prose: "**良い点**: ビルドが速い" "**テストプロジェクト**: React 18"
+ ❌ Full clauses/sentences: "**クライアント側でcatchしていないのに、アプリケーション全体がクラッシュしない**"
+ ❌ Concepts or ideas longer than 4 words
+ ❌ Generic descriptive phrases

- ✅ Bold technical concepts: **並列処理の強化**, **インクリメンタルビルド**, **Rolldown bundler**
- ❌ Bold section labels: "**良い点**: ビルドが速い" "**テストプロジェクト**: React 18"
+ **PRECISION RULE**: If bold is longer than 4 words, it's probably wrong. Bold should be technical TERMS, not explanatory CLAUSES.

**<3 terms = caps score at 8.5/10** (weak uhyo voice marker)
```

**Rationale**: The article's full-clause bold (19+ words) was significantly over-bold. The new "1-4 words max" rule provides a clear heuristic. Technical TERMS (nouns/noun phrases) vs explanatory CLAUSES (full sentences).

**Impact**: Writers will restrict bold to short technical terms only. The 4-word max rule is easy to check.

---

### 5. Enhanced Pre-Submission Checklist

**Location**: Section "PRE-SUBMISSION CHECKLIST"

**Problem Identified**:
- Checklist didn't emphasize manual counting verification
- Didn't mention scaling for longer articles (274-line article needs more endings than 200-line)
- "筆者" guidance was generic (3-8x) without intensity target
- Zenn formatting wasn't in checklist

**Changes**:

**CRITICAL Section**:
```diff
- [ ] **40-50 です/ます endings for ~200-line articles** (optimal target; 30-39 caps at 8.5; <15 unpublishable)
+ [ ] **40-50 です/ます endings for ~200-line articles** (MANUALLY COUNTED and VERIFIED; 30-39 caps at 8.5; <15 unpublishable)
+ [ ] **Scale です/ます for article length** (270-line article needs ~50-60 endings)
```

**BASIC QUALITY Section**:
```diff
- [ ] **3-5 strategic bold terms** (key concepts on first mention; <3 = caps at 8.5)
+ [ ] **3-5 strategic bold TERMS** (1-4 words max; no full clauses; <3 = caps at 8.5)

- [ ] "筆者" used appropriately (3-8x for uhyo voice)
+ [ ] **"筆者" used 5-6 times (optimal)** or 3-4 times (borderline) for uhyo voice
+ [ ] **Zenn formatting when applicable** (:::message for version caveats if discussing specific versions)
```

**Rationale**: Checklist now reflects all the detailed guidance from the updated sections. Writers will see specific targets (5-6 "筆者", 1-4 word bold, scaled です/ます counts) at submission time.

**Impact**: Pre-submission checklist becomes actionable validation tool with specific numeric targets.

---

## What These Changes Address

### Base Score Cap (8.5) - Polite Form Density
**Root Cause**: 31 です/ます endings in 274-line article → 30-35% distribution (below 45-60% optimal)

**Solution**: Enhanced self-checking procedure with:
- Explicit manual counting requirement
- Verification step (count twice)
- Length scaling guidance (274 lines needs ~50-60 endings, not just 40-50)
- Accuracy warning against claiming wrong counts

**Expected Impact for Iteration 5**: Writer will manually count and verify, catching if count is below scaled target (50-60 for ~270 lines).

---

### Author Voice Cap (8.5) - Pattern Intensity
**Root Cause**: 8.5 author voice points from minimal pattern intensity:
- "筆者" 3x (minimum, not typical)
- Zenn formatting 0 blocks (absent where natural)
- Bold 1 imprecision (clause instead of term)

**Solutions**:
1. **"筆者" intensity guidance**: 5-6 is optimal, 3-4 is borderline
2. **Zenn formatting guidance**: When to use (version caveats = expected)
3. **Bold precision rule**: 1-4 words max, no clauses

**Expected Impact for Iteration 5**: Writer will target 5-6 "筆者" uses, add :::message if discussing specific versions, and restrict bold to short technical terms only.

---

## Key Improvements Summary

| Issue | Iteration 4 Result | Style Guide Update | Expected Iteration 5 |
|-------|-------------------|-------------------|---------------------|
| です/ます count | 31 (claimed 47) | Manual count + verify procedure | Accurate count, scaled for length |
| "筆者" frequency | 3x (minimal) | Optimal 5-6, borderline 3-4 | Target 5-6 uses |
| Zenn formatting | 0 blocks | When to use guidance + examples | 1 block if version-specific |
| Bold precision | 1 clause bold | 1-4 words max, no clauses | Only technical terms |
| Length scaling | Not considered | Explicit scaling guidance | 50-60 endings for 270-line article |

---

## Technical Rationale

### Why Manual Counting Matters

**Problem**: Writer self-check said "47 endings" but actual was 31.

**Analysis**:
- Difference of 16 endings (33% error)
- Likely confusion between:
  - Total です/ます (including mid-sentence)
  - Sentence-ending です。ます。(what counts)
- Or simple estimation without verification

**Solution**: Force explicit search (`Ctrl+F` → です。) and manual counting of results.

**Verification**: Count twice to catch mistakes.

---

### Why "筆者" Intensity Guidance Matters

**Problem**: 3 uses technically passes but feels minimal.

**Analysis**:
- Actual uhyo articles sampled: 5-8 uses typical
- 3 uses = bare minimum compliance
- No distinction in style guide between 3x (weak) and 6x (strong)

**Solution**: Explicit tiers:
- 5-6 = optimal (authentic intensity)
- 3-4 = borderline (passing but weak)
- 8+ = maximum (over-presence)

**Rationale**: Writers need to know target, not just acceptable range.

---

### Why Zenn Formatting Guidance Matters

**Problem**: 0 blocks when article naturally called for :::message.

**Analysis**:
- Article title: "Next.js 14のServer Actionsを試して..."
- Article mentions: "Next.js 15のドキュメント"
- Natural use case for: ":::message この記事はNext.js 14.0時点の挙動です。:::
- But no blocks used

**Solution**: Explicit rule: "If article has version-specific information, :::message is expected (not optional)"

**Rationale**: Makes previously implicit expectation explicit.

---

### Why Bold Precision Rule Matters

**Problem**: Full clause bolded instead of technical term.

**Analysis**:
- Bolded: "**クライアント側でcatchしていないのに、アプリケーション全体がクラッシュしない**" (19 words)
- Should be: No bold (concept explained in prose) OR "**クラッシュしない**" (3 words)
- Style guide warned against section labels but not clauses

**Solution**: "1-4 words max" heuristic

**Rationale**:
- Technical TERMS are short: **Server Actions** (2 words), **型推論** (1 word)
- Explanatory CLAUSES are long: "クライアント側で...しない" (19 words)
- Simple numeric cutoff (4 words) is easy to check

---

## Path to 9.0+ (Iteration 5 Goals)

### Base Score Requirements
**Current**: 8.5/10 (polite form cap)

**To Lift Cap**:
- Increase です/ます endings to 50-60 (from 31)
- Achieve 45-60% polite form distribution (from ~30-35%)
- Verify count manually before claiming

**Target**: Base score 9.0+ (no polite form cap)

---

### Author Voice Requirements
**Current**: 8.5 points → 8.5 cap (7-8 tier)

**To Lift Cap**:
- Increase "筆者" to 5-6 uses (from 3)
- Add 1 Zenn formatting block if version-specific (from 0)
- Fix bold precision to terms only (from 1 clause violation)

**Target**: 9+ author voice points → no cap (enables 9.0+ final score)

---

### Dual Requirements for 9.0+

Both must pass:
1. **Base Score** ≥ 9.0 (Season 2 criteria)
2. **Author Voice** ≥ 9 points (no cap applied)

**Final Score** = min(Base Score, Author Voice Cap)

For 9.0+ final: Base ≥ 9.0 AND Author Voice ≥ 9 pts

---

## Iteration 4 Achievement Assessment

**Overall Score**: 8.5/10 (dual-limited by both base and author voice)

**Strengths**:
- Zero forbidden patterns (clean linguistic compliance)
- Strong systematic investigation structure
- Rich personal project integration
- Perfect opening and conclusion formulas
- Technical accuracy with ecosystem context
- Code-driven narrative rhythm

**Dual Limitations**:
1. **Base Score**: Polite form density 30-35% vs 45-60% optimal
2. **Author Voice**: Pattern intensity lower than typical uhyo

**Progress Assessment**: Article demonstrates strong uhyo voice fundamentals at reduced intensity. Gap to 9.0+ is narrow - requires pattern intensity boost, not fundamental restructuring.

---

## Style Guide Evolution

**Version**: 2.3 → 2.4

**Philosophy**: From "permissive ranges" to "intensity targets"

Previous approach:
- "3-8 times" (anywhere in range OK)
- "0-2 blocks" (no guidance on when)
- "3-5 terms" (no guidance on what)

Updated approach:
- "5-6 optimal, 3-4 borderline" (intensity matters)
- "Version-specific = expected" (explicit trigger)
- "1-4 words max, terms only" (precision rule)

**Result**: Writers now have specific numeric targets and clear triggers for when patterns are expected vs optional.

---

## Expected Iteration 5 Improvements

Based on these style guide updates, Iteration 5 should demonstrate:

1. **Accurate です/ます count**: 50-60 endings for ~270-line article, manually verified
2. **Higher "筆者" frequency**: 5-6 uses (not 3-4)
3. **Zenn formatting presence**: 1 :::message block if version-specific topic
4. **Precise bold usage**: Only 1-4 word technical terms, no clauses
5. **Combined result**: Base score 9.0+ AND author voice 9+ pts = 9.0+ final score

**Strategic Focus**: Iteration 5 should maintain Iteration 4's strong fundamentals (systematic investigation, personal projects, code-driven narrative) while increasing pattern intensity (more です/ます, more "筆者", proper Zenn formatting).

---

**Changelog Version**: 1.0
**Created**: Iteration 4 completion
**Author**: Style Guide Updater Agent
