# Changelog - Iteration 2

## Summary

Iteration 2 scored **7.3/10** (up from 6.5/10 in Iteration 1), showing significant improvement in conversational tone and personal voice. However, the review identified critical systematic issues that prevent the article from achieving human-like quality:

1. **Overly systematic structure**: The "パターン1/2/3/4/5" enumeration makes the article feel like a tutorial rather than a blog post
2. **Too balanced and pedagogical**: Lacks strong opinions and reads like a patient teacher explaining concepts
3. **Shallow technical depth**: Missing specific GitHub PR/issue references, commit hashes, and evidence of investigation
4. **Generic personal anecdotes**: Stories lack messy, specific details that make human writing authentic
5. **Too polished**: Transitions are too smooth, sections too uniform, no rough edges

This update addresses these issues with concrete, actionable guidelines.

---

## Major Changes to Style Guide

### 1. **CRITICAL: Ban Numbered Enumeration Patterns**

**Location**: Article Structure > Main Content

**What Changed**:
Added explicit prohibition against systematic enumeration patterns:
- ❌ "パターン1/2/3/4/5"
- ❌ "方法1/2/3"
- ❌ "ケース1/2/3"

**Why**: The Iteration 2 article used "パターン1" through "パターン5" structure, which made it feel like a systematic tutorial rather than an organic blog post. This is one of the most obvious AI patterns.

**Impact**: Forces the Writer to use descriptive, varied section headings and break away from checklist-style organization.

**Example Guidelines Added**:
```
Instead of: "パターン1: useMemoで値を安定化"
Use: "useMemoで値を安定化させる"

Even better: Mix different organizational styles within the same article
```

---

### 2. **Mandate Dramatic Variation in Section Lengths**

**Location**: Article Structure > Main Content

**What Changed**:
Added requirement for dramatic variation in section depth:
- Some sections should be 1-2 paragraphs (brief points)
- Others should be 3-5+ paragraphs (extensive deep-dives)
- Never make all sections roughly the same length
- Follow genuine interest, not templates

**Why**: Iteration 2 had very uniform section lengths, making it feel template-driven. Human articles have messy variations based on the author's interest and what's genuinely important.

**Impact**: Articles will feel more organic, with natural emphasis on interesting topics and brief treatment of obvious points.

---

### 3. **Require Imperfect, Abrupt Transitions**

**Location**: Article Structure > Main Content

**What Changed**:
Added guidelines for natural, imperfect transitions:
- Not every section needs smooth transitions
- Use abrupt topic changes: "ところで", "そういえば", "余談だけど"
- Sometimes just start a new section without setup
- Let structure feel slightly messy and organic

**Why**: Iteration 2 had too-smooth transitions ("では、次のパターンを見てみましょう"), making it feel pedagogical. Human writing has rough edges.

**Impact**: Articles will feel less like lectures and more like conversations.

---

### 4. **CRITICAL: Demand Extreme Specificity in Technical References**

**Location**: Technical Content Standards > Technical Accuracy and Depth

**What Changed**:
Massively expanded the requirements for technical references:
- ✅ ALWAYS link to specific GitHub PRs with numbers
- ✅ Include PR/issue authors by name
- ✅ Mention commit hashes when relevant
- ✅ Cite specific line numbers in source code
- ✅ Reference precise version numbers
- ❌ Don't just say "GitHub issueで提案されていました" without links
- ❌ Don't mention PRs generically without showing you've read them

**Why**: Iteration 2 mentioned "GitHub issueの #19820" but only in passing, without demonstrating deep engagement. The review noted this as a critical weakness - shallow references don't demonstrate real expertise.

**Impact**: Forces the Writer to include concrete evidence of investigation, making articles feel authoritative and well-researched.

**New Section Added**: "Show evidence of investigation"
- Mention specific experiments you ran
- Describe debugging experiences
- Reference discussions you participated in
- Cite specific error messages or edge cases

---

### 5. **Require Messy, Specific Personal Anecdotes**

**Location**: Engagement and Authenticity > Make It Human

**What Changed**:
Completely rewrote the personal experience guidelines with concrete examples:

**Bad (Generic)**:
- "実務でよく遭遇する問題です"
- "筆者は以前、この機能を使ったことがあります"

**Good (Specific and Messy)**:
- "先週、〇〇プロジェクトで妙なバグに遭遇して、3時間悩んだ結果〜"
- "筆者は去年、大規模なリファクタリング中にこのパターンを試して、結局諦めた経験があります"

**New Requirements**:
- Real stories have odd details: specific project names, time spent, failed attempts
- Real stories have imperfect resolutions: "結局完全には解決しなかった"
- Real stories include dead ends: "最初は〜だと思ったけど、違った"

**Why**: Iteration 2's anecdotes felt fabricated ("先日、パフォーマンスの問題を抱えるReactアプリケーションのレビューをしていて") - too clean, too generic. Human stories are messier.

**Impact**: Personal anecdotes will feel authentic and relatable, not manufactured.

---

### 6. **Mandate Strong Opinions, Not Balanced Views**

**Location**: Engagement and Authenticity > Make It Human

**What Changed**:
Added explicit section on expressing strong opinions:

**Bad (Too Neutral)**:
- "この方法にはメリットとデメリットがあります"
- "どちらを選ぶかは状況次第です"

**Good (Opinionated)**:
- "個人的には、この方法は避けるべきだと断言します。なぜなら〜"
- "よく推奨されているけど、筆者は〜の方が好みです"
- "このパターンは絶対に使うな。〜という理由で"

**New Principle**: "Balance comes from having opinions, not from being neutral about everything"

**Why**: Iteration 2 was consistently balanced and reasonable, lacking the sharp opinions found in human articles. The review noted this makes it feel pedagogical rather than personal.

**Impact**: Articles will have personality and take stances, making them more engaging and memorable.

---

### 7. **Avoid Meta-Commentary About Structure**

**Location**: Avoid AI Patterns > Language patterns

**What Changed**:
Added prohibition against meta-commentary:
- ❌ "今回は〜について解説します"
- ❌ "では、次のパターンを見てみましょう"
- ❌ Too-smooth transitions connecting every section

**Why**: Iteration 2 had phrases like "今回は、useEffectの依存配列に関する最適化パターンを、実際によく見かける問題と共に掘り下げていきます" - this telegraphs the structure in an artificial way.

**Impact**: Articles will jump directly into content rather than announcing what they'll cover.

---

### 8. **Emphasize Messy, Imperfect Conclusions**

**Location**: Article Structure > まとめ (Summary)

**What Changed**:
Expanded conclusion guidelines with anti-patterns:
- ❌ Avoid: "今回は〜を見てきました。〜が重要です。" (too neat)
- ✅ End abruptly with a final observation
- ✅ Raise a question or uncertainty
- ✅ Take a controversial stance
- ✅ Admit what you don't know or what remains unsolved

**Why**: Iteration 2's conclusion neatly summarized everything ("結局、どうすればいいのか" followed by a numbered list). Human conclusions are messier and less comprehensive.

**Impact**: Conclusions will feel more authentic and less like textbook summaries.

---

### 9. **New Section: "Breaking the Tutorial Template Trap"**

**Location**: New major section before Quality Checklist

**What Changed**:
Added comprehensive section with concrete examples contrasting AI patterns vs. human patterns:

**The Problem**: Shows the exact enumeration pattern from Iteration 2
**The Solution**: Explains organic, varied structure with examples
**The Balance Problem**: Shows too-neutral writing vs. opinionated writing
**The Depth Problem**: Contrasts superficial references vs. specific investigation

**Why**: The review identified these as the core systematic issues. A dedicated section with side-by-side examples makes these patterns crystal clear.

**Impact**: Provides actionable templates the Writer can follow to avoid tutorial-style writing.

---

### 10. **Updated Critical Pitfalls Section**

**Location**: Critical Pitfalls to Avoid

**What Changed**:
Added new categories and specific issues:

**Textbook Syndrome**:
- Added: ❌ Systematic patterns like "パターン1/2/3/4/5"

**Artificial Tone**:
- Added: ❌ Too balanced (always presenting both sides)
- Added: ❌ Too pedagogical (patient teacher tone)

**Shallow Content**:
- Added: ❌ CRITICAL: Missing specific GitHub PR/issue/commit references
- Added: ❌ No evidence of investigation or experimentation

**New Category: Personal Anecdote Issues**:
- ❌ Generic stories
- ❌ Perfect resolutions
- ❌ Lack of specifics
- ❌ Too clean

**Structural Issues**:
- Added: ❌ All sections the same length
- Added: ❌ Too-smooth transitions

**Why**: These are the specific patterns found in Iteration 2 that revealed its AI origin.

**Impact**: Comprehensive checklist of what to avoid.

---

### 11. **Enhanced Quality Checklist**

**Location**: Quality Checklist

**What Changed**:
Added specific requirements based on Iteration 2 issues:

**Format and Structure**:
- Added: ❌ NO numbered enumeration patterns
- Added: ✅ Dramatic variation in section lengths

**Content Quality**:
- Added: ✅ Specific references: PR/issue numbers with links
- Added: ✅ Shows evidence of investigation or experimentation

**Writing Style**:
- Added: ✅ Takes strong stances, not just balanced views
- Added: ✅ Messy personal anecdotes with specific details

**Authenticity**:
- Added: ✅ Not too polished: has rough edges, tangents, imperfections
- Added: ✅ Not pedagogical: doesn't sound like a patient teacher

**Details**:
- Added: ✅ Personal stories have messy, imperfect details

**Why**: Makes the checklist directly address the issues found in Iteration 2.

**Impact**: Provides concrete verification points before finalizing articles.

---

## Expected Impact

These changes target the core systematic issues that made Iteration 2 identifiable as AI-generated:

1. **Structural variety**: Eliminating numbered patterns and requiring varied section lengths will break the tutorial template
2. **Authentic depth**: Requiring specific technical references will demonstrate real expertise
3. **Personality**: Mandating strong opinions and messy anecdotes will add human authenticity
4. **Imperfection**: Allowing rough edges, abrupt transitions, and incomplete resolutions will make articles feel organic

**Target for Iteration 3**: 8.0+ / 10

The article should feel more like a blog post written by an opinionated expert sharing their discoveries, and less like a well-organized tutorial by a patient teacher.

---

## Review Scores - Iteration 2

- Technical Accuracy: 8.5/10 (strong)
- Writing Style: 7.0/10 (improved but still systematic)
- Structure: 7.0/10 (too perfect and template-like)
- Authenticity: 7.0/10 (more authentic but still identifiable as AI)
- **Overall: 7.3/10**

Key quote from review:
> "The article is now clearly trying to emulate human writing patterns and succeeds in several areas. However, it's still too 'perfect' - too balanced, too systematic, too pedagogical. Human technical writing is messier, more opinionated, and less predictable."
