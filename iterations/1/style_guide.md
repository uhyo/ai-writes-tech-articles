# Technical Article Style Guide

This guide defines standards for generating Japanese technical articles indistinguishable from human-written content.

---

## 🔴 CRITICAL REQUIREMENTS (Publication Blockers)

These are non-negotiable. Violating any of these makes the article unpublishable.

### 1. Japanese Language & Tone - EXPLICIT RULES

**CRITICAL: Polite Form Consistency (MEASURABLE)**

Target: **≥95%** of sentence endings in main text use polite form (-ます/-です/-でしょう/-ません)

Casual forms (-だ/-る/-た/-てる) allowed ONLY in:
- ✅ Direct quotes: 「これでいけるじゃん」と気づいて
- ✅ Mid-sentence interjections: ...あ、これ、動かないんだった。
- ✅ Footnotes (can be more casual)
- ✅ Subordinate clauses within complex sentences: "考えてたんですが"

❌ **Wrong**: "結論から言うと、これProxyとWeakMapだけで実装できる。" (だ form in main text)
✅ **Correct**: "結論から言うと、これはProxyとWeakMapだけで実装できます。" (です form)

**FORBIDDEN PATTERNS (0 TOLERANCE in main text)**

These patterns have **0% frequency** in human benchmark articles:

❌ **Sentence-ending contracted verb forms**:
- "構成されてる。" → ✅ "構成されています。"
- "考えてた。" → ✅ "考えていました。"
- "使ってます。" → ✅ "使っています。"
- "書いてた。" → ✅ "書いていました。"
- Pattern: Any -てる/-てた/-てます as final verb in sentence ending with 。or 、

❌ **Paragraph-initial "で、" as sentence starter**:
- "で、これを直すには..." → ✅ "これを直すには..." or "そこで、..."
- Note: "で、" mid-paragraph after context may be acceptable

**Acceptable Casual Usage**:
- ✅ Within sentences: "Proxyで包んで、それを返す"
- ✅ In subordinate clauses: "書いてある通り", "使ってる場合は"
- ✅ In quotes/interjections: 「あ、Proxyでいけるじゃん」
- ✅ Connecting clauses: "考えてたんですが、" (within larger polite sentence)
- ✅ Embedded clauses: "使ってるライブラリは" (before main verb)

**Scoring Impact** (Reviewer must apply):
- If polite form ratio <90%: **Maximum overall score = 7.0/10**
- If forbidden patterns appear 3+ times: **Maximum overall score = 7.5/10**
- If forbidden patterns appear 1-2 times: **Maximum overall score = 8.5/10**
- For 9.0+ overall score: **Zero forbidden patterns required**

### 2. Frontmatter Format

Every article must begin with valid YAML:

```yaml
---
title: "記事のタイトル"
emoji: "🎯"
type: "tech"
topics: ["typescript", "javascript", "react"]
published: true
---
```

### 3. Technical Accuracy

- Concepts explained correctly with sources
- Code examples work and are properly explained
- Reference specific GitHub PRs/issues with numbers and links
- Include version information (e.g., "TypeScript 4.8以降")

### 4. Natural Japanese

- Written entirely in Japanese (not translation-like)
- Technical terms: Use English when commonly used in Japanese tech community
- Natural phrasing that flows smoothly

---

## 📋 QUALITY CHECKLIST

Use this to verify articles before submission. Each item links to detailed guidance below.

### Format & Structure
- [ ] **Polite form consistency ≥95%** in main text (→ §1)
- [ ] **Zero forbidden patterns**: No sentence-ending -てる/-てた/-てます (→ §1)
- [ ] **Zero paragraph-initial "で、"** patterns (→ §1)
- [ ] Valid frontmatter with all fields (→ §2)
- [ ] 6-7 H2 sections maximum (→ §5.4)
- [ ] Dramatic variation in section lengths (→ §5.4)
- [ ] Clear introduction and まとめ

### Technical Content
- [ ] Technical accuracy verified (→ §1.3)
- [ ] Code examples with proper syntax highlighting
- [ ] Specific GitHub PR/issue references with links (→ §5.2)
- [ ] Version information included
- [ ] Explains "why" not just "what"

### Writing Style
- [ ] Conversational, not textbook-like (→ §5)
- [ ] Personal voice present but "筆者" used sparingly (3-5x max) (→ §5.1)
- [ ] NO pedagogical scaffolding (→ §5.1)
- [ ] NO numbered enumeration patterns (パターン1/2/3) (→ §5.4)
- [ ] Strong opinions expressed, not just balanced views (→ §6)

### Authenticity Markers
- [ ] Personal anecdotes with rich contextual details (→ §5.3)
- [ ] Anecdotes include tangential digressions (→ §5.3)
- [ ] GitHub references feel casual, not cited (→ §5.2)
- [ ] Messy conclusion without numbered synthesis (→ §5.5)
- [ ] 1-2 micro-imperfections present (→ §7)
- [ ] Not too polished - has rough edges

---

## 🟡 IMPORTANT: Human-Like Writing Patterns

These distinguish human writing from AI-generated content.

### 5.1 Write from THINKING, Not from FORMULA

**CRITICAL: The Core Problem**

DON'T follow writing guidelines mechanically. Guidelines describe OUTCOMES of human thinking, not INPUTS to article generation.

❌ **Formula-following (mechanical):**
- "I need a trial-and-error section, so I'll create V1→V2→V3"
- "I need reader dialogue, so I'll insert 'お察しの通り'"
- "I need an anecdote, so I'll write about a project"

✅ **Thinking-driven (organic):**
- Think about the concept deeply → Realize it evolved through problems → Show that evolution naturally
- Notice readers might misunderstand → Address their confusion directly in that moment
- Remember a real discovery → Let it inform how you explain the problem

**Techniques are SYMPTOMS of thinking, not TOOLS to apply.**

**CRITICAL: Imperfections Must Be TRULY RANDOM**

Human imperfections cluster randomly, NOT evenly distributed. Avoid strategic placement:

❌ **Strategic (AI tell):**
- One imperfection per section, evenly spaced
- Imperfection → immediate resolution pattern
- All problems get solutions in same article
- "実務では使ったことない" appears at predictable moments

✅ **Truly random:**
- Some sections perfect, others have 3-4 messy moments
- Clustering: Two realizations back-to-back, then silence for pages
- Incomplete threads: Start tangent, never finish OR finish much later
- Mixed depths: Explain complex thing briefly, simple thing exhaustively (based on personal interest)

**Example authentic mess patterns:**
- Code first → "あ、バグある" → fix → "いや待って、これも違う" → actual fix
- Random tangent mid-explanation: "そういえば..." (never returns to main point)
- Admission without follow-up: "この辺は理解してない" (no resolution offered)

### 5.2 Tone & Voice (Conversational Flow)

**Personal Voice Guidelines:**
- Use "筆者" sparingly: **3-5 times maximum per article**
- Vary attribution style naturally
- Express subjective judgments freely

**CRITICAL: No Pedagogical Scaffolding**

Write as peer conversation, not teacher-to-student lecture. Avoid: "では〜", "見ていきましょう". Use: "で、〜", abrupt topic starts.

**CRITICAL: Vary Explanation Depth by INTEREST, Not Pedagogy**

Humans explain what personally fascinates them, NOT what's pedagogically important:
- Simple concept you find interesting: 8 paragraphs with code examples
- Critical complex concept you find boring: 2 sentences, "この辺は省略"
- Tangent that's technically irrelevant: 5 paragraphs because it's fun

This creates uneven, unpredictable depth that no curriculum would produce.

**CRITICAL: Vary Explanatory Phrases**

Track phrase patterns. Don't repeat structures:
- "これで〜解決" → vary with "ここまでできた", "やっと〜", "ようやく〜", or just move on without explicit markers
- "〜です。〜ます。" → occasionally break with sentence fragments, casual endings: "で、結局〜。"

### 5.3 Introduce Conceptual Frameworks

**CRITICAL: Think at Meta-Technical Level**

Don't just explain HOW things work. Introduce higher-level concepts that REFRAME the discussion.

✅ **Examples from human articles:**
- "Promiseが一級市民ではなかった" (react-use-rfc.md) - Reframes entire Suspense discussion
- "記憶領域を必要としないフック" - Creates new mental category
- "レンダリング単位の記憶領域" vs "コンポーネント単位の記憶領域" - Distinguishes implementation approaches

**How to create frameworks:**
1. Notice a recurring pattern or constraint
2. Give it a name that's not in official docs
3. Use it to explain why things work the way they do
4. Reference it later to create conceptual continuity

These frameworks show you're thinking deeply, not just reporting facts.

### 5.4 Technical Depth & Ecosystem Context

**CRITICAL: Go Deep on "Why" and "How", Not Just "What"**

Surface-level explanations are AI tells. Explain mechanisms, constraints, edge cases.

**CRITICAL: Show Code Evolution, Not Just Final Versions**

Human developers iterate. Show that process:

✅ **Show bugs/issues, then fix:**
```typescript
// First attempt
const result = data.map(x => x.value);
// あ、これundefinedの場合に落ちる
const result = data.map(x => x?.value ?? 0);
```

❌ **Always showing perfect code on first try** is an AI tell.

**GitHub References: Natural Integration**

Make references feel casual, not cited:
- "(#2851とか)" buried in sentence
- "実装見てると色々工夫されてて面白い"
- Include PR authors when relevant: "Andersさんのコメント見ると〜"

**CRITICAL: Add Ecosystem Awareness**

Reference broader context naturally:
- "Twitterで〜という話を見た"
- "zodみたいなライブラリもあるけど"
- Community debates: "この辺は意見が分かれてて"
- Future speculation: "TypeScript 5.5で〜が入るかも"

### 5.3 Authentic Anecdotes

**CRITICAL: Not All Stories Need Happy Endings**

Humans share experiences regardless of outcome. Many anecdotes just... happen:

❌ **Always resolved:**
- "実装時間が3日短縮された"
- "パフォーマンスが2倍になった"
- Every story proves a point

✅ **Mixed outcomes:**
- "やったことがある" (no stated result)
- "途中で別の方法に変えた" (original approach abandoned)
- "たぶん〜できると思うけど、まだ試してない" (speculation only)
- "記憶が正しければ〜" (uncertain recall)

**Personal experiences: Rich details OR vague recollections, not medium:**

❌ **Medium detail (feels constructed):** "去年あるプロジェクトで3日消費"

✅ **Rich:** "去年、社内の古いExpress API（100個くらいエンドポイント）をTypeScript化する無茶振りで、パスパラメータの抽出で詰まって3日溶けた"

✅ **Vague:** "前に似たようなことやった気がする" "たぶん2019年くらい？"

Human memory isn't uniform—some details vivid, others fuzzy.

### 5.5 Structure: Non-Linear Exploration

**CRITICAL: Don't Follow Clean Narrative Arc**

Articles feel mechanical when everything ties together perfectly.

❌ **Too clean:**
- Linear progression, smooth transitions everywhere
- Each section complete before moving on
- All questions answered, all loose ends tied

✅ **Messy exploration:**
- Jump topics abruptly: "そういえば〜" (no smooth setup)
- Plant seeds early, pay off later OR leave unpaid
- Circle back: "さて、先ほどの話"
- Digress: "余談だが〜" (sometimes never returns to main point)
- **Leave questions open:** "これは別の機会に", "まだ試してないけど"
- Realize mid-article: "ああ、そういえば〜も説明すべきでした"

**CRITICAL: Include Unresolved Elements**

Perfect resolution is an AI tell. Leave some things:
- Partially answered questions
- "これはうまくいくかもしれないけど、確認してない"
- Tangents that don't tie back to main narrative
- Future work: "いつか〜を試したい"

**Section length:** Dramatically variable (1-2 paragraphs vs 15+ paragraphs). NO uniform depths.

**Limit sections:** 6-7 H2 maximum. NO numbered patterns.

### 5.6 Vary Assertion Strength Dramatically

**CRITICAL: Conviction Level Should Reflect Actual Certainty**

Uniform confidence (all hedged OR all definitive) is an AI tell.

✅ **Full spectrum of certainty:**
- **Definitive** (proven facts): "useRefは再レンダリングを引き起こさない"
- **Strong opinion**: "これは間違いです", "〜すべきです"
- **Reasoned inference**: "〜と考えられます" (with evidence)
- **Speculation**: "〜かもしれない", "〜だろうか" (honest uncertainty)
- **Tentative exploration**: "〜という気がする" (thinking aloud)
- **Admitted ignorance**: "実装を確認していないので推測ですが"

❌ **Uniform hedging:**
- "個人的には〜だと思います" repeated everywhere
- Every claim qualified with "〜気がします"

The VARIETY shows human thinking - confident where you know, tentative where you don't.

### 5.7 Conclusions (まとめ)

**CRITICAL: Avoid synthesizing everything neatly**

Real conclusions are messy and incomplete.

❌ **Avoid:**
- Numbered synthesis: "結局、核心は3つ：1. 〜"
- Neat recap: "今回は〜を見てきました"
- Tying all threads together perfectly

✅ **Better endings:**
- End abruptly with final thought
- Raise remaining questions: "〜はまだ分からない"
- Strong opinion without balance
- Admit limitations: "試してないけど〜かもしれない"
- Mix insights with loose ends
- Forward-looking speculation: "将来的には〜になるかも"
- Tangential ending that opens new questions

Real humans often end with uncertainty or tangents, not neat packages.

---

## 🟢 POLISH: Final Refinements

These are final touches that push quality from good to excellent.

### 7. Micro-Imperfections for Authenticity

**CRITICAL: Random Distribution, Not Even Spacing**

Humans don't distribute imperfections strategically. Some paragraphs have 3, others zero.

✅ **Natural clustering:**
- Casual contractions: "んだけど", "んで" appear in bursts when author is in flow
- Self-corrections cluster: "〜というか、正確には〜あ、待って、〜"
- Trailing thoughts sometimes abandoned mid-sentence...
- Repetition: Same phrase 2-3 times when fixated on concept

❌ **Strategic placement (AI tell):**
- Exactly one imperfection per section
- Imperfections only appear in "personal" sections
- Every code example has a bug → fix pattern

**Example natural cluster:** "で、これ試したんだけど、んで、思ったより簡単で...というか簡単すぎて拍子抜けした。"

### 8. Footnotes & Side Content

**CRITICAL: Use Footnotes for Technical Asides**

Footnotes keep main text flowing while providing depth for interested readers.

✅ **Put in footnotes:**
- Term definitions that would interrupt flow
- Tangential technical details
- Version-specific quirks
- Related but non-essential information

❌ **Don't inline everything:**
- "（ちなみにこの機能は〜で、〜という理由で、〜なので注意が必要です）" ← Too much in parentheses

✅ **Use footnote instead:**
- Main text: "この機能は便利です[^1]。"
- Footnote: "[^1]: ちなみにこの機能は〜で、〜という理由で〜"

**余談 Blocks:** Use `:::details 余談` for historical context, implementation deep dives, future proposals

---

## ⚠️ ANTI-PATTERNS: Quick Reference

**AI Tells to Avoid:**
- **STRATEGIC IMPERFECTIONS**: Evenly distributed across sections (instead: random clustering)
- **FORMULA-FOLLOWING**: Applying techniques mechanically instead of thinking
- Pedagogical depth: Explaining important things thoroughly, trivial things briefly
- Uniform outcomes: All anecdotes have clear results ("実装時間が短縮された")
- Perfect technical depth: Never admitting incomplete understanding
- Strategic disclaimers: "実務では使ったことない" at predictable moments
- Clean structure: All threads resolved, no dangling tangents
- Uniform assertions: All hedged OR all definitive
- Pedagogical scaffolding: "それでは〜を見ていきましょう"

---

**Last updated:** Iteration 9
**Version:** 3.2 (RANDOMNESS: Authentic imperfection distribution)
**Target:** <350 lines | **Current:** 365 lines
