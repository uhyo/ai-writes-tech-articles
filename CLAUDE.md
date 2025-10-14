# AI-Driven Technical Article Generation - Orchestrator Guide

This document guides Claude Code instances to orchestrate the iterative process of generating human-quality technical articles through continuous refinement.

## Project Goal

Generate Japanese technical articles about TypeScript, JavaScript, React, and frontend technologies that are **indistinguishable from human-written articles**. The project uses an iterative feedback loop with three specialized sub-agents to progressively improve article quality.

## Architecture

### Sub-Agents

Three specialized agents work together in each iteration:

1. **Writer Agent** (`.claude/agents/writer.md`)
   - Generates technical articles based on topics and the current style guide
   - Uses ONLY `style_guide.md` as its source of writing guidelines
   - Must NOT access `human-bench/articles/` directly
   - Outputs: `iterations/{N}/article.md`

2. **Reviewer Agent** (`.claude/agents/reviewer.md`)
   - Reviews generated articles by comparing them to human benchmarks
   - The ONLY agent that reads `human-bench/articles/`
   - **MUST NOT read previous iterations** to maintain review independence
   - Provides detailed feedback and quality scores
   - Outputs: `iterations/{N}/review.md`

3. **Style Guide Updater Agent** (`.claude/agents/style_guide_updater.md`)
   - Refines the style guide based on review feedback
   - Translates reviewer insights into actionable guidelines
   - **CAN access previous iterations** to track patterns and rule effectiveness
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
    1. Read the style guide at style_guide.md
    2. Generate a technical article on the following topic: "{TOPIC}"
    3. Save the article to iterations/{N}/article.md

    Follow all guidelines in your agent definition and the style guide.
    The article should be in Zenn format, written in Japanese, and indistinguishable from human-written articles.
```

### Step 5: Invoke Reviewer Agent

After the Writer completes, invoke the Reviewer Agent:

```
Task:
- subagent_type: general-purpose
- description: Review article with human baseline comparison
- prompt: |
    You are the Reviewer Agent. Read your agent definition at .claude/agents/reviewer.md.

    **CRITICAL: Follow the enhanced review methodology in your agent definition**

    Your task:
    1. **First**, randomly sample 3-5 articles from human-bench/articles/ to establish baseline
    2. Document human linguistic patterns in a "Human Baseline Observations" section
    3. Read the generated article at iterations/{N}/article.md
    4. Perform quantitative pattern analysis comparing AI article to human baseline
    5. Check compliance with ALL rules in style_guide.md (especially CRITICAL REQUIREMENTS)
    6. Provide comprehensive review with specific line numbers for violations
    7. Apply scoring rules from style_guide.md based on violation counts
    8. Save the review to iterations/{N}/review.md

    **IMPORTANT**:
    - Use the style guide as your reference for what patterns to check
    - Provide quantitative evidence (counts, line numbers, percentages)
    - Overall score must respect caps defined in style_guide.md for violations
    - Do NOT read or reference previous iterations. Each review must be independent.
    - Be thorough and constructive. Identify specific differences from human-written articles.
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

**Continue iterating if:**
- Overall quality score < 8.5/10
- Significant gaps remain between AI and human articles
- Reviews identify systematic issues
- Fewer than 10 iterations have been completed

**Stop iterating if:**
- Overall quality score ≥ 8.5/10 for 2+ consecutive iterations
- Reviews indicate articles are indistinguishable from human work
- No meaningful improvements in recent iterations
- 10+ iterations completed with diminishing returns

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
├── Review article → iterations/1/review.md (Quality: 6.5/10)
├── Update root style guide + Create changelog → iterations/1/changelog.md
└── Continue (quality below threshold)

Iteration 2:
├── Topic: "Reactのカスタムフックのパターン"
├── Archive style guide → iterations/2/style_guide.md
├── Write article → iterations/2/article.md
├── Review article → iterations/2/review.md (Quality: 7.2/10)
├── Update root style guide + Create changelog → iterations/2/changelog.md
└── Continue (quality improving but below threshold)

...

Iteration 8:
├── Topic: "JavaScriptのProxyとReflectの実践的な使い方"
├── Archive style guide → iterations/8/style_guide.md
├── Write article → iterations/8/article.md
├── Review article → iterations/8/review.md (Quality: 8.7/10)
├── Update root style guide + Create changelog → iterations/8/changelog.md
└── Continue one more iteration to confirm

Iteration 9:
├── Topic: "型安全なReactコンポーネントの設計パターン"
├── Archive style guide → iterations/9/style_guide.md
├── Write article → iterations/9/article.md
├── Review article → iterations/9/review.md (Quality: 8.8/10)
├── Create changelog → iterations/9/changelog.md
└── Success! Quality threshold met for 2 consecutive iterations
```

## Success Criteria

The iterative process has succeeded when generated articles:

1. Score ≥ 8.5/10 overall for multiple consecutive iterations
2. Receive reviewer feedback indicating human-like quality
3. Show mastery of:
   - Natural Japanese technical writing
   - Engaging, conversational tone
   - Accurate technical content
   - Appropriate structure and flow
   - Authentic voice without AI tells

## Getting Started

To begin the iterative process, simply start with Step 1. The orchestrator (you) will manage all coordination between agents and track progress toward human-quality article generation.

Good luck! The goal is ambitious but achievable through systematic iteration and refinement.
