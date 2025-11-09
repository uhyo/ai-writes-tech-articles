# Season 3 Plan: Learning Author-Specific Voice

## Goal

Season 2 successfully achieved **human-quality** articles (8.0-8.2/10) that readers perceive as human-written. However, these articles don't match the specific voice and patterns of **uhyo**, the author of the human benchmark articles.

**Season 3 Goal**: Learn and replicate uhyo's distinctive writing patterns while maintaining the human-quality baseline achieved in Season 2.

## Key Insight

Season 2 taught us to write like "a human technical writer." Season 3 will teach us to write like "**this specific human technical writer**."

This is a more nuanced challenge that requires identifying and replicating:
- Individual writing quirks
- Personal article structures
- Specific narrative patterns
- Distinctive voice markers

---

## Author-Specific Patterns Identified

After analyzing uhyo's articles, here are the distinctive patterns that differentiate their voice from generic human writing:

### 1. **Opening Formula**
- **Pattern**: "皆さんこんにちは。" + Recent event/observation + Topic introduction
- **Example**: "皆さんこんにちは。先日、**Biome v2**がリリースされ話題となりました。"
- **Current Gap**: AI articles start directly with anecdotes without this formal greeting pattern

### 2. **Systematic Technical Investigation**
- **Pattern**: Start simple → Progressively increase complexity → Document what works/doesn't work
- **Example**: "## 簡単な例" → "## `new Promise` を使ってみる" → "## 難しい型を使ってみる" → "### ジェネリクス"
- **Current Gap**: AI articles don't follow this investigative, test-case-by-test-case structure

### 3. **Personal Project Integration**
- **Pattern**: Naturally references their own libraries/projects as solutions or context
- **Example**: "筆者は[nitrogql](https://github.com/uhyo/nitrogql)の開発中にこの問題に直面しました。"
- **Current Gap**: AI articles mention ecosystem but don't reference "筆者's own projects"

### 4. **Strategic Use of Bold**
- **Pattern**: **Bold** is used very specifically for:
  - Key terms in introduction (first mention)
  - Technical concepts being introduced
  - Emphasis on important findings
- **Current Gap**: AI articles may under-use or over-use bold inconsistently

### 5. **Result Documentation Pattern**
- **Pattern**: Code example → "このコードに対して...を実行すると、〜でした。"
- **Example**: "このコードに対して`biome lint`を実行すると、なんとエラーは検知されませんでした。個人的にはちょっとびっくりしました。"
- **Current Gap**: AI articles describe code but may not follow this specific result-documentation rhythm

### 6. **:::details and :::message Blocks**
- **Pattern**: Uses Zenn-specific formatting for asides
- **Example**: ":::details Experimentalって書いてあるけど？" for digressions
- **Example**: ":::message" for important caveats
- **Current Gap**: AI articles don't use these Zenn-specific formatting blocks

### 7. **Reflective, Forward-Looking Conclusions**
- **Pattern**: Summary of findings + Personal reflection + Future outlook
- **Example**: "筆者としては、これからどうなるかまた見守っていきたいと思います。"
- **Current Gap**: AI articles may end more definitively without this forward-looking uncertainty

### 8. **Self-Promotion Style**
- **Pattern**: Casual, direct promotion of own work
- **Example**: "最終的に筆者が開発した`async-object-stack`を宣伝します。"
- **Example**: "この話はSoftware Design 9月号の非同期処理特集でも少し触れたので、ぜひそちらも読んでみてください（宣伝）。"
- **Current Gap**: AI articles don't have "own projects" to promote

### 9. **"筆者は" Usage Pattern**
- **Pattern**: Used for:
  - Personal experiences with specific projects
  - Subjective reactions ("筆者は...が一番驚きだったのですが")
  - Forward-looking statements ("筆者としては、...と思います")
- **Current Gap**: May be using "筆者" too generically without the specific contexts uhyo uses

### 10. **Embedded Meta-Commentary**
- **Pattern**: Comments on the writing process itself
- **Example**: "残念ながら、この場合は..."
- **Example**: "個人的にはちょっとびっくりしました"
- **Example**: "答えは、残念ながら..."
- **Current Gap**: AI articles may not have this layer of meta-commentary on findings

---

## Season 3 Architecture Changes

### New Review Focus: Author Voice Verification

The Reviewer Agent will add a new **STEP 2.5: Author Voice Analysis** between quantitative analysis and compliance checking:

**STEP 2.5: Author Voice Analysis**
- Check for uhyo-specific opening pattern ("皆さんこんにちは。" + context)
- Verify systematic investigation structure (simple → complex examples)
- Look for personal project references (even if fictional)
- Check bold usage patterns (first mention of key terms)
- Verify result documentation rhythm
- Check for :::details and :::message blocks where appropriate
- Analyze conclusion style (reflective + forward-looking)
- Verify "筆者" usage contexts
- Check for meta-commentary on findings

**Scoring Impact**:
- Missing 3+ author-specific patterns: Cap at 7.5/10
- Missing 1-2 author-specific patterns: Cap at 8.5/10
- All patterns present: Can achieve 9.0+/10

### Updated Style Guide Structure

The style guide will add a new top-level section:

```
## 👤 AUTHOR VOICE: uhyo-specific patterns (Season 3)
   - Opening formula
   - Systematic investigation structure
   - Personal project integration
   - Bold usage strategy
   - Result documentation rhythm
   - Zenn formatting blocks
   - Reflective conclusions
   - Self-promotion style
   - "筆者" usage contexts
   - Meta-commentary patterns
```

This section will be added AFTER the critical requirements but BEFORE the human-like writing patterns section.

**Rationale**: These are author-specific, not just human-like. They come after critical requirements because forbidden patterns and polite forms are still publication blockers.

---

## Updated Iteration Workflow

### Step 1-3: Same as Season 2
(Determine iteration, generate topic, create directory)

### Step 4: Invoke Writer Agent (UPDATED)

```
Task:
- subagent_type: general-purpose
- description: Generate technical article
- prompt: |
    You are the Writer Agent. Read your agent definition at .claude/agents/writer.md.

    Your task:
    1. Read the style guide at style_guide.md (now includes uhyo-specific voice patterns)
    2. Generate a technical article on the following topic: "{TOPIC}"
    3. Save the article to iterations/{N}/article.md

    **SEASON 3 FOCUS**: Replicate uhyo's distinctive voice and article structure patterns.
    The article should not only be human-quality, but specifically match uhyo's writing style.

    Key uhyo patterns to incorporate:
    - Opening: "皆さんこんにちは。" + recent context
    - Structure: Systematic investigation (simple → complex)
    - Voice: Personal project references, meta-commentary on findings
    - Formatting: Use :::details and :::message blocks for asides
    - Conclusion: Reflective + forward-looking uncertainty
```

### Step 5: Invoke Reviewer Agent (UPDATED)

```
Task:
- subagent_type: general-purpose
- description: Review article with human baseline comparison
- prompt: |
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
       - **Score deduction**: Missing 3+ patterns: cap at 7.5; Missing 1-2: cap at 8.5
    5. **STEP 3 - Compliance**: Check ALL rules in style_guide.md
    6. **STEP 4 - Holistic Review**: Comprehensive review
    7. **STEP 5 - Score**: Apply scoring rules (including author voice caps)
    8. Save review to iterations/{N}/review.md

    **CRITICAL**: The article must be both human-quality AND match uhyo's specific voice.
```

### Step 6: Invoke Style Guide Updater (SAME)
(No changes to this step)

### Step 7-9: Analyze Progress and Decide (UPDATED THRESHOLDS)

**Season 3 Success Criteria**:
- Overall quality score ≥ 9.0/10 for 2+ consecutive iterations
- Author voice verification: 8+ out of 10 uhyo-specific patterns present
- Reviews indicate articles match uhyo's specific style
- Both human-quality AND author voice consistency achieved

---

## Season 3 Iterations Plan

### Iteration 1-3: Establish Baseline
- Add uhyo-specific patterns to style guide
- Test whether Writer can incorporate basic patterns
- Identify which patterns are hardest to replicate
- Expected scores: 7.0-7.5/10 (author voice patterns still missing)

### Iteration 4-6: Refine Author Voice
- Focus on systematic investigation structure
- Improve personal project integration
- Perfect opening and conclusion patterns
- Expected scores: 7.5-8.5/10 (some patterns present)

### Iteration 7-9: Polish and Consistency
- All 10 author-specific patterns should be present
- Fine-tune the balance between patterns
- Achieve natural integration of uhyo's voice
- Expected scores: 8.5-9.0+/10 (author voice mastered)

### Success Point: 2 consecutive 9.0+ scores with full author voice

---

## Style Guide Management

### Current State
- Season 2 style guide: 350 lines (at capacity)
- Version: 1.8

### Season 3 Approach
- **Target**: Keep total under 400 lines
- **Strategy**: Add uhyo-specific patterns section (~50 lines)
- **Trade-off**: May need to consolidate some Season 2 content

### Priority Order (for space management)
1. 🔴 CRITICAL REQUIREMENTS (forbidden patterns, polite forms)
2. 👤 AUTHOR VOICE (Season 3 focus)
3. 📋 PRE-SUBMISSION CHECKLIST
4. 🟡 HUMAN-LIKE PATTERNS (can be slightly compressed)
5. 🟢 POLISH (lowest priority, can be trimmed)

---

## Key Differences from Season 2

| Aspect | Season 2 | Season 3 |
|--------|----------|----------|
| **Goal** | Human-quality writing | uhyo-specific voice |
| **Target Score** | 8.5+/10 | 9.0+/10 |
| **Style Guide Focus** | Generic human patterns | Author-specific patterns |
| **Review Focus** | Human vs AI tells | uhyo voice vs generic human |
| **Success Criteria** | Indistinguishable from humans | Indistinguishable from uhyo |
| **Pattern Count** | 11 AI tells to avoid | 10 uhyo patterns to match |

---

## Expected Challenges

### Challenge 1: Personal Project References
- **Problem**: AI can't reference real projects by uhyo
- **Solution**: Create plausible fictional projects with similar integration style
- **Alternative**: Focus on thought processes rather than specific projects

### Challenge 2: Balancing Generic Human + Specific Author
- **Problem**: Too much focus on uhyo patterns might sacrifice Season 2 gains
- **Solution**: Author voice is layered ON TOP of human quality, not replacing it
- **Approach**: Season 2 patterns are still mandatory; uhyo patterns are additions

### Challenge 3: Over-Formulaic Application
- **Problem**: Mechanically applying all 10 patterns might feel forced
- **Solution**: Emphasize natural integration; not all patterns needed in every article
- **Guideline**: 8/10 patterns is sufficient; perfect 10/10 might be too rigid

### Challenge 4: Style Guide Size
- **Problem**: Adding 50+ lines of author-specific patterns
- **Solution**: Consolidate Season 2 content where possible
- **Strategy**: Merge overlapping sections, use more concise examples

---

## Measurement and Tracking

### Quantitative Metrics
1. **Overall Quality Score**: 9.0+/10 target
2. **Author Voice Match**: 8+/10 uhyo patterns present
3. **です/ます Distribution**: Maintain 40-60% (Season 2 baseline)
4. **Forbidden Patterns**: Maintain ZERO violations

### Qualitative Metrics
1. **Opening Formula**: Present/Absent
2. **Investigation Structure**: Present/Partial/Absent
3. **Personal Integration**: Natural/Forced/Absent
4. **Meta-Commentary**: Frequent/Occasional/Missing
5. **Conclusion Style**: Reflective/Neutral/Definitive

### Success Dashboard (Track per Iteration)
```
Iteration N:
├── Overall Score: X.X/10
├── Author Voice: X/10 patterns
├── です/ます: XX count (XX%)
├── Forbidden Patterns: X violations
├── Key Strengths: [list]
└── Key Gaps: [list]
```

---

## Risk Mitigation

### Risk 1: Quality Regression
- **Risk**: Focus on author voice might break Season 2 achievements
- **Mitigation**: Keep Season 2 critical requirements unchanged and mandatory
- **Monitor**: Track です/ます and forbidden patterns every iteration

### Risk 2: Uncanny Valley
- **Risk**: Close-but-not-quite uhyo voice might feel worse than generic human
- **Mitigation**: Allow graceful degradation; 8/10 patterns is acceptable
- **Fallback**: If scores drop below 8.0, pause and reassess

### Risk 3: Over-Optimization
- **Risk**: Fitting too specifically to training data (overfitting to uhyo)
- **Mitigation**: Still maintain variety in topics and natural imperfections
- **Check**: Ask "Would uhyo write this?" not "Did uhyo write exactly this?"

---

## Timeline and Expectations

### Conservative Estimate: 9-12 Iterations
- Iterations 1-3: Establish patterns (7.0-7.5/10)
- Iterations 4-6: Improve voice (7.5-8.5/10)
- Iterations 7-9: Polish (8.5-9.0/10)
- Iterations 10-12: Consistency validation

### Optimistic Estimate: 6-8 Iterations
- If Writer Agent quickly adapts to author-specific patterns
- If most patterns naturally align with existing human-quality writing

### Pessimistic Estimate: 12-15 Iterations
- If author voice conflicts with Season 2 patterns
- If significant style guide restructuring needed
- If balance between generic human and specific author is difficult

---

## Next Steps

1. **Update style_guide.md**: Add "👤 AUTHOR VOICE" section with uhyo-specific patterns
2. **Update .claude/agents/reviewer.md**: Add STEP 2.5 for author voice analysis
3. **Update CLAUDE.md**: Document Season 3 changes to orchestrator workflow
4. **Begin Iteration 1**: Test the new system with a uhyo-style article

---

## Success Definition

Season 3 succeeds when:

✅ **Generated articles achieve 9.0+/10** for 2+ consecutive iterations
✅ **8+ out of 10 uhyo-specific patterns** consistently present
✅ **Season 2 quality maintained**: Zero forbidden patterns, 40-60% です/ます
✅ **Reviewer feedback**: "This reads like a uhyo article" not just "This is human-quality"
✅ **Blind test ready**: Articles could plausibly be mistaken for uhyo's work

At that point, we will have achieved not just human-quality technical writing, but **author voice cloning** through iterative refinement.

---

**Season 3 Start Date**: [To be determined]
**Expected Duration**: 6-12 iterations
**Target Completion**: When 2 consecutive articles score 9.0+ with full author voice
