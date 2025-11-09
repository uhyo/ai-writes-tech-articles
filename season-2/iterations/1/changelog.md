# Style Guide Changelog - Iteration 1

## Summary

This iteration revealed a **critical misalignment** between the style guide's requirements and human baseline reality. The style guide demanded 95%+ polite forms, but analysis of human benchmark articles showed they use only 40-60% polite forms. The generated article matched human patterns (43% polite) but violated the style guide's artificial standard.

**Key realization**: The style guide was creating an impossible target that would make articles **more artificial, not more human-like**.

## Major Changes

### 🔴 CRITICAL: Corrected Polite Form Requirement

**Previous (INCORRECT):**
- Target: ≥95% of sentence endings use polite form (-ます/-です)
- Scoring: Polite form ratio <90% caps overall score at 7.0/10

**New (ALIGNED WITH HUMAN REALITY):**
- Target: Natural mix (~40-60% polite) based on context
- Use polite forms for main explanations, casual for lists/facts/embedded statements
- Removed artificial 95% requirement that contradicted human baseline
- Updated scoring to focus on forbidden patterns, not polite ratio

**Why this change:**
- Human benchmark analysis showed 40-60% polite forms across all sampled articles
- The article's 43% polite ratio **matched human baseline** but violated style guide
- The old rule would have forced unnatural, overly formal writing
- Authenticity comes from natural variation, not artificial uniformity

**Evidence from review:**
```
Human articles polite form ratio:
- react-use-rfc.md: ~43%
- typescript-4-8-type-narrowing.md: ~60%
- javascript-algebra-1.md: ~53%
- array-n-keys-yamero.md: ~33%
Average: 40-60%

Generated article: 43% (matches human baseline!)
```

### 🔴 Updated Scoring Rules

**Previous:**
- Polite form <90%: Maximum score 7.0/10
- Forbidden patterns 1-2x: Maximum score 8.5/10
- Forbidden patterns 3+x: Maximum score 7.5/10

**New:**
- Forbidden patterns 3+x: Maximum score 7.0/10
- Forbidden patterns 1-2x: Maximum score 8.0/10
- Unnatural uniformity (>90% or <20% polite): Deduction as AI tell
- Focus on zero tolerance for specific forbidden patterns

**Why this change:**
- Removed polite ratio cap (was based on incorrect assumption)
- Made forbidden pattern penalties clearer and more logical
- Added detection for unnatural uniformity in either direction

### 🟡 Added Mandatory Authenticity Markers

**New requirement:** Every article MUST include these elements:

1. **Code Evolution (MANDATORY)**
   - Show bugs → fixes, or V1 → V2 → V3 iteration
   - Mid-explanation realizations: "あ、これバグあるな..."
   - Sometimes leave imperfect: "まあ、動くので放置"
   - Multiple attempts showing learning process

2. **Unresolved Elements (2-3 REQUIRED)**
   - Partially answered questions: "この辺はまだ分かってない"
   - Speculation without confirmation: "試してないけど、たぶん動くはず"
   - Abandoned tangents: "余談ですが〜" (never returns)
   - Future intentions: "いつか検証したい"
   - Mid-article admissions: "さっき言い忘れたけど〜"

3. **Ecosystem Context (1-2 REQUIRED)**
   - GitHub references: "(#2851とか)" casually buried
   - Community awareness: "Twitterで〜見た"
   - Temporal context: "TypeScript 5.5で入るかも"

**Why this change:**
- Review showed article had ZERO code evolution (all perfect on first try)
- No unresolved elements (everything neatly answered)
- No ecosystem references (article in vacuum)
- These are the #1, #2, #3 AI tells identified in comparison with human articles

**Impact:**
- Makes these requirements explicit and mandatory
- Moves from "nice to have" to "must have"
- Added to AUTHENTICITY MARKERS checklist section

### 🟡 Restructured Technical Depth Section

**Enhanced §5.4: Technical Depth & Ecosystem Context**

**New emphasis:**
- Code evolution is now the FIRST and most prominent guideline
- Added specific patterns: bug realizations, multi-step iterations
- Expanded ecosystem context with concrete examples
- Added temporal awareness ("2019年くらい？")

**Before:** Generic advice about showing iteration
**After:** Specific patterns with code examples and concrete phrases to use

### 🟡 Made Unresolved Elements Explicit

**Enhanced §5.5: Structure - Non-Linear Exploration**

**New section: "Include Unresolved Elements (MANDATORY)"**
- Changed from implicit suggestion to explicit checklist
- Requires 2-3 unresolved elements per article
- Provides specific examples of each type
- Explains why perfect resolution is an AI tell

### 🟢 Updated Anti-Patterns Section

**Previous:** Generic list of AI tells
**New:** Prioritized top 7 AI tells with specific alternatives

**New structure:**
1. Perfect code (most critical)
2. Complete resolution
3. No ecosystem context
4. Uniform depth
5. Strategic imperfections
6. Artificial formality (NEW - addresses polite form issue)
7. Formula-following

**Why this change:**
- Prioritizes by impact (code perfection is #1 tell)
- Adds "artificial formality" to address the polite form correction
- Makes alternatives explicit ("instead: ...")

## Changes by Priority Level

### 🔴 CRITICAL (Publication Blockers)

**§1 - Japanese Language & Tone:**
- ✏️ Replaced 95% polite requirement with 40-60% natural mix
- ✏️ Updated scoring rules to focus on forbidden patterns
- ✅ Kept forbidden patterns: sentence-ending -てる/-てた, paragraph-initial "で、"

**Impact:** Prevents creating artificially formal articles

### 🟡 IMPORTANT (Human-Like Patterns)

**§5.4 - Technical Depth & Ecosystem Context:**
- ➕ Added "Show Code Evolution Through Discovery" as primary guideline
- ➕ Added specific code iteration patterns
- ➕ Expanded ecosystem context examples
- ➕ Added temporal awareness examples

**§5.5 - Structure:**
- ➕ Made "Unresolved Elements" mandatory with checklist
- ➕ Changed from 0-1 unresolved elements to 2-3 required

**Checklist Section:**
- ➕ Added "Authenticity Markers (MANDATORY)" section
- ➕ Made code evolution, unresolved elements, and ecosystem context explicit requirements

**Impact:** Transforms vague guidance into concrete requirements

### 🟢 POLISH (Final Refinements)

**Anti-Patterns:**
- ➕ Added prioritized list of top 7 AI tells
- ➕ Added "artificial formality" as new anti-pattern

## Line Count Analysis

**Changes:**
- Lines added: ~95 (new mandatory sections, expanded examples)
- Lines removed: ~40 (consolidated scoring rules, removed incorrect polite form guidance)
- Net change: +55 lines
- New total: 420 lines (target: <350)

**Justification:**
- Critical corrections required adding explicit guidance that was previously wrong or missing
- The 95% polite form requirement was actively harmful - removal saves future confusion
- Mandatory authenticity markers are highest-impact additions (address top 3 AI tells)
- Next iteration should consolidate examples and compress verbose sections

**Sections to consolidate in iteration 2:**
- §5.1 (Write from THINKING) has some redundancy with §5.5
- §5.3 (Authentic Anecdotes) could be compressed to bullet points
- §7 (Micro-Imperfections) overlaps with randomness discussion in §5.1
- Polish section could be significantly compressed

## Rule Effectiveness Tracking

### Rules That Worked (Consider Compressing)

**[✓ EFFECTIVE - Followed] Forbidden Pattern: Sentence-ending contracted forms**
- Evidence: Only 2 instances in entire article (lines 40, 135)
- This rule was mostly followed, violations were edge cases
- Action: Keep as CRITICAL, but rule is working

**[✓ EFFECTIVE - Followed] Frontmatter Format**
- Evidence: Valid YAML frontmatter present with all required fields
- Article correctly formatted with title, emoji, type, topics, published
- Action: Can remain concise in current form

**[✓ EFFECTIVE - Followed] Technical Accuracy**
- Evidence: All code examples correct, concepts explained accurately
- Review gave 9/10 on technical accuracy
- Action: No changes needed to this guidance

**[✓ EFFECTIVE - Followed] Section Count**
- Evidence: 7 H2 sections (within 6-7 guideline)
- Section lengths varied (2-10+ paragraphs)
- Action: Guideline working, keep as is

**[✓ EFFECTIVE - Followed] Personal Voice**
- Evidence: "筆者" used 3 times (within 3-5 guideline)
- Personal anecdotes present ("去年くらいに社内の...")
- Action: Guideline effective, consider compressing

### Rules That Were Violated (Promote or Clarify)

**[✗ VIOLATED - CRITICAL] Polite Form Requirement (95%)**
- Evidence: Article had 43% polite forms, triggering 7.0/10 score cap
- Impact: Capped score despite article matching human baseline (40-60%)
- **Root cause: Style guide requirement was WRONG**
- Action: ✅ FIXED - Replaced with 40-60% natural mix guideline

**[✗ VIOLATED - CRITICAL] Forbidden Pattern: Paragraph-initial "で、"**
- Evidence: 2 instances (lines 44, 170)
- Impact: Publication blocker, contributed to score cap
- Action: ✅ Rule is correct, kept at CRITICAL with zero tolerance

**[✗ VIOLATED] Show Code Evolution**
- Evidence: All code examples perfect on first try, no bugs or iteration shown
- Impact: Major AI tell - review identified this as primary weakness
- Action: ✅ PROMOTED to CRITICAL, made MANDATORY in authenticity markers

**[✗ VIOLATED] Include Unresolved Elements**
- Evidence: Article resolved all questions, no loose ends or speculation
- Impact: Feels too polished and complete (AI tell)
- Action: ✅ PROMOTED to MANDATORY, requires 2-3 unresolved elements

**[✗ VIOLATED] Add Ecosystem Context**
- Evidence: Zero GitHub references, no community mentions, no temporal context
- Impact: Article exists in vacuum, lacks human contextual awareness
- Action: ✅ PROMOTED to MANDATORY in authenticity markers

**[✗ VIOLATED] Vary Depth by Interest**
- Evidence: All use cases got similar depth (5-8 paragraphs each)
- Impact: Pedagogically uniform, not human-like interest-driven variation
- Action: ✅ Enhanced guideline with stronger examples

### Rules That Need Clarification

**[~ UNCLEAR] "Main Text" Definition**
- Evidence: Confusion about when casual forms are acceptable
- Current rule says casual OK in "subordinate clauses" but this is vague
- Action: ✅ Added explicit context-based usage examples in §1

**[~ UNCLEAR] What Counts as "Ecosystem Context"**
- Evidence: Rule existed but no examples of casual integration
- Writer may not have known how to include GitHub refs naturally
- Action: ✅ Added concrete examples: "(#2851とか)", "Andersさんのコメント"

### New Issues Not Covered by Current Guide

**[+ NEW ISSUE] No Conceptual Frameworks**
- Evidence: Article explains syntax but doesn't introduce meta-technical concepts
- Human examples: "Promiseが一級市民ではなかった", "JavaScript代数系"
- Proposed guideline: ✅ Already exists in §5.3 (Introduce Conceptual Frameworks)
- Action: Keep existing rule, consider emphasizing in future if pattern repeats

**[+ NEW ISSUE] Uniform Use Case Structure**
- Evidence: Each use case followed identical formula (code → explanation → conclusion)
- This creates predictable, mechanical feel
- Proposed guideline: Vary explanation approaches, break patterns
- Action: Covered implicitly by "vary depth by interest" but could be made more explicit in iteration 2

**[+ NEW ISSUE] No Mid-Article Realizations**
- Evidence: No "ああ、そういえば〜" or "さっき言い忘れたけど"
- Human articles often circle back or realize omissions mid-article
- Proposed guideline: ✅ Added to unresolved elements checklist
- Action: Now explicitly mentioned as type of authentic messiness

## Impact Assessment

### Expected Improvements in Iteration 2

**High confidence (should see immediately):**
1. ✅ Natural polite/casual ratio (40-60%) without artificial formality
2. ✅ Code evolution shown in at least 1-2 examples
3. ✅ At least 2-3 unresolved elements (speculation, abandoned tangents)
4. ✅ Some ecosystem context (GitHub/community/temporal references)

**Medium confidence (may take 2-3 iterations):**
1. Dramatically uneven depth based on apparent interest
2. Natural clustering of imperfections (not strategic distribution)
3. Messy conclusions without numbered synthesis
4. Meta-technical conceptual frameworks

**Challenges remaining:**
- Line count needs reduction (420 → <350)
- Some redundancy between sections needs consolidation
- Writer may still apply techniques mechanically rather than thinking deeply
- Balancing explicit requirements with organic thinking

### Key Success Metrics for Iteration 2

**Must achieve (publication blockers):**
- Zero forbidden patterns (no -てる。endings, no paragraph-initial で、)
- 40-60% polite form ratio
- Valid frontmatter

**Should achieve (authenticity markers):**
- ✅ Code evolution shown (at least 1 example)
- ✅ 2-3 unresolved elements present
- ✅ 1-2 ecosystem context references

**Target scores:**
- Technical Accuracy: 9/10 (maintain)
- Writing Style: 7.5+/10 (up from 5/10)
- Linguistic Authenticity: 8+/10 (up from 6/10)
- Authenticity: 7.5+/10 (up from 6/10)
- Overall: 8.0+/10 (up from 7.0/10)

## Lessons Learned

### Critical Discovery

**The style guide was enforcing an artificial standard that contradicted human reality.**

This is a meta-lesson about the iterative process itself:
- Initial guidelines can be based on assumptions rather than evidence
- The Reviewer Agent's human baseline analysis is critical for catching these errors
- Style guide requirements should be **descriptive** (what humans do) not **prescriptive** (what we think they should do)

### Process Insight

**The review's scoring impact rules worked correctly** - the 7.0/10 cap forced us to discover the mismatch. Without strict scoring rules, we might have missed that the style guide itself was wrong.

### Architecture Validation

The three-agent system proved effective:
1. **Writer** followed style guide faithfully (even when it was wrong)
2. **Reviewer** compared against human baseline and caught the contradiction
3. **Style Guide Updater** (this agent) corrected the root cause

This shows the system can self-correct when guidelines are misaligned.

## Next Iteration Focus

### For Writer Agent (Iteration 2)

**Priorities:**
1. Show at least one code evolution example (bug → fix)
2. Include 2-3 unresolved elements (speculation, abandoned tangents, future work)
3. Add 1-2 ecosystem references (GitHub/community/temporal)
4. Vary depth dramatically (10+ paragraphs on interesting topic, 2 sentences on boring one)
5. Maintain natural polite/casual mix (40-60%) without forcing uniformity

### For Reviewer Agent (Iteration 2)

**New checks:**
- Verify code evolution is present (mandatory)
- Count unresolved elements (need 2-3)
- Check for ecosystem context (need 1-2)
- Verify natural polite/casual ratio without artificial uniformity

### For Style Guide Updater (Iteration 3)

**Consolidation task:**
- Target: Reduce from 420 lines to <350
- Compress overlapping sections (§5.1 + §5.5)
- Simplify examples (max 2 per rule)
- Remove redundant explanations

---

**Version:** 1.0 → 1.1
**Status:** Major corrections implemented
**Next review:** Iteration 2 will validate if mandatory authenticity markers are followed
