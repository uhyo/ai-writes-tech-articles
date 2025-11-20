# AI-Driven Technical Article Generation - Orchestrator Guide (Season 4)

This document guides Claude Code instances to orchestrate the iterative process of generating human-quality technical articles through continuous refinement.

## Project Goal

**Season 4 Goal**: Generate Japanese technical articles about TypeScript, JavaScript, React, and frontend technologies that match **uhyo's specific writing voice** (9.0+/10) AND are **factually reliable** (Reliability ≥ 8.5/10).

The project uses an iterative feedback loop with specialized sub-agents to progressively improve article quality. Season 4 builds on Season 3's uhyo-voice achievement by adding reliability/honesty requirements.

**Season 4 Enhancement**: The review process now uses **five specialized agents** (four parallel reviewers + score synthesizer) with a new **Reliability Reviewer** to detect fabrications and ensure honesty.

**Evolution**:
- Season 1: Basic technical articles
- Season 2: Human-quality articles (8.0-8.2/10) - indistinguishable from humans
- Season 3: uhyo-specific voice (9.0+/10) - indistinguishable from uhyo's articles
- **Season 4**: Reliable uhyo-voice (9.0+/10 + Reliability 8.5+/10) - honest AND engaging

## Architecture

### Sub-Agents

Seven specialized agents work together in each iteration:

1. **Writer Agent** (`.claude/agents/writer.md`)
   - Generates technical articles based on topics and the current style guide
   - Uses ONLY `style_guide.md` as its source of writing guidelines
   - Must NOT access `human-bench/articles/` directly
   - Outputs: `iterations/{N}/article.md`

2. **Technical Quality Reviewer** (`.claude/agents/technical_reviewer.md`)
   - Evaluates technical accuracy, code quality, and educational value
   - Focuses exclusively on technical content correctness
   - **MUST NOT read previous iterations** to maintain independence
   - Outputs: `iterations/{N}/technical_review.md`

3. **Linguistic Quality Reviewer** (`.claude/agents/linguistic_reviewer.md`)
   - Assesses natural Japanese patterns and human-likeness (Season 2 baseline)
   - Performs quantitative analysis (です/ます counts, etc.)
   - Checks style guide compliance and detects AI tells
   - Reads `human-bench/articles/` to establish baseline
   - **MUST NOT read previous iterations** to maintain independence
   - Outputs: `iterations/{N}/linguistic_review.md`

4. **Author Voice Reviewer** (`.claude/agents/author_voice_reviewer.md`)
   - Verifies uhyo-specific voice patterns (Season 3 focus)
   - Scores author voice presence (0-10 points)
   - Determines score cap based on voice authenticity
   - Reads `human-bench/articles/` for pattern verification
   - **MUST NOT read previous iterations** to maintain independence
   - Outputs: `iterations/{N}/author_voice_review.md`

5. **Reliability Reviewer** (`.claude/agents/reliability_reviewer.md`) **🆕 SEASON 4**
   - Detects fabricated experiences, false verification claims, and wrong references
   - Ensures factual honesty while preserving engaging voice
   - Focuses exclusively on truthfulness
   - **MUST NOT read previous iterations** to maintain independence
   - Outputs: `iterations/{N}/reliability_review.md`

6. **Score Synthesizer** (`.claude/agents/score_synthesizer.md`)
   - Combines **four** specialized reviews into unified assessment
   - Calculates Base Quality Score (Technical + Linguistic + **Reliability**)
   - Applies Author Voice Cap using Season 4 two-layer formula
   - Prioritizes recommendations across all dimensions
   - Outputs: `iterations/{N}/review.md` (unified comprehensive review)

7. **Style Guide Updater Agent** (`.claude/agents/style_guide_updater.md`)
   - Refines the style guide based on unified review feedback
   - Translates reviewer insights into actionable guidelines
   - **CAN access previous iterations** to track patterns and rule effectiveness
   - Updates: `style_guide.md`

### Key Resources

- **Style Guide**: `style_guide.md` - Evolving guidelines for article generation (Writer's primary input)
- **Human Benchmarks**: `human-bench/articles/` - Reference articles (Linguistic & Author Voice Reviewers)
- **Iteration Output**: `iterations/{N}/` - Each iteration contains:
  - `article.md` - Generated article
  - `technical_review.md` - Technical quality assessment
  - `linguistic_review.md` - Linguistic quality assessment
  - `reliability_review.md` - **🆕 SEASON 4:** Reliability assessment
  - `author_voice_review.md` - Author voice assessment
  - `review.md` - Unified comprehensive review (from Score Synthesizer)
  - `style_guide.md` - Copy of the style guide used for this iteration (for version tracking)
  - `changelog.md` - Changes made to the style guide in this iteration

### Information Flow

The architecture maintains a clean separation of concerns with parallel review:

```
                                    ┌─ Technical Reviewer ──┐
                                    │                        │
Human Benchmarks ──┬─→ Linguistic Reviewer ──┤              │
                   │                           │              ├─→ Score Synthesizer
                   └─→ Author Voice Reviewer ─┤              │          ↓
                                               │              │    Unified Review
Style Guide ──→ Writer ──→ Article ────────┬─→ Reliability Reviewer ─┘         ↓
                                            │                          Style Guide Updater
                                            └──────────────────────────────────→ ↓
                                                                          Updated Style Guide
```

**Key Points (Season 4)**:
- **Four** reviewers run in **parallel** for efficiency (Technical, Linguistic, Reliability, Author Voice)
- Each reviewer has a **focused scope** to prevent context overload
- **Reliability Reviewer** ensures honesty without fabrications (NEW!)
- Score Synthesizer **combines** the four reviews into unified assessment
- Writer learns patterns from human articles **indirectly through the style guide**

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
    1. Read the style guide at style_guide.md (now includes uhyo-specific voice patterns)
    2. Generate a technical article on the following topic: "{TOPIC}"
    3. Save the article to iterations/{N}/article.md

    **SEASON 3 FOCUS**: Replicate uhyo's distinctive voice and article structure patterns.
    The article should not only be human-quality, but specifically match uhyo's writing style.

    Key uhyo patterns to incorporate:
    - Opening: "皆さんこんにちは。" + recent context + topic with bold
    - Structure: Systematic investigation (simple → complex examples)
    - Voice: Personal project references, meta-commentary on findings
    - "筆者" usage: 3-8 times in appropriate contexts
    - Formatting: Use :::details and :::message blocks for asides
    - Conclusion: Reflective + forward-looking uncertainty

    Follow all guidelines in your agent definition and the style guide.
    The article should be in Zenn format, written in Japanese, and match uhyo's specific voice.
```

### Step 5: Invoke Review Agents (Parallel)

After the Writer completes, invoke **all four review agents in parallel** for efficiency:

**IMPORTANT**: Use a single message with **four** Task tool calls to run these in parallel.

#### 5a. Technical Quality Reviewer

```
Task:
- subagent_type: general-purpose
- description: Review technical quality and accuracy
- prompt: |
    You are the Technical Quality Reviewer. Read your agent definition at .claude/agents/technical_reviewer.md.

    Your task:
    1. Read the article at iterations/{N}/article.md
    2. Assess technical accuracy, code quality, and educational value
    3. Check for technical errors or misconceptions
    4. Evaluate code examples for correctness and practicality
    5. Assess article structure and pedagogical effectiveness
    6. Assign a technical quality score (0-10)
    7. Save your review to iterations/{N}/technical_review.md

    Focus exclusively on technical content. Do NOT assess linguistic patterns or author voice.
```

#### 5b. Linguistic Quality Reviewer

```
Task:
- subagent_type: general-purpose
- description: Review linguistic quality and human-likeness
- prompt: |
    You are the Linguistic Quality Reviewer. Read your agent definition at .claude/agents/linguistic_reviewer.md.

    Your task:
    1. Sample 3-5 articles from human-bench/articles/ to establish baseline
       - MANDATORY: Count です/ます endings (baseline: 15-70)
    2. Read the article at iterations/{N}/article.md
    3. Perform quantitative analysis:
       - Count です。and ます。(<10 = critical failure)
       - Analyze sentence patterns and variety
       - Document all findings with line numbers
    4. Check compliance with ALL rules in style_guide.md
    5. Detect AI tells and unnatural patterns
    6. Assess overall human-likeness (Season 2 baseline)
    7. Assign linguistic quality score (0-10)
    8. Save your review to iterations/{N}/linguistic_review.md

    Focus exclusively on linguistic quality. Do NOT assess technical content or author voice.

    **IMPORTANT**: Do NOT read previous iterations. Each review must be independent.
```

#### 5c. Author Voice Reviewer

```
Task:
- subagent_type: general-purpose
- description: Review uhyo-specific voice patterns
- prompt: |
    You are the Author Voice Reviewer. Read your agent definition at .claude/agents/author_voice_reviewer.md.

    Your task:
    1. Read the article at iterations/{N}/article.md
    2. Evaluate presence of 10 uhyo-specific patterns:
       - Opening formula, systematic investigation, personal projects
       - Meta-commentary, "筆者" usage, Zenn formatting
       - Reflective conclusion, strategic bold, code-driven narrative, title style
    3. Assign points (0.0, 0.5, or 1.0) for each pattern with evidence
    4. Calculate total Author Voice Score (0-10 points)
    5. Determine score cap based on voice score:
       - 9-10 pts: no cap | 7-8 pts: 8.5 cap | 5-6 pts: 8.0 cap | 3-4 pts: 7.5 cap | 0-2 pts: 7.0 cap
    6. Assess authenticity beyond checklist patterns
    7. Save your review to iterations/{N}/author_voice_review.md

    Focus exclusively on uhyo's voice. Do NOT assess technical content or general linguistic quality.

    **IMPORTANT**: Do NOT read previous iterations. Each review must be independent.
```

#### 5d. Reliability Reviewer **🆕 SEASON 4**

```
Task:
- subagent_type: general-purpose
- description: Review reliability and factual honesty
- prompt: |
    You are the Reliability Reviewer. Read your agent definition at .claude/agents/reliability_reviewer.md.

    Your task:
    1. Read the article at iterations/{N}/article.md
    2. Identify fabricated personal experiences
    3. Flag false verification/testing claims ("実行すると〜となりました")
    4. Check external references (GitHub issues, docs)
    5. Assess conditional language usage ("はずです", "と考えられます")
    6. Count reliability violations:
       - CRITICAL: Fabricated experiences, false claims, wrong refs (-1.0 to -2.0 each)
       - MAJOR: Vague fabrications, missing conditionals (-0.5 to -0.8 each)
    7. Assign reliability score (0-10)
       - <6.0 = UNPUBLISHABLE (publication blocker)
    8. Save your review to iterations/{N}/reliability_review.md

    Focus exclusively on truthfulness. Do NOT assess technical correctness or writing style.

    **IMPORTANT**: Do NOT read previous iterations. Each review must be independent.
```

### Step 5.5: Invoke Score Synthesizer

After all **four** reviewers complete, invoke the Score Synthesizer:

```
Task:
- subagent_type: general-purpose
- description: Synthesize reviews and calculate final score
- prompt: |
    You are the Score Synthesizer. Read your agent definition at .claude/agents/score_synthesizer.md.

    Your task:
    1. Read all FOUR specialized reviews:
       - iterations/{N}/technical_review.md
       - iterations/{N}/linguistic_review.md
       - iterations/{N}/reliability_review.md (🆕 SEASON 4)
       - iterations/{N}/author_voice_review.md
    2. Read the article at iterations/{N}/article.md for context
    3. Calculate Base Quality Score (Season 4 Formula):
       - Base = (Technical × 0.35) + (Linguistic × 0.5) + (Reliability × 0.15)
    4. Check publication blocker:
       - If Reliability < 6.0 → UNPUBLISHABLE
    5. Apply Author Voice Cap:
       - Final Score = min(Base Score, Voice Cap)
    6. Synthesize feedback across all FOUR dimensions
    7. Prioritize recommendations (blockers, high-impact, polish)
    8. Create comprehensive unified review
    9. Save to iterations/{N}/review.md

    Your unified review is what the Style Guide Updater will use to improve the guidelines.
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

    Focus on actionable, specific improvements. Add concrete guidelines that address systematic issues.
    The changelog should explain what changed, why it changed, and what issues it addresses.
```

### Step 7: Analyze Progress

After the iteration completes:

1. Read the review to understand the quality scores
2. Read the updated style guide to see what changed
3. Assess whether quality targets have been met

### Step 8: Decide Whether to Continue

**Season 3 Target**: Articles that match uhyo's specific voice (9.0+/10)

**Continue iterating if:**
- Overall quality score < 9.0/10
- **Reliability score < 8.5/10** (🆕 SEASON 4)
- Author voice score < 7 points (less than 70% of uhyo patterns present)
- Significant gaps remain between AI articles and uhyo's specific voice
- Reviews identify reliability violations (fabrications, false claims)
- Fewer than 12 iterations have been completed

**Stop iterating if:**
- Overall quality score ≥ 9.0/10 for 2+ consecutive iterations
- **Reliability score ≥ 8.5/10** (honest, minimal fabrications) (🆕 SEASON 4)
- Author voice score ≥ 8 points (80%+ of uhyo patterns present)
- Reviews indicate articles match uhyo's distinctive writing style AND are factually honest
- No meaningful improvements in recent iterations
- 12+ iterations completed with diminishing returns

**Season 4 Requirements for Success**:
- Technical Quality: ≥ 8.5/10 (accurate concepts)
- Linguistic Quality: ≥ 9.0/10 (human-like writing)
- **Reliability: ≥ 8.5/10** (honest, no significant fabrications) (🆕 SEASON 4)
- Base Score: ≥ 9.0/10 (weighted combination of above)
- Author Voice: ≥ 8 points (no cap applied)
- Final Score: ≥ 9.0/10 (all dimensions passing)

### Step 9: Iterate or Report

**If continuing:**
- Return to Step 1 with N+1
- Generate a new topic (different from previous topics)
- Run the full workflow again

**If stopping:**
- Summarize the overall progress
- Report final quality metrics
- Highlight key improvements made to the style guide
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

- **Iteration N**: Topic, Quality Scores, Key Issues, Style Guide Updates
- Monitor trends in quality improvement
- Identify if certain types of articles are harder to generate

## Example Iteration Flow

```
Iteration 1:
├── Topic: "TypeScript 5.0の新機能について"
├── Archive style guide → iterations/1/style_guide.md
├── Write article → iterations/1/article.md
├── Review (parallel - 4 reviewers):
│   ├── Technical review → iterations/1/technical_review.md (Tech: 7.0/10)
│   ├── Linguistic review → iterations/1/linguistic_review.md (Ling: 6.2/10)
│   ├── Reliability review → iterations/1/reliability_review.md (Reliability: 5.5/10) 🆕
│   └── Author voice review → iterations/1/author_voice_review.md (Voice: 4 pts, cap 7.5)
├── Synthesize → iterations/1/review.md (Final: 6.0/10)
├── Update root style guide + Create changelog → iterations/1/changelog.md
└── Continue (quality below threshold)

Iteration 2:
├── Topic: "Reactのカスタムフックのパターン"
├── Archive style guide → iterations/2/style_guide.md
├── Write article → iterations/2/article.md
├── Review (parallel):
│   ├── Technical review → iterations/2/technical_review.md (Tech: 7.5/10)
│   ├── Linguistic review → iterations/2/linguistic_review.md (Ling: 7.5/10)
│   └── Author voice review → iterations/2/author_voice_review.md (Voice: 5 pts, cap 8.0)
├── Synthesize → iterations/2/review.md (Final: 7.5/10)
├── Update root style guide + Create changelog → iterations/2/changelog.md
└── Continue (quality improving but below threshold)

...

Iteration 8:
├── Topic: "JavaScriptのProxyとReflectの実践的な使い方"
├── Archive style guide → iterations/8/style_guide.md
├── Write article → iterations/8/article.md
├── Review (parallel):
│   ├── Technical review → iterations/8/technical_review.md (Tech: 8.8/10)
│   ├── Linguistic review → iterations/8/linguistic_review.md (Ling: 9.0/10)
│   └── Author voice review → iterations/8/author_voice_review.md (Voice: 7 pts, cap 8.5)
├── Synthesize → iterations/8/review.md (Final: 8.5/10, capped by voice)
├── Update root style guide + Create changelog → iterations/8/changelog.md
└── Continue (close to threshold, voice needs strengthening)

Iteration 9:
├── Topic: "型安全なReactコンポーネントの設計パターン"
├── Archive style guide → iterations/9/style_guide.md
├── Write article → iterations/9/article.md
├── Review (parallel):
│   ├── Technical review → iterations/9/technical_review.md (Tech: 9.0/10)
│   ├── Linguistic review → iterations/9/linguistic_review.md (Ling: 9.2/10)
│   └── Author voice review → iterations/9/author_voice_review.md (Voice: 9 pts, no cap)
├── Synthesize → iterations/9/review.md (Final: 9.1/10, no cap applied!)
├── Create changelog → iterations/9/changelog.md
└── Success! Quality threshold met with strong uhyo voice
```

## Success Criteria

**Season 4 Success**: The iterative process has succeeded when generated articles:

1. **Score ≥ 9.0/10 overall** for 2+ consecutive iterations
2. **Reliability Score ≥ 8.5/10** (honest, minimal fabrications) **🆕 SEASON 4**
3. **Author Voice Score ≥ 8 points** (80%+ of uhyo-specific patterns present)
4. **Receive reviewer feedback** indicating articles match uhyo's distinctive writing style AND are factually honest
5. **Show mastery of**:
   - Natural Japanese technical writing (Season 2 baseline)
   - Engaging, conversational tone
   - Accurate technical content
   - Appropriate structure and flow
   - Authentic voice without AI tells
   - **Factual honesty** (no fabrications, conditional language, verified refs) **🆕 SEASON 4**
   - **uhyo-specific patterns**:
     - Opening formula ("皆さんこんにちは。" + context)
     - Systematic investigation structure
     - Personal project integration (generic/hypothetical in Season 4)
     - Meta-commentary on findings
     - Appropriate "筆者" usage (3-8x)
     - Reflective forward-looking conclusions
     - Zenn formatting blocks where appropriate
     - Strategic bold usage
     - Code-driven narrative
     - uhyo-style titles

## Getting Started

To begin Season 4, start with Step 1. The orchestrator (you) will manage all coordination between agents and track progress toward **reliable uhyo-voice article generation**.

**Season 4 Focus**: Each article should:
- Be human-quality (Season 2 baseline maintained)
- Match uhyo's distinctive writing patterns (Season 3 achievement maintained)
- **Be factually honest** (Season 4 NEW requirement)

The challenge: Maintain engaging, investigative voice while being honest about uncertainty.

Good luck! The goal is ambitious but achievable through systematic iteration and refinement. Season 3 achieved 9.0+/10 with perfect uhyo voice but had reliability issues. Season 4 aims for 9.0+/10 while being factually honest.
