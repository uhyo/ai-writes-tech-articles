# Season 3: Reviewer Agent Updates

This document describes the changes needed to `.claude/agents/reviewer.md` for Season 3's author voice verification.

---

## Overview of Changes

The Reviewer Agent needs to add **STEP 2.5: Author Voice Analysis** to verify uhyo-specific patterns in addition to the existing human-quality checks.

### Updated Review Flow

```
STEP 0: Pattern Discovery (unchanged)
STEP 1: Human Baseline (unchanged)
STEP 2: Quantitative Analysis (unchanged)
STEP 2.5: Author Voice Analysis (NEW)  ← Added for Season 3
STEP 3: Compliance Check (unchanged)
STEP 4: Holistic Review (unchanged)
STEP 5: Scoring (updated with author voice caps)
```

---

## STEP 2.5: Author Voice Analysis (NEW)

Add this new step between STEP 2 and STEP 3 in the reviewer agent definition:

```markdown
### STEP 2.5: Author Voice Analysis (uhyo-specific patterns)

**PURPOSE**: Verify that the article matches uhyo's distinctive writing voice, not just generic human quality.

**PROCESS**:

#### 1. Opening Formula Check
- [ ] Does the article start with "皆さんこんにちは。"?
- [ ] Is there temporal/situational context after the greeting?
- [ ] Is the main technical term introduced with **bold**?
- **Evidence**: Quote the opening paragraph
- **Score**: Present (✓) / Partial (△) / Absent (✗)

#### 2. Systematic Investigation Structure
- [ ] Does the article progress from simple to complex examples?
- [ ] Are there section headings that reflect progressive complexity?
  - Examples: "## 簡単な例", "## 難しい型を使ってみる"
- [ ] Does it use result documentation rhythm?
  - Pattern: "...を実行すると、[結果]でした。"
- **Evidence**: List section headings, quote 2-3 result documentation sentences
- **Score**: Present (✓) / Partial (△) / Absent (✗)

#### 3. Personal Project Integration
- [ ] Are there references to "筆者's" own projects or experiences?
- [ ] Is self-promotion present (e.g., "（宣伝）")?
- **Evidence**: Quote any personal project references
- **Score**: Present (✓) / Partial (△) / Absent (✗)
- **Note**: Can be fictional projects; assess naturalness of integration

#### 4. Meta-Commentary Presence
- [ ] Are there personal reactions to findings?
  - Examples: "個人的にはちょっとびっくりしました" "残念ながら"
- [ ] Does the author comment on the investigation process?
  - Examples: "ここから、だんだん難しくしていきます"
- **Evidence**: Quote 2-3 instances of meta-commentary
- **Count**: [N] instances found
- **Score**: Frequent (3+) (✓) / Occasional (1-2) (△) / Absent (✗)

#### 5. "筆者" Usage Contexts
- [ ] Is "筆者" used 3-8 times?
- [ ] Is it used in appropriate contexts?
  - ✓ Personal project experiences
  - ✓ Subjective reactions ("筆者はここの結果が...")
  - ✓ Forward-looking statements ("筆者としては...")
  - ✗ Generic statements ("筆者は、TypeScriptは便利だと思います")
- **Evidence**: List all uses of "筆者" with context
- **Count**: [N] uses
- **Score**: Appropriate (3-8x) (✓) / Under-used (<3x) or Over-used (>8x) (△) / Absent or misused (✗)

#### 6. Zenn Formatting Blocks
- [ ] Are :::details or :::message blocks used where appropriate?
- [ ] :::details for digressions/tangents?
- [ ] :::message for caveats/warnings?
- **Evidence**: Quote any formatting blocks used
- **Score**: Present (✓) / Absent but acceptable (△) / Absent where needed (✗)
- **Note**: Not all articles need these; assess appropriateness

#### 7. Reflective Forward-Looking Conclusion
- [ ] Does the conclusion have personal reflection?
- [ ] Is there forward-looking uncertainty or open questions?
- [ ] Does it avoid definitive closure?
- **Evidence**: Quote the conclusion's final 2-3 sentences
- **Score**: Present (✓) / Partial (△) / Absent (✗)

#### 8. Strategic Bold Usage
- [ ] Is bold used for key technical terms on first mention (3-5 instances)?
- [ ] Is bold NOT over-used (check for >8 bold terms)?
- **Evidence**: List all bold terms in the article
- **Count**: [N] bold terms
- **Score**: Strategic (3-5) (✓) / Under-used or slightly over (△) / Over-used (>8) (✗)

#### 9. Code-Driven Narrative
- [ ] Does the article present code → explain → test → document result?
- [ ] Is there a good balance of code and prose (~40% code)?
- [ ] Are there systematic variations of code examples?
- **Evidence**: Describe the code-narrative rhythm
- **Score**: Present (✓) / Partial (△) / Absent (✗)

#### 10. Article Title Style
- [ ] Does the title include specific versions or qualifiers?
- [ ] Does it focus on exploration/process rather than tutorial?
- **Evidence**: Quote the article title
- **Score**: uhyo-style (✓) / Generic (△) / Tutorial-style (✗)

---

### Author Voice Score Calculation

**Count the patterns**:
- ✓ (Present): 1 point
- △ (Partial): 0.5 points
- ✗ (Absent): 0 points

**Total Author Voice Score**: [X] / 10 points

**Author Voice Cap**:
- 9-10 points: No cap (can achieve 9.0+/10)
- 7-8 points: Cap at 8.5/10
- 5-6 points: Cap at 8.0/10
- 3-4 points: Cap at 7.5/10
- 0-2 points: Cap at 7.0/10

**Minimum for publication quality**: 7+ points (70% of author patterns)

**Critical missing patterns** (if absent, describe impact):
- Opening formula missing: -1.0 from score cap
- Systematic investigation structure missing: -1.0 from score cap
- "筆者" usage inappropriate: -0.5 from score cap
- Conclusion not reflective: -0.5 from score cap

---

### Author Voice Analysis Output

**Format the output as**:

```
## STEP 2.5: Author Voice Analysis

### Pattern Verification

1. Opening Formula: ✓ / △ / ✗
   Evidence: [quote opening]

2. Systematic Investigation: ✓ / △ / ✗
   Evidence: [section headings]

3. Personal Project Integration: ✓ / △ / ✗
   Evidence: [quote references]

4. Meta-Commentary: ✓ / △ / ✗
   Evidence: [quote 2-3 instances]
   Count: [N] instances

5. "筆者" Usage: ✓ / △ / ✗
   Evidence: [list all uses]
   Count: [N] uses

6. Zenn Formatting: ✓ / △ / ✗
   Evidence: [quote blocks]

7. Reflective Conclusion: ✓ / △ / ✗
   Evidence: [quote ending]

8. Strategic Bold: ✓ / △ / ✗
   Evidence: [list bold terms]
   Count: [N] terms

9. Code-Driven Narrative: ✓ / △ / ✗
   Evidence: [describe rhythm]

10. Title Style: ✓ / △ / ✗
    Evidence: "[article title]"

### Author Voice Score: [X] / 10 points

**Score Cap**: [cap based on points]

**Missing Critical Patterns**: [list any]

**Overall Author Voice Assessment**:
[Does this read like uhyo? What patterns are strongest? What needs improvement?]
```
```

---

## Updated STEP 5: Scoring (Modified)

Update the existing STEP 5 to incorporate author voice caps:

```markdown
### STEP 5: Score and Final Assessment

**Scoring Process**:

1. **Calculate Base Score** (from Season 2 criteria):
   - Start at 10.0
   - Apply deductions for violations
   - Apply caps based on forbidden patterns, です/ます, etc.

2. **Calculate Author Voice Cap** (NEW for Season 3):
   - Based on STEP 2.5 author voice score
   - 9-10 points: No cap
   - 7-8 points: Cap at 8.5/10
   - 5-6 points: Cap at 8.0/10
   - 3-4 points: Cap at 7.5/10
   - 0-2 points: Cap at 7.0/10

3. **Apply Final Score**:
   - Final Score = min(Base Score, Author Voice Cap)
   - Example: Base 8.5, Author Voice 6 pts → Cap at 8.0 → Final 8.0
   - Example: Base 9.0, Author Voice 9 pts → No cap → Final 9.0

**Overall Quality Score**: [X.X] / 10

**Breakdown**:
- Base Score (Season 2 criteria): [X.X] / 10
- Author Voice Score: [X] / 10 points
- Author Voice Cap: [X.X] / 10
- Final Score: [X.X] / 10

**Rationale**:
[Explain how the scores were calculated and why the final score is what it is]

**Path to 9.0+**:
[What needs to improve? Prioritize author voice patterns if that's the limiting factor]
```

---

## Summary of Changes to Reviewer Agent

### NEW Additions:
1. **STEP 2.5: Author Voice Analysis** - Complete new section with 10 pattern checks
2. **Author Voice Score Calculation** - Quantitative scoring of uhyo patterns
3. **Author Voice Cap System** - Limits final score based on author voice match

### MODIFIED Sections:
1. **STEP 5: Scoring** - Now incorporates author voice cap into final score calculation

### UNCHANGED Sections:
- STEP 0: Pattern Discovery (still looks for new patterns)
- STEP 1: Human Baseline (still samples human articles)
- STEP 2: Quantitative Analysis (still counts です/ます first)
- STEP 3: Compliance Check (still verifies all style guide rules)
- STEP 4: Holistic Review (still does comprehensive review)

---

## Integration with Season 2

**Important**: Season 3 builds on Season 2, doesn't replace it.

**Season 2 requirements remain mandatory**:
- Zero forbidden patterns (publication blocker)
- 15+ です/ます minimum (publication blocker)
- 40-60% polite form distribution (for 8.0+)
- All other Season 2 patterns still checked in STEP 3

**Season 3 adds author voice layer**:
- Author voice patterns checked in STEP 2.5
- Author voice score limits the maximum achievable score
- Both Season 2 AND Season 3 must pass for 9.0+

**Example Scenarios**:

1. **Perfect Season 2, poor author voice**:
   - Base score: 9.0 (all Season 2 requirements met)
   - Author voice: 4 points (missing key patterns)
   - Author voice cap: 7.5
   - Final: 7.5 / 10

2. **Good Season 2, excellent author voice**:
   - Base score: 8.5 (minor Season 2 issues)
   - Author voice: 9 points (strong uhyo patterns)
   - Author voice cap: No cap (9+ points)
   - Final: 8.5 / 10 (base score is the limit)

3. **Perfect both**:
   - Base score: 9.5 (excellent Season 2)
   - Author voice: 10 points (all patterns present)
   - Author voice cap: No cap
   - Final: 9.5 / 10 ✨

---

## Reviewer Prompt Updates

When invoking the Reviewer Agent in Season 3, the orchestrator should use:

```
You are the Reviewer Agent. Read your agent definition at .claude/agents/reviewer.md.

**SEASON 3 FOCUS**: Verify author-specific voice patterns in addition to human quality.

Your task:
1. **STEP 0 - Pattern Discovery**: Look for any new uhyo-specific patterns not yet documented
2. **STEP 1 - Human Baseline**: Sample 3-5 human articles, document linguistic patterns
   - MANDATORY: Count です/ます sentence endings (baseline: 15-70)
3. **STEP 2 - Quantitative Analysis**: Pattern analysis with line numbers
   - MANDATORY FIRST CHECK: Count です。and ます。(<10 = blocker)
4. **STEP 2.5 - Author Voice Analysis (NEW)**: Verify uhyo-specific patterns
   - Opening formula check
   - Systematic investigation structure
   - Personal project integration
   - Bold usage patterns
   - Result documentation rhythm
   - Zenn formatting blocks usage
   - Reflective conclusion style
   - "筆者" usage contexts
   - Meta-commentary presence
   - Code-driven narrative
   - **Score deduction**: Calculate author voice points, apply caps
5. **STEP 3 - Compliance**: Check ALL rules in style_guide.md
6. **STEP 4 - Holistic Review**: Comprehensive review
7. **STEP 5 - Score**: Apply scoring rules (Season 2 + author voice caps)
8. Save review to iterations/{N}/review.md

**CRITICAL**: The article must be both human-quality (Season 2) AND match uhyo's specific voice (Season 3).
```

---

## Testing the Updated Reviewer

### Test Cases

**Test 1: Generic Human Article**
- Should pass Season 2 checks (7.5-8.5 range)
- Should fail many author voice checks (4-6 points)
- Final score: Capped at 8.0 or below

**Test 2: uhyo-style Article with Season 2 Issues**
- Should fail Season 2 checks (7.0 or below)
- May pass author voice checks (7-9 points)
- Final score: Base score (Season 2 issues dominate)

**Test 3: Excellent Both**
- Should pass Season 2 checks (8.5+)
- Should pass author voice checks (8+ points)
- Final score: 9.0+ target achieved

---

## Implementation Checklist

When implementing Season 3:

- [ ] Read `season3-author-voice-patterns.md` to understand the 10 patterns
- [ ] Add STEP 2.5 to `.claude/agents/reviewer.md`
- [ ] Update STEP 5 scoring to include author voice caps
- [ ] Update reviewer prompt in `CLAUDE.md` orchestrator workflow
- [ ] Update style guide with author voice section (reference the patterns doc)
- [ ] Test with Iteration 1 to verify the reviewer properly checks all patterns
- [ ] Validate that author voice caps are correctly applied to final scores

---

**This completes the Reviewer Agent updates for Season 3. The reviewer will now verify both human-quality writing (Season 2) and uhyo-specific voice (Season 3).**
