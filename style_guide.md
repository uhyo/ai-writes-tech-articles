# Technical Article Style Guide

This guide defines standards for generating Japanese technical articles indistinguishable from human-written content.

---

## 🔴 CRITICAL REQUIREMENTS (Publication Blockers)

These are non-negotiable. Violating any of these makes the article unpublishable.

### 1. Japanese Language & Tone - EXPLICIT RULES

**CRITICAL: Natural Formality Mix (Based on Human Baseline)**

Target: **Natural mix of polite and casual forms** (~40-60% polite in main text)

Human articles use varied formality based on context:
- Polite forms (-ます/-です) for main explanations and conclusions
- Casual forms (-だ/-る/-た) for lists, direct facts, embedded statements
- Natural flow; don't force artificial uniformity

**Context-based usage:**
- ✅ Polite for: Main explanations, section conclusions, reader-facing statements
- ✅ Casual for: List items, subordinate clauses, embedded facts, code comments
- ✅ Mixed within same paragraph is natural and authentic

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

❌ **Full-width colon (：) to introduce code blocks**:
- "こんな感じのコードを書いてたんです：" → ✅ "こんな感じのコードを書いてたんです。"
- Colons in human articles appear ONLY in: section headers, parenthetical notes (訳注：), blockquotes
- **NEVER use colons to introduce code blocks in prose**
- Pattern: 0% frequency in human articles for code block introduction

**Acceptable Casual Usage**:
- ✅ Within sentences: "Proxyで包んで、それを返す"
- ✅ In subordinate clauses: "書いてある通り", "使ってる場合は"
- ✅ In quotes/interjections: 「あ、Proxyでいけるじゃん」
- ✅ Connecting clauses: "考えてたんですが、" (within larger polite sentence)
- ✅ Embedded clauses: "使ってるライブラリは" (before main verb)

**Scoring Impact** (Reviewer must apply):
- If forbidden patterns appear 3+ times total: **Maximum overall score = 7.0/10**
- If forbidden patterns appear 1-2 times total: **Maximum overall score = 8.0/10**
- For 9.0+ overall score: **Zero forbidden patterns required**
- Forbidden patterns include: sentence-ending -てる/-てた/-てます, paragraph-initial "で、", colon before code blocks
- Polite/casual ratio should match human baseline (~40-60%); unnatural uniformity (>90% or <20%) is an AI tell

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
- [ ] **Natural polite/casual mix** (~40-60% polite, context-dependent) (→ §1)
- [ ] **Zero forbidden patterns**: No sentence-ending -てる/-てた/-てます (→ §1)
- [ ] **Zero paragraph-initial "で、"** patterns (→ §1)
- [ ] **Zero colons before code blocks** (：to introduce code) (→ §1)
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

### Authenticity Markers (MANDATORY)
- [ ] Code evolution shown: bug → fix OR V1 → V2 iterations (→ §5.4)
- [ ] At least 2-3 unresolved elements: speculation, abandoned tangents, future work (→ §5.5)
- [ ] Ecosystem context: GitHub refs OR community mentions OR temporal context (→ §5.4)
- [ ] Personal anecdotes with rich OR vague details (not medium) (→ §5.3)
- [ ] Dramatically uneven depth by interest, not pedagogy (→ §5.2)
- [ ] Messy conclusion without numbered synthesis (→ §5.7)
- [ ] Natural micro-imperfections in random clusters (→ §7)

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

**CRITICAL: Show Code Evolution Through Discovery**

Perfect code on first try is the #1 AI tell. Show iteration:

✅ **Natural discovery patterns:**
```typescript
// 最初これ書いた
const result = data.map(x => x.value);
// あ、これundefinedの場合に落ちる
const result = data.map(x => x?.value ?? 0);
// いや待って、0じゃなくてfilterすべきか...
```

✅ **Bug realization mid-explanation:**
- Show code first, then: "あ、これバグあるな..."
- Sometimes fix immediately, sometimes leave it: "まあ、動くので放置"
- Multiple attempts: V1 → "ダメだった" → V2 → "これもダメ" → V3 → "やっと動いた"

❌ **AI tells to avoid:**
- Every code block perfect and polished
- Never showing mistakes or iteration
- No mid-explanation realizations

**CRITICAL: Add Ecosystem Context**

Articles without external references feel isolated. Include 1-2 of:

✅ **GitHub references (casual, not cited):**
- "(#2851とか)" buried in sentence
- "実装見てると色々工夫されてて面白い"
- PR authors: "Andersさんのコメント見ると〜"

✅ **Community awareness:**
- "Twitterで〜という話を見た"
- "zodみたいなライブラリもあるけど"
- Debates: "この辺は意見が分かれてて"

✅ **Temporal context:**
- "TypeScript 5.5で〜が入るかも"
- "昔は〜だったけど、今は〜"
- "2019年くらい？にこの話題で議論してた記憶がある"

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

**CRITICAL: Include Unresolved Elements (MANDATORY)**

Perfect resolution is an AI tell. Every article MUST have at least 2-3 of:
- [ ] Partially answered questions ("この辺はまだ分かってない")
- [ ] Speculation without confirmation: "これはうまくいくかもしれないけど、確認してない"
- [ ] Tangents that don't tie back: "余談ですが〜" (never returns to main point)
- [ ] Future intentions: "いつか〜を試したい", "そのうち検証したい"
- [ ] Abandoned threads: Start explaining something → "本題から逸れるのでこの辺で"
- [ ] Mid-article admission: "さっき言い忘れたけど〜"

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

**Top AI Tells to Avoid:**
1. **PERFECT CODE**: Every example works on first try (instead: show bugs → fixes)
2. **COMPLETE RESOLUTION**: All questions answered, no loose ends (instead: 2-3 unresolved elements)
3. **NO ECOSYSTEM**: Article exists in vacuum (instead: GitHub/community/temporal refs)
4. **UNIFORM DEPTH**: Equal detail on all topics (instead: 15 paragraphs on favorite topic, 2 sentences on boring one)
5. **STRATEGIC IMPERFECTIONS**: Evenly distributed (instead: random clustering, some sections perfect)
6. **ARTIFICIAL FORMALITY**: 95%+ polite OR 90%+ casual (instead: 40-60% polite, context-dependent)
7. **COLONS BEFORE CODE**: Using "：" to introduce code blocks (instead: end with period, start code block)
8. **FORMULA-FOLLOWING**: Mechanical application of guidelines (instead: think deeply, techniques emerge naturally)

---

**Last updated:** Iteration 2 (pre-generation)
**Version:** 1.2 (PATTERN DISCOVERY: Added colon usage rule, exploratory review phase)
**Target:** <350 lines | **Current:** 428 lines (consolidation needed)
