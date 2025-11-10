# AI-Driven Technical Article Generation - Orchestrator Guide (Season 4)

This document guides Claude Code instances to orchestrate the iterative process of generating human-quality technical articles through continuous refinement.

## Project Goal

**Season 4 Goal**: Generate Japanese technical articles about TypeScript, JavaScript, React, and frontend technologies that match **uhyo's specific writing voice (9.0+/10) while using ONLY authentic personal reference patterns (ZERO fabricated experiences)**.

The project uses an iterative feedback loop with three specialized sub-agents to progressively improve article quality. Season 4 builds on Season 3's uhyo-voice success by adding a critical authenticity constraint: eliminating all fabricated personal experiences.

**Evolution**:
- Season 1: Basic technical articles
- Season 2: Human-quality articles (8.0-8.2/10) - indistinguishable from humans
- Season 3: uhyo-specific voice (9.5/10 achieved) - indistinguishable from uhyo's articles
- **Season 4**: uhyo-voice + authenticity (9.0+/10 target) - high quality WITHOUT fabricated experiences

**Season 4 Challenge**: Season 3 achieved 9.5/10 by matching uhyo's voice, but included **fabricated personal experiences** (fake projects, false metrics, unverified claims) that decrease article reliability. Season 4 solves this by constraining "筆者" usage to authentic patterns only.

## Architecture

### Sub-Agents

Three specialized agents work together in each iteration:

1. **Writer Agent** (`.claude/agents/writer.md`)
   - Generates technical articles based on topics and the current style guide
   - Uses ONLY `style_guide.md` as its source of writing guidelines
   - Must NOT access `human-bench/articles/` directly
   - **Season 4**: Must use ONLY authentic "筆者" patterns (no fabricated experiences)
   - Outputs: `iterations/{N}/article.md`

2. **Reviewer Agent** (`.claude/agents/reviewer.md`)
   - Reviews generated articles by comparing them to human benchmarks
   - The ONLY agent that reads `human-bench/articles/`
   - **MUST NOT read previous iterations** to maintain review independence
   - Provides detailed feedback and quality scores
   - **Season 4**: Includes fabrication detection (STEP 2.6)
   - Outputs: `iterations/{N}/review.md`

3. **Style Guide Updater Agent** (`.claude/agents/style_guide_updater.md`)
   - Refines the style guide based on review feedback
   - Translates reviewer insights into actionable guidelines
   - **CAN access previous iterations** to track patterns and rule effectiveness
   - **Season 4**: Must NOT add guidelines that encourage fabrication
   - Updates: `style_guide.md`

### Key Resources

- **Style Guide**: `style_guide.md` - Evolving guidelines for article generation (Writer's primary input)
- **Human Benchmarks**: `human-bench/articles/` - Reference articles (Reviewer only)
- **Iteration Output**: `iterations/{N}/` - Each iteration contains:
  - `article.md` - Generated article
  - `review.md` - Review feedback
  - `style_guide.md` - Copy of the style guide used for this iteration (for version tracking)
  - `changelog.md` - Changes made to the style guide in this iteration

### Information Flow

The architecture maintains a clean separation of concerns:

```
Human Benchmarks → Reviewer → Style Guide → Writer → Article
                        ↓                       ↓
                    Review ←──────────────── Article
                        ↓
              Style Guide Updater → Updated Style Guide
```

This ensures the Writer learns patterns from human articles **indirectly through the style guide**, not by directly copying from examples.

**Season 4 Addition**: Reviewer includes fabrication detection to ensure Writer uses only authentic patterns.

## Orchestrator Workflow

As the orchestrator, you manage the complete iterative process:

### Step 1: Determine Current Iteration

```bash
# Find the next iteration number
ls -d iterations/* 2>/dev/null | wc -l
```

Calculate `N = count + 1` for the new iteration.

### Step 2: Generate a Topic

Create a specific, interesting technical topic related to:
- TypeScript features or type system
- React patterns, hooks, or architecture
- JavaScript language features
- Frontend development concepts
- Build tools or ecosystem topics

**Topic Guidelines:**
- Be specific (not "React hooks" but "React useEffectの依存配列の最適化")
- Choose current or recent technologies when possible
- Vary topic types across iterations (new features, deep dives, comparisons, patterns)
- Consider what would make an engaging blog post

**Example Topics:**
- "TypeScript 5.3の型推論改善について"
- "ReactのServer Componentsとクライアントコンポーネントの使い分け"
- "JavaScriptのPromiseとasync/awaitの内部実装"

### Step 3: Create Iteration Directory and Archive Style Guide

```bash
mkdir -p iterations/{N}
cp style_guide.md iterations/{N}/style_guide.md
```

This archives the current version of the style guide for this iteration, allowing you to track which guidelines were in effect when each article was generated.

### Step 4: Invoke Writer Agent

Use the Task tool to invoke the Writer Agent:

```
Task:
- subagent_type: general-purpose
- description: Generate technical article
- prompt: |
    You are the Writer Agent. Read your agent definition at .claude/agents/writer.md.

    Your task:
    1. Read the style guide at style_guide.md (v3.0 - Season 4 with authenticity constraints)
    2. Generate a technical article on the following topic: "{TOPIC}"
    3. Save the article to iterations/{N}/article.md

    **SEASON 4 CRITICAL CONSTRAINT**: You are an AI with no real past experiences or projects.

    **FORBIDDEN**: Do NOT fabricate:
    - Past projects or implementations you "worked on"
    - Specific metrics from implementations (e.g., "70% performance improvement")
    - Testing/verification claims (e.g., "I tested on old devices")
    - Detailed scenarios that didn't happen
    - Specific timelines (e.g., "last year I encountered...")

    **ALLOWED**: You CAN authentically:
    - React to findings shown in THIS article (e.g., "筆者はここの結果が驚きだった")
    - Express opinions on technology direction (e.g., "筆者の考えでは")
    - Share concerns about the future (e.g., "筆者は〜について心配している")
    - Admit limitations (e.g., "筆者はまだ試していない")
    - Name your personal terminology (e.g., "筆者はこれを〜と呼んでいます")
    - State vague preferences (e.g., "筆者はこちらが好みです")

    **"筆者" target**: 3-6 uses (reduced from Season 3's 5-8 due to authenticity constraints)

    Key uhyo patterns to incorporate:
    - Opening: "皆さんこんにちは。" + recent context + topic with bold
    - Structure: Systematic investigation (simple → complex examples)
    - Voice: Meta-commentary on findings (reactions to what you discover in the article)
    - "筆者" usage: 3-6 times using ONLY authentic patterns
    - Formatting: Use :::details and :::message blocks for asides
    - Conclusion: Reflective + forward-looking uncertainty

    Follow all guidelines in your agent definition and the style guide.
    The article should be in Zenn format, written in Japanese, match uhyo's voice, and contain ZERO fabricated experiences.
```

### Step 5: Invoke Reviewer Agent

After the Writer completes, invoke the Reviewer Agent:

```
Task:
- subagent_type: general-purpose
- description: Review article with human baseline comparison
- prompt: |
    You are the Reviewer Agent. Read your agent definition at .claude/agents/reviewer.md.

    **SEASON 4 FOCUS**: Verify both author voice AND authenticity (ZERO fabricated experiences).

    Your task:
    1. **STEP 0 - Pattern Discovery**: Look for any new uhyo-specific patterns not yet documented
    2. **STEP 1 - Human Baseline**: Sample 3-5 human articles, document linguistic patterns
       - MANDATORY: Count です/ます sentence endings (baseline: 15-70)
    3. **STEP 2 - Quantitative Analysis**: Pattern analysis with line numbers
       - MANDATORY FIRST CHECK: Count です。and ます。(<10 = blocker)
    4. **STEP 2.5 - Author Voice Analysis**: Verify uhyo-specific patterns
       - Opening formula check ("皆さんこんにちは。" + context)
       - Systematic investigation structure (simple → complex)
       - Meta-commentary presence (NOT fake project references)
       - "筆者" usage contexts (3-6 times, authentic patterns only)
       - Zenn formatting blocks (:::details, :::message)
       - Reflective conclusion style
       - Strategic bold usage (3-5 terms)
       - Code-driven narrative
       - Title style
       - **Calculate author voice score (0-10 points)**
    5. **STEP 2.6 - Fabricated Experience Detection (NEW - Season 4)**: CRITICAL CHECK
       - Extract all "筆者" usage instances with line numbers
       - Classify each: ✅ ALLOWED (reaction/opinion/concern/limitation/terminology/preference)
                        ❌ FORBIDDEN (past project/implementation claim/verification/scenario/timeline)
       - Forbidden pattern indicators:
         * "筆者は以前", "筆者が〜した", "筆者の〜プロジェクトで"
         * Specific metrics: "〜%削減", "〜倍速くなった"
         * Verification claims: "筆者が確認した限り", "筆者が試したところ"
         * Rich scenarios with fake context
         * Timeline specificity: "去年", "先月", "2年前"
       - **Calculate Fabrication Score**: PASS (0 forbidden) or BLOCKER (1+ forbidden)
       - **ONE forbidden instance = PUBLICATION BLOCKER (max 7.0/10)**
    6. **STEP 3 - Compliance**: Check ALL rules in style_guide.md
    7. **STEP 4 - Holistic Review**: Comprehensive review
    8. **STEP 5 - Score**: Apply Season 4 three-layer scoring
       - Base Score (Season 2 criteria)
       - Author Voice Cap (from STEP 2.5, adjusted for 3-6 "筆者" frequency)
       - Fabrication Penalty (from STEP 2.6)
       - Final Score = min(Base Score, Author Voice Cap) IF Fabrication = PASS
       - Final Score = max 7.0/10 IF Fabrication = BLOCKER
    9. Save review to iterations/{N}/review.md

    **CRITICAL**: The article must be human-quality (Season 2) AND match uhyo's voice (Season 3) AND contain zero fabricated experiences (Season 4).

    **IMPORTANT**:
    - Do NOT read or reference previous iterations. Each review must be independent.
    - Provide quantitative evidence for all patterns
    - Fabrication detection is ZERO-TOLERANCE (1 violation = blocker)
    - Path to 9.0+ requires: Base ≥9.0 AND Author Voice ≥7 points AND Fabrication = PASS
```

### Step 6: Invoke Style Guide Updater Agent

After the review is complete, invoke the Style Guide Updater:

```
Task:
- subagent_type: general-purpose
- description: Update style guide and create changelog
- prompt: |
    You are the Style Guide Updater Agent. Read your agent definition at .claude/agents/style_guide_updater.md.

    Your task:
    1. Read the current style guide at style_guide.md
    2. Read the review at iterations/{N}/review.md
    3. Read the article at iterations/{N}/article.md for context
    4. Update style_guide.md based on the review feedback
    5. Create a detailed changelog at iterations/{N}/changelog.md documenting all changes made

    **SEASON 4 CRITICAL CONSTRAINT**:
    - DO NOT add guidelines that encourage fabrication of experiences
    - If review identifies fabricated patterns, ADD to forbidden list with examples
    - Emphasize authentic patterns over fabricated ones
    - Focus on strengthening allowed "筆者" usage patterns

    Focus on actionable, specific improvements. Add concrete guidelines that address systematic issues.
    The changelog should explain what changed, why it changed, and what issues it addresses.
```

### Step 7: Analyze Progress

After the iteration completes:

1. Read the review to understand the quality scores
2. Check the Fabrication Score (PASS or BLOCKER)
3. Read the updated style guide to see what changed
4. Assess whether quality targets have been met

### Step 8: Decide Whether to Continue

**Season 4 Target**: Articles that match uhyo's voice with ZERO fabricated experiences (9.0+/10)

**Continue iterating if:**
- Overall quality score < 9.0/10
- Fabrication Score = BLOCKER (any fabricated experiences present)
- Author voice score < 7 points (adjusted for 3-6 "筆者" frequency)
- Significant gaps remain between AI articles and uhyo's authentic voice
- Fewer than 12 iterations have been completed

**Stop iterating if:**
- Overall quality score ≥ 9.0/10 for 2+ consecutive iterations
- Fabrication Score = PASS for ALL iterations (especially the successful ones)
- Author voice score ≥ 7 points (with 3-6 authentic "筆者" uses)
- Reviews indicate articles match uhyo's voice without fabricated content
- No meaningful improvements in recent iterations
- 12+ iterations completed with diminishing returns

**Season 4 Requirements for Success**:
- Base Score (Season 2): ≥ 9.0/10 (human-quality foundation)
- Author Voice: ≥ 7 points (uhyo patterns with 3-6 authentic "筆者" uses)
- Fabrication Score: PASS (0 forbidden instances)
- Final Score: ≥ 9.0/10 (all three layers passing)

### Step 9: Iterate or Report

**If continuing:**
- Return to Step 1 with N+1
- Generate a new topic (different from previous topics)
- Run the full workflow again

**If stopping:**
- Summarize the overall progress
- Report final quality metrics including fabrication scores
- Highlight key improvements made to the style guide
- Document the working formula for authentic uhyo-voice articles
- Note remaining challenges if any

## Orchestration Best Practices

### Topic Selection

- Keep a mental list of used topics to ensure variety
- Balance different difficulty levels
- Mix feature explanations, deep dives, and practical patterns
- Choose topics that showcase different writing styles

### Agent Communication

- Provide clear, specific prompts to each agent
- Ensure each agent has all necessary file paths
- Wait for each agent to complete before invoking the next
- Read agent outputs to verify completion

### Progress Tracking

- Track quality scores across iterations
- **Track fabrication scores** (Season 4 addition)
- Note which types of feedback recur (systematic issues)
- Monitor style guide growth and evolution
- Watch for plateaus in improvement

### Error Handling

If an agent fails or produces invalid output:
- Read the agent's output to understand what went wrong
- Provide clarifying instructions and retry
- Adjust prompts if needed
- Update agent definitions if systematic issues arise

## Iteration Tracking

Keep track of iterations mentally or in outputs:

- **Iteration N**: Topic, Quality Scores, Fabrication Score (PASS/BLOCKER), Key Issues, Style Guide Updates
- Monitor trends in quality improvement
- Monitor trends in fabrication elimination
- Identify if certain types of articles are harder to generate authentically

## Example Iteration Flow (Season 4)

```
Iteration 1:
├── Topic: "TypeScript 5.4の新機能について"
├── Archive style guide → iterations/1/style_guide.md
├── Write article → iterations/1/article.md
├── Review article → iterations/1/review.md (Quality: 7.5/10, Fabrication: BLOCKER - 2 fake project refs)
├── Update root style guide + Create changelog → iterations/1/changelog.md (Added fabrication examples to forbidden list)
└── Continue (fabrication blocker + quality below threshold)

Iteration 2:
├── Topic: "ReactのuseEffectの再実行タイミング"
├── Archive style guide → iterations/2/style_guide.md
├── Write article → iterations/2/article.md
├── Review article → iterations/2/review.md (Quality: 8.0/10, Fabrication: PASS - all authentic patterns)
├── Update root style guide + Create changelog → iterations/2/changelog.md
└── Continue (quality improving, fabrication eliminated, but below 9.0 target)

...

Iteration 8:
├── Topic: "JavaScript Proxyの実践パターン"
├── Archive style guide → iterations/8/style_guide.md
├── Write article → iterations/8/article.md
├── Review article → iterations/8/review.md (Quality: 9.2/10, Fabrication: PASS)
├── Update root style guide + Create changelog → iterations/8/changelog.md
└── Continue one more iteration to confirm consistency

Iteration 9:
├── Topic: "TypeScriptの型推論を活用したコード設計"
├── Archive style guide → iterations/9/style_guide.md
├── Write article → iterations/9/article.md
├── Review article → iterations/9/review.md (Quality: 9.1/10, Fabrication: PASS)
├── Create changelog → iterations/9/changelog.md
└── Success! Quality 9.0+ with ZERO fabrications for 2 consecutive iterations
```

## Success Criteria

**Season 4 Success**: The iterative process has succeeded when generated articles:

1. **Score ≥ 9.0/10 overall** for 2+ consecutive iterations
2. **Fabrication Score = PASS** (0 forbidden instances) for ALL successful iterations
3. **Author Voice Score ≥ 7 points** (adjusted for 3-6 authentic "筆者" uses)
4. **Receive reviewer feedback** indicating articles match uhyo's voice without fabricated content
5. **Show mastery of**:
   - Natural Japanese technical writing (Season 2 baseline)
   - Engaging, conversational tone
   - Accurate technical content
   - Appropriate structure and flow
   - Authentic voice without AI tells
   - **uhyo-specific patterns** (Season 3):
     - Opening formula ("皆さんこんにちは。" + context)
     - Systematic investigation structure
     - Meta-commentary on findings
     - Appropriate "筆者" usage (3-6x, authentic patterns ONLY)
     - Reflective forward-looking conclusions
     - Zenn formatting blocks where appropriate
     - Strategic bold usage
     - Code-driven narrative
     - uhyo-style titles
   - **Authenticity constraint** (Season 4):
     - ZERO fabricated past projects
     - ZERO false implementation claims
     - ZERO unverified testing claims
     - ZERO detailed fake scenarios
     - ZERO specific fake timelines

## Getting Started

To begin Season 4, start with Step 1. The orchestrator (you) will manage all coordination between agents and track progress toward **authentic uhyo-voice article generation**.

**Season 4 Focus**: Each article should not only be human-quality (Season 2) and match uhyo's voice (Season 3), but also maintain honesty about the AI's limitations by using ONLY authentic personal reference patterns (Season 4).

**The Challenge**: Can we maintain 9.0+ quality while adding a constraint that directly conflicts with learned patterns? Season 3 learned that personal anecdotes increase quality. Season 4 tests whether quality can be maintained using only authentic patterns.

Good luck! This is a challenging but important goal: responsible AI content generation that doesn't pretend to have experiences it doesn't have.

---

**Version**: 4.0 (Season 4: Authenticity Constraint)
**Previous Achievement**: Season 3 reached 9.5/10 (3 instances)
**Current Goal**: Maintain 9.0+ quality with ZERO fabricated experiences
