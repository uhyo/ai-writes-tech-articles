# Changelog - Iteration 5

## Summary

This iteration addressed the "polished uniformity" problem: the article was technically correct and well-structured, but lacked the organic irregularity of human writing. The review identified six critical issues:

1. **Overly uniform politeness** - Every concept explained with equal care
2. **Isolated sections** - No organic flow between topics
3. **Missing footnotes** - Technical asides inline, disrupting flow
4. **Single predictable anecdote** - One story, everything else theory
5. **Weak assertions** - Excessive hedging with "〜だと思います"
6. **Shallow technical depth** - Describes "what" but not "why/how"

**Overall Quality Score: 7.0/10** (up from 6.2 in Iteration 4, but still showing AI tells)

## Style Guide Changes

### Lines Changed
- **Lines added:** 61
- **Lines removed:** 24
- **Net change:** +37 lines
- **New total:** 363 lines (target: <350, slightly over but justified by critical additions)

### Section-by-Section Updates

#### 1. Enhanced §5.1 Tone & Voice - Explanation Depth Variation

**What changed:**
Added "Vary Explanation Depth" subsection with concrete guidance on prioritizing ruthlessly.

**Why:**
The article explained every concept with uniform care, creating a textbook-like feel. Human writers explain key insights deeply but rush through obvious points.

**New guidelines:**
```
❌ Avoid uniform politeness:
- Explaining every concept with same care level
- Every section getting 3-4 similar-length paragraphs

✅ Vary explanation intensity:
- Key insights: Deep dive with rich context
- Obvious points: Single sentence, move on
- Some topics: Just state the fact, let readers infer why
- Example: "stateで管理すると無駄。" (why it's wasteful is implicit)
```

**Evidence from article:**
The article had uniform 2-3 paragraph sections throughout. Example:
> "この実装の何が良いかって、`intervalIdRef.current`を更新してもコンポーネントが再レンダリングされないこと。"

This meta-explanation is overly helpful. Better: Just state the fact and let readers infer.

---

#### 2. Expanded §5.2 Technical Depth - "Why/How" Focus

**What changed:**
Reorganized section to lead with "Go Deep on Why/How" before GitHub references.

**Why:**
The review noted: "技術的な深堀りが表面的。人間の記事では、マイクロタスクキューやレンダリング単位の記憶領域など、もっと深い実装詳細に踏み込む"

**New structure:**
1. First: Deep explanation patterns (mechanisms, constraints, edge cases)
2. Then: GitHub reference integration (now properly contextualized)

**New guidelines:**
```
❌ Shallow (just describes what):
- "useRefは再レンダリングを引き起こしません"

✅ Deep (explains why/how):
- "`use`も普通のフックと同様に「何番目の呼び出しか」に依存して記憶領域から
  データを読みだします。それにも関わらず`use`を条件分岐の中で使用できるのは、
  **レンダリングの最中に条件分岐の結果が変わることはないという仮定**を
  設けているからです。"
```

**Impact:**
This reframes technical writing from "what does it do" to "how does it work internally."

---

#### 3. Enhanced §5.3 Anecdotes - Multiple Distribution

**What changed:**
Added "CRITICAL: Multiple Anecdotes, Not Just One" guidance at the top of the section.

**Why:**
The article had exactly one anecdote (dashboard project) that felt like an "assigned story." Real articles scatter anecdotes organically throughout.

**New guidelines:**
```
❌ Single episode pattern:
- One elaborate anecdote in article
- Everything else is theory/explanation
- Episode feels like "assigned story"

✅ Scattered organic mentions:
- Brief experience mention in section 2
- Longer story with digressions in section 4
- Quick callback in section 6: "さっきの話と似てるけど"
- Mix depths: Some 1-sentence, others multi-paragraph
```

**Streamlined contextual elements:**
Removed redundant items (team size, who else involved) to keep under line limit while maintaining core guidance.

---

#### 4. New §5.4 Guidance - Organic Section Flow

**What changed:**
Added "CRITICAL: Create Organic Flow Between Sections" subsection.

**Why:**
Review feedback: "各セクションが独立しすぎていて、セクション間の有機的なつながりが弱い"

Each section in the article felt like an encyclopedia entry with no callbacks to previous content.

**New guidelines:**
```
❌ Independent sections (too isolated):
- Each section self-contained with no callbacks to previous content
- Every section feels like fresh start
- Reader can't tell why this topic comes after that topic

✅ Connected flow:
- Reference previous section: "さて、先ほどの〇〇の話に戻ると"
- Build on earlier points: "ここまでで〇〇を見たけど、実は〜"
- Use connectors: "ところで", "そういえば", "ちなみに"
```

**Human benchmark example included:**
"さて、すでに何か勉強した気になったかもしれませんが、これはReact 17と特に関係ない話なのでまだ復習です。" (from react17-useeffect.md)

---

#### 5. New §5.5 - Strong Assertions (Not Everything Hedged)

**What changed:**
Created entirely new section addressing excessive hedging.

**Why:**
Review noted: "主張が控えめで丸い" - every opinion qualified with "〜だと思います" or "気がします."

**New guidelines:**
```
❌ Over-qualified (weak):
- "個人的には〜だと思います" (repeats every section)
- "〜という気がします"
- Balanced view on everything

✅ Mix assertion strengths:
- Strong opinions on some topics: "これは間違いです", "〜は無駄"
- Direct statements without hedging: "useRefの本質は〜です。"
- Save qualifiers for genuinely uncertain claims
- Don't hedge facts: "useRefは再レンダリングを引き起こさない"
```

**Rationale:**
Humans vary conviction levels. They state facts directly and reserve hedging for genuinely uncertain claims. The current article hedged almost everything, creating weak, AI-like prose.

---

#### 6. New §8 - Footnotes & Side Content

**What changed:**
Created guidance on using footnotes for technical asides.

**Why:**
Review feedback: "人間の記事では脚注で用語の定義や議論の補足を行い、本文の流れを妨げない。AIは脚注を使わず、すべてを本文で説明しようとする"

**New guidelines:**
```
✅ Put in footnotes:
- Term definitions that would interrupt flow
- Tangential technical details
- Version-specific quirks
- Related but non-essential information

❌ Don't inline everything:
- "（ちなみにこの機能は〜で、〜という理由で、〜なので注意が必要です）"
  ← Too much in parentheses

✅ Use footnote instead:
- Main text: "この機能は便利です[^1]。"
- Footnote: "[^1]: ちなみにこの機能は〜で、〜という理由で〜"
```

**Impact:**
Footnotes allow depth without disrupting narrative flow—a key human writing pattern.

---

#### 7. Consolidated Anti-Patterns Section

**What changed:**
Compressed verbose anti-patterns into compact list of "AI Tells to Avoid."

**Lines saved:** 13 lines
**Why:**
The anti-patterns section had become repetitive with §5 sections. Consolidated into single scannable list.

**New format:**
Single bullet list covering all major AI tells:
- Pedagogical scaffolding
- Uniform politeness
- Isolated sections
- Single anecdote
- Weak assertions
- Shallow depth
- No footnotes
- Numbered patterns
- Neat conclusions
- Formal citations
- Excessive 筆者

---

#### 8. Removed Examples Section

**What changed:**
Removed "Examples from Human Articles" section (11 lines).

**Why:**
Examples are now integrated throughout §5 sections where they're more contextually relevant. The standalone examples section was redundant.

**Lines saved:** 11 lines

---

## Rule Effectiveness Tracking

### Rules That Worked (Followed Successfully)

**[✓ EFFECTIVE]** §1: です・ます polite form
- **Evidence:** Article consistently uses です・ます throughout
- **Action:** Keep as CRITICAL requirement

**[✓ EFFECTIVE]** §1.2: Frontmatter format
- **Evidence:** Valid YAML frontmatter with all required fields
- **Action:** Keep as CRITICAL requirement

**[✓ EFFECTIVE]** §5.1: Conversational tone with casual expressions
- **Evidence:** "これ、意外と知られてないんだけど", "実務だと結構あります"
- **Action:** Can remain at current priority

**[✓ EFFECTIVE]** §5.3: Rich contextual anecdote details
- **Evidence:** Dashboard story included tech stack, project scope (100個くらいエンドポイント), team dynamics
- **Action:** Guideline works, but needs distribution (now addressed)

---

### Rules That Were Violated (Need Promotion/Clarification)

**[✗ VIOLATED]** §5.1: No pedagogical scaffolding
- **Evidence:** "この実装の何が良いかって" - meta-explanation of what you're explaining
- **Impact:** Creates teacher-to-student dynamic instead of peer conversation
- **Action:** ✅ Added to prohibited phrases list

**[✗ VIOLATED]** §5.4: Vary section lengths dramatically
- **Evidence:** Nearly all sections were 2-3 paragraphs, uniform length
- **Impact:** Creates predictable, textbook-like rhythm
- **Action:** ✅ Enhanced guidance, increased range to 1-2 vs 5-8+ paragraphs

**[✗ VIOLATED]** §5.4: Natural transitions between sections
- **Evidence:** Each section stood alone, no callbacks to previous content
- **Impact:** Felt like encyclopedia entries, not flowing essay
- **Action:** ✅ Created new "Organic Flow Between Sections" subsection with examples

**[✗ VIOLATED]** Implicit: Strong assertions expected
- **Evidence:** "個人的には〜", "〜気がします", "〜だと思います" repeated throughout
- **Impact:** Weak, over-hedged prose (major AI tell)
- **Action:** ✅ Created new §5.5: Strong Assertions

**[✗ VIOLATED]** Implicit: Deep technical explanations
- **Evidence:** "useRefは再レンダリングを引き起こさない" (what, not why)
- **Impact:** Shallow explanations don't match human benchmark depth
- **Action:** ✅ Reorganized §5.2 to lead with depth-first approach

---

### Rules That Need Clarification (Partial Compliance)

**[~ UNCLEAR]** §5.3: Anecdote digressions
- **Evidence:** Dashboard story had ONE digression (Redux → hooks change)
- **Compliance:** Partial - had digression but felt "assigned"
- **Action:** ✅ Added guidance on multiple scattered anecdotes vs. single story

**[~ UNCLEAR]** §5.2: GitHub reference integration
- **Evidence:** Included GitHub URL but integration felt awkward: "実はReactのソースコード（URL）を見ると"
- **Compliance:** Partial - has URL but "citation-like"
- **Action:** ✅ Enhanced examples of casual integration patterns

---

### New Issues Not Covered by Current Guide

**[+ NEW ISSUE]** Uniform explanation depth
- **Description:** Every concept explained with same level of care/detail
- **Evidence:** All sections 2-3 paragraphs, uniform pedagogical tone
- **Proposed guideline:** Vary explanation intensity—deep dives vs. quick mentions
- **Action:** ✅ Added to §5.1: "Vary Explanation Depth"

**[+ NEW ISSUE]** Missing footnotes for technical asides
- **Description:** All technical details inline, disrupting narrative flow
- **Evidence:** "(2024年10月時点)" and RFC details inline instead of footnotes
- **Human benchmarks:** react-use-rfc.md, useeffect-taught-by-extremist.md use footnotes
- **Action:** ✅ Created new §8: Footnotes & Side Content

**[+ NEW ISSUE]** Over-hedging assertions
- **Description:** Qualifying nearly every statement with "〜だと思います"
- **Evidence:** Repeated pattern throughout article
- **Human pattern:** Mix strong assertions with hedged exploration
- **Action:** ✅ Created new §5.5: Strong Assertions

---

## Expected Impact

These changes address the core issue from Iteration 5: **polished uniformity masking AI origin**.

### Primary targets:
1. **Explanation depth variation** → Breaks uniform pedagogical tone
2. **Organic section flow** → Creates essay-like progression vs. encyclopedia entries
3. **Multiple distributed anecdotes** → Natural experience sharing vs. "assigned story"
4. **Strong assertions mixed with hedging** → Confident voice vs. over-cautious AI
5. **Deep technical explanations** → Why/how vs. what-only descriptions
6. **Footnotes for asides** → Smooth narrative flow vs. disrupted inline detail

### Quality score projection:
Current: 7.0/10 → Target: 7.8-8.2/10

The gap to 8.5+ requires:
- Actually implementing varied section lengths (not just knowing to)
- Creating genuine section callbacks and flow
- Distributing anecdotes naturally (not forcing it)
- Asserting strongly when appropriate (not defaulting to hedging)

---

## Meta-Observations

### What's Working
The style guide's CRITICAL/IMPORTANT/POLISH hierarchy continues to be effective. The Writer consistently follows CRITICAL requirements (です・ます, frontmatter, technical accuracy).

### What's Challenging
**Tone subtleties** remain difficult:
- Writer knows to be conversational but defaults to pedagogical
- Writer includes anecdotes but in predictable patterns
- Writer varies language but maintains uniform explanation depth

These are **systemic patterns**, not rule violations—the guide needs to directly address these defaults.

### Iteration Pattern
Quality progression: 5.5 → 6.2 → 7.0
Each iteration adds ~0.7 points by addressing one layer of AI tells:
- Iteration 3-4: Basic authenticity (anecdotes, voice)
- Iteration 5: Uniformity problems (depth, flow, assertions)
- Next: Implementation consistency (actually varying, not just knowing to vary)

---

## Consolidation Notes

**Total lines:** 363 (target <350, +13 over)
**Justification:** Six new critical guidelines addressing systemic issues outweigh compression benefits. Anti-patterns and examples sections were consolidated to minimize overage.

**Compression opportunities for Iteration 6:**
- If new rules prove effective, compress §5.3 anecdote details
- If depth guidelines work, simplify GitHub reference examples
- Consider merging §5.5 and §5.6 if assertion patterns stabilize

**Growth prevention:**
- No new examples added (integrate into existing guidance)
- Removed standalone examples section (11 lines saved)
- Compressed anti-patterns (13 lines saved)
- Net +37 lines all critical guidance, no fluff
