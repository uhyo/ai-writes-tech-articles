# Iteration 3 Changelog

## Summary

Iteration 3 achieved a score of **7.8/10** (up from 7.3/10 in Iteration 2), representing significant progress toward human-quality writing. The article successfully avoided major AI patterns like numbered enumeration (パターン1/2/3) and included strong opinions, specific GitHub references, and personal anecdotes with failures.

However, the review identified **6 critical remaining issues** that prevent the article from reaching 8.5+ quality:

1. **Pedagogical scaffolding** - Too many classroom-style transitions
2. **GitHub references feel inserted** - Cited formally rather than naturally woven in
3. **Anecdotes lack contextual richness** - Missing project details, requirements, team dynamics
4. **Excessive "筆者" usage** - Used 15+ times when humans use 3-5 times
5. **Too many sections** - 11 H2 sections is excessive for the length
6. **Conclusion too neat** - まとめ section synthesizes too cleanly

This changelog addresses these subtle but important patterns to push from 7.8 to 8.5+.

---

## Major Changes to Style Guide

### 1. Limit "筆者" Usage (Section: Writing Style Characteristics)

**What Changed:**
- Added explicit limit: **"筆者" usage to 3-5 times per article maximum**
- Added guidance to vary attribution style with "個人的には", "自分の経験では", or omission
- Provided concrete examples of overuse vs. natural usage

**Why:**
The Iteration 3 article used "筆者" 15+ times, making it feel performative and artificial. Human articles use "筆者" sparingly (3-5 times) and often state opinions directly without attribution.

**Review Feedback Addressed:**
> "Too many '筆者': While human articles use it occasionally, this article uses it 15+ times, making it feel performative"

**Examples Added:**
- ❌ Bad: "筆者は〜と思う。筆者の経験では〜。筆者が実際に書いた〜。筆者の結論は〜。" (overuse)
- ✅ Good: "個人的にはこの機能が最高だと思う。実際に書いた実装は〜。これは確信を持って言える。"

---

### 2. Eliminate Pedagogical Scaffolding (Section: Natural Flow)

**What Changed:**
- Added **CRITICAL** subsection on avoiding pedagogical scaffolding
- Listed specific phrases to avoid:
  - "では、〇〇の場合はどうでしょうか" (textbook-style transitions)
  - "本題に戻ると" (unnecessary announcements)
  - "ここで〇〇について見ていきましょう" (classroom guide language)
  - "次に〇〇を説明します" (explicit structure signaling)
- Provided natural alternatives for transitions

**Why:**
The article felt like a classroom lecture with explicit transitions guiding the reader through sections. Human writing transitions more abruptly without announcing topic changes.

**Review Feedback Addressed:**
> "Pedagogical scaffolding remains: Section transitions are too smooth and educational"
> "Example of AI tell: '本題に戻ると、なぜ`use`には後者のルールが適用されないのでしょうか。' Human writers don't typically announce 'back to the main topic' - they just continue."

**Examples Added:**
- ❌ "では、〇〇の場合はどうでしょうか" → ✅ "で、〇〇だと話が変わる。"
- ❌ "本題に戻ると" → ✅ "なぜ〇〇なのか。" (just resume without announcement)

---

### 3. Limit Section Count (Section: Main Content)

**What Changed:**
- Added **CRITICAL** guideline: Target **6-7 H2 sections maximum** for typical articles (200-300 lines)
- Emphasized combining related topics into single sections
- Noted that not every subtopic needs its own H2 section
- Suggested using H3 subsections for organization within larger sections

**Why:**
The article had 11 H2 sections, making it feel over-structured and tutorial-like. Human articles typically have 3-7 H2 sections with more organic flow.

**Review Feedback Addressed:**
> "Too much sectioning: 11 H2 sections is excessive and overly organized"
> "Comparison to human articles: Human article 'typescript-intrinsic.md' has only 4 H2 sections and flows more organically"

**Impact:**
This guideline will force the Writer to combine related content and create more varied section depths, resulting in more natural article structure.

---

### 4. Make Conclusions Messier (Section: まとめ)

**What Changed:**
- Emphasized **CRITICAL: Avoid synthesizing everything neatly**
- Added explicit patterns to avoid:
  - "重要なポイントをまとめると: [bullet list]" (too organized)
  - "今回は〜を見てきました。〜が重要です。" (textbook recap)
  - "筆者の結論: [perfectly balanced summary]" (too synthesized)
- Enhanced ending styles section with more concrete guidance
- Added example of rough conclusion

**Why:**
The article's conclusion was too neat and organized, synthesizing everything into a perfect summary. Human conclusions are messier, with loose ends and incomplete thoughts.

**Review Feedback Addressed:**
> "Conclusion too neat: まとめ section synthesizes too cleanly"
> "Organized まとめ: Final section synthesizes too neatly with clear takeaways"

**Example Added:**
✅ Good: "結局どう使えばいいのか。個人的には小規模なら最高。でも大規模だと厳しい。あと、この機能を使うなら型システムの理解が必須。"

---

### 5. Natural GitHub Reference Integration (Section: Technical Accuracy and Depth)

**What Changed:**
- Added new **CRITICAL** subsection: "Make GitHub references feel natural, not cited"
- Provided specific examples of bad (formal citation) vs. good (casual mention) styles
- Emphasized dropping PR/issue numbers naturally like references, not building a bibliography
- Warned against explaining PR titles or describing comment threads

**Why:**
While the article included specific GitHub references (good!), they felt like formal citations added to meet requirements rather than naturally flowing from the narrative.

**Review Feedback Addressed:**
> "GitHub references feel inserted: While specific, they read like citations added after the fact rather than naturally woven into storytelling"
> "Example showing the difference: Iteration 3 feels like citing sources ('このPRのタイトルは...'), while human style is more casual ('実は当初の実装では... ([#40336](...))'"

**Examples Added:**
- ❌ Bad: "このPRのタイトルは「〇〇」で、コメント欄を読むと分かるんだけど..."
- ❌ Bad: "この機能は GitHub issue #12754 で提案され、PR #40336 で実装されました"
- ✅ Good: "この機能、実はPR #40336で入ったんだけど、issue #12754を見ると数百のupvoteが集まってて..."
- ✅ Good: "実は当初の実装ではintrinsicを使っていませんでした。代わりに... ([#40336](...))"

---

### 6. Richer Anecdote Context (Section: Make It Human)

**What Changed:**
- Completely rewrote the personal anecdotes section to emphasize **contextual richness**
- Added explicit questions to guide context:
  - What kind of project? (company context, team size, tech stack)
  - Why were you doing this? (requirements, constraints, business needs)
  - Who else was involved? (team dynamics, stakeholders)
  - What were you trying to achieve? (specific goals)
  - What alternatives did you consider?
- Provided before/after examples showing insufficient vs. rich context

**Why:**
Anecdotes mentioned time spent and problems encountered but lacked the messy contextual details that make stories feel real (project specifics, team dynamics, why it mattered).

**Review Feedback Addressed:**
> "Anecdotes lack contextual detail: Stories mention problems but lack: What project was this? Who else was involved? What were the business constraints? Why did you try this approach?"
> "Example of insufficient context: '去年、あるプロジェクトで API のルーティング型を作ってて、3日間まるまる消費した経験がある。' This mentions time but doesn't explain why it took 3 days, what the blockers were, or what finally worked."

**Examples Added:**
- ❌ Lacks context: "去年、あるプロジェクトで3日間消費した経験がある"
- ✅ Rich context: "去年、社内の古いExpress APIをTypeScript化するプロジェクトで、「既存の100個のエンドポイント全部に型つけろ」って無茶振りされた。最初は『stringでいいんじゃない？』って思ってたけど、実際にやり始めたらパスパラメータの抽出で詰まって、気づいたら3日溶けてた"

---

## Updates to "Avoid AI Patterns" Section

### Language Patterns

**Added:**
- **CRITICAL: Pedagogical scaffolding** subsection with specific phrases:
  - "では、〇〇の場合はどうでしょうか" (classroom-style transitions)
  - "本題に戻ると" (unnecessary announcements)
  - "ここで〇〇について見ていきましょう" (explicit guiding)
  - "この型、実は結構問題がある" (unnecessary foreshadowing)
- **Excessive "筆者" usage**: More than 5-7 times per article feels performative

**Why:**
These patterns were present in Iteration 3 and are strong AI tells that prevent articles from feeling natural.

---

### Structural Patterns

**Added:**
- **CRITICAL: Too many sections (10+ H2 sections is excessive)**
- Too-organized まとめ sections with bullet points and comprehensive synthesis

**Why:**
The 11 H2 sections made the article feel over-structured. Added to reinforce the importance of section count limits.

---

### Personal Anecdote Issues

**Enhanced:**
- Changed "Lack of specifics" to "**Lack of contextual richness**: Mentions time spent but not why, what project, what requirements, who was involved"
- Added "Lack of specifics: No project details, tech stack, team size, business constraints"
- Added concrete examples of insufficient vs. rich context

**Why:**
This makes the problem more specific and actionable for the Writer Agent.

---

## Updates to Quality Checklist

### Format and Structure
**Added:**
- [ ] **Target 6-7 H2 sections maximum** (not 10+)

### Content Quality
**Added:**
- [ ] **GitHub references feel natural**, not cited (casual mentions, parenthetical links)

### Writing Style
**Enhanced:**
- [ ] **"筆者" used sparingly (3-5 times maximum)**, varies with "個人的には", omission
- [ ] **NO pedagogical scaffolding** ("では〇〇の場合は", "本題に戻ると")
- [ ] **Messy personal anecdotes** with rich contextual details (project specifics, requirements, team dynamics)

### Authenticity
**Added:**
- [ ] **Conclusion is messy**, not a neat synthesis with bullet points

### Details
**Enhanced:**
- [ ] Conclusion adds insight with rough edges, not just neat recap
- [ ] Personal stories have messy, imperfect details with rich context (not just time spent)

---

## Expected Impact

These changes target the **subtle but critical differences** between 7.8/10 and 8.5+ quality articles:

1. **Reducing pedagogical scaffolding** will make transitions feel more natural and less classroom-like
2. **Limiting "筆者" usage** will reduce performative feeling and make opinions flow more naturally
3. **Limiting section count** will force more organic structure and varied depth
4. **Messier conclusions** will avoid the "too neat" synthesis that marks AI writing
5. **Natural GitHub references** will make technical depth feel authentic rather than researched
6. **Richer anecdote context** will make personal stories feel embedded in real work rather than generic examples

The review noted: **"The foundation is excellent. Remaining issues are about subtlety and naturalness rather than fundamental problems."** These updates address exactly those subtle issues.

---

## Review Score Progression

- **Iteration 1**: 6.5/10 - Textbook-like, no personality
- **Iteration 2**: 7.3/10 - Better tone, but パターン1/2/3 structure
- **Iteration 3**: 7.8/10 - Strong personality and specifics, but pedagogical scaffolding remains
- **Target**: 8.5/10 - Indistinguishable from human writing

With these style guide updates, the next iteration should address the remaining subtle AI patterns and push toward human-indistinguishable quality.

---

## Key Takeaways

The difference between 7.8 and 8.5+ is **not about fundamental changes** but about:
- **Subtlety in transitions**: No explicit signposting
- **Natural references**: Casual mentions, not formal citations
- **Rich context**: Stories embedded in real scenarios with messy details
- **Restraint**: Less "筆者", fewer sections, messier conclusions
- **Imperfection**: Rough edges, loose ends, incomplete thoughts

The Writer Agent now has concrete, actionable guidance to avoid these subtle AI tells in the next iteration.
