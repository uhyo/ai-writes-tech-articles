# Technical Article Style Guide

This guide defines standards for generating Japanese technical articles indistinguishable from human-written content.

---

## 🔴 CRITICAL REQUIREMENTS (Publication Blockers)

These are non-negotiable. Violating any of these makes the article unpublishable.

### 1. Japanese Language & Tone

**CRITICAL: です・ます (polite form) throughout**
- Use "です/ます" form consistently, NOT "だ/である" form
- Mix in casual expressions naturally: "〜ですね", "〜でしょう", "〜なんです"
- Conversational yet informative tone

❌ **Wrong**: "結論から言うと、これProxyとWeakMapだけで実装できる。" (だ form)
✅ **Correct**: "結論から言うと、これはProxyとWeakMapだけで実装できます。" (です form)

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
- [ ] **です・ます polite form** used throughout (→ §1)
- [ ] Valid frontmatter with all fields (→ §1.2)
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

### 5.1 Tone & Voice (Conversational Flow)

**Personal Voice Guidelines:**
- Use "筆者" sparingly: **3-5 times maximum per article**
- Vary attribution style:
  - "個人的には〜" (personal opinion without "筆者")
  - "自分の経験では〜" (casual self-reference)
  - State opinions directly without attribution
- Express subjective judgments freely

**CRITICAL: No Pedagogical Scaffolding**

Write as peer conversation, not teacher-to-student lecture.

❌ **Avoid these teaching transitions:**
- "では、〇〇の場合はどうでしょうか" (textbook-style)
- "本題に戻ると" (unnecessary announcement)
- "ここで〇〇について見ていきましょう" (classroom guide language)
- "理屈はわかった。じゃあ実装してみよう。" (guides reader like student)
- "使い方はこう：" (instructional setup before code)
- "次に〇〇を説明します" (explicit structure signaling)
- "この実装の何が良いかって" (meta-explanation of what you're explaining)

✅ **Use natural transitions:**
- "で、〇〇だと話が変わる。"
- "なぜ〇〇なのか。" (just resume without announcement)
- Just start code without preamble
- "試しに〜してみると" (discovery, not instruction)

**CRITICAL: Vary Explanation Depth + Show Trial-and-Error Process**

Real writers prioritize ruthlessly AND show how they arrived at solutions.

❌ **Avoid these AI tells:**
- Explaining every concept with same care level
- Presenting final, polished solutions directly
- Every section getting 3-4 similar-length paragraphs

✅ **Vary depth AND show evolution:**
- Key insights: Show V1 → problem → V2 → V3 progression
- Obvious points: Single sentence, move on
- **Show failed attempts:** "最初はこう書いたが〜で問題が出た"
- Example progression:
  ```typescript
  // 最初の実装
  type V1<T> = ...;  // でもこれだとXの場合に問題

  // ユニオン型に対応
  type V2<T> = ...;  // 今度はYで詰まった

  // 最終形
  type V3<T> = ...;
  ```

### 5.2 Technical Depth (GitHub References)

**CRITICAL: Go Deep on "Why" and "How", Not Just "What"**

Surface-level explanations are AI tells. Humans explain mechanisms, not just facts.

❌ **Shallow (just describes what):**
- "useRefは再レンダリングを引き起こしません"
- "TypeScript 5.0で新機能が追加されました"

✅ **Deep (explains why/how):**
- "useRefは`current`プロパティを持つオブジェクトを返すだけ。このオブジェクトはコンポーネントのライフサイクル全体で同一のインスタンスが保持されます。"
- "`use`も普通のフックと同様に「何番目の呼び出しか」に依存して記憶領域からデータを読みだします。それにも関わらず`use`を条件分岐の中で使用できるのは、**レンダリングの最中に条件分岐の結果が変わることはないという仮定**を設けているからです。"

**Depth indicators:**
- Explain internal mechanisms: "実装を見ると〜という仕組みで動いている"
- Mention constraints: "〜という仮定を設けているから"
- Describe edge cases: "〜の場合は動作が変わる"
- Reference specific implementation details (line numbers, functions)

**GitHub References: Natural Integration**

✅ **Required specificity:**
- Link to PRs with numbers: `https://github.com/microsoft/TypeScript/pull/49119`
- Include authors: "もちろんPRの著者はAndersさんです"
- Cite line numbers: "#L1900あたり"

**CRITICAL: Make references feel natural, not cited**

❌ **Formal citation style:**
- "PR #2851でshallowとnormalのProxyを別々にtrackする修正が入った"

✅ **Casual integration:**
- "(#2851とか)" buried in sentence
- "実装見てると、shallow proxyの扱いとか配列の最適化とか色々工夫されてて面白い" (no numbers)
- "Reactのソースコード（URL）を見ると、React内部でもこのパターンが使われてます" (URL in passing)

### 5.3 Authentic Anecdotes

**CRITICAL: Multiple Anecdotes, Not Just One**

Humans naturally weave anecdotes throughout. Don't save it all for one "story section."

❌ **Single episode pattern:**
- One elaborate anecdote in article
- Everything else is theory/explanation
- Episode feels like "assigned story"

✅ **Scattered organic mentions:**
- Brief experience mention in section 2
- Longer story with digressions in section 4
- Quick callback in section 6: "さっきの話と似てるけど"
- Mix depths: Some 1-sentence, others multi-paragraph

**Personal experiences need rich contextual details:**

❌ **Generic:** "実務でよく遭遇する" "去年あるプロジェクトで3日消費"
✅ **Rich context:** "去年、社内の古いExpress API（100個くらいエンドポイント）をTypeScript化するプロジェクトで、『既存の全部に型つけろ』って無茶振りされた。最初は『stringでいいんじゃない？』って思ってたけど、実際にやり始めたらパスパラメータの抽出で詰まって、気づいたら3日溶けてた"

Include: project context, tech stack, what you tried, what failed

**CRITICAL: Real stories digress and meander**

Include 1-2 asides per major anecdote that don't directly advance the point:
- "（ちなみにこのプロジェクト、最初はReduxで書き直す予定だったのが、途中でみんな飽きて別の方向に...）"
- Types: Scope changes, team dynamics, failed approaches

### 5.4 Structure & Organization

**CRITICAL: Limit section count**
- **Target 6-7 H2 sections maximum** for typical articles (not 10+)
- Combine related topics rather than splitting everything
- Use H3 subsections within larger sections when needed

**CRITICAL: No numbered enumeration**
- ❌ "パターン1: 〜", "パターン2: 〜", "パターン3: 〜"
- ❌ "方法1", "方法2", "方法3"
- ✅ Use descriptive headings: "useMemoで値を安定化させる"

**CRITICAL: Create Organic Flow Between Sections + Reader Dialogue**

Sections shouldn't feel like independent encyclopedia entries. Connect them AND engage readers.

❌ **Independent sections (too isolated):**
- Each section self-contained with no callbacks
- Every section feels like fresh start
- No direct reader engagement

✅ **Connected flow with reader dialogue:**
- Reference previous: "さて、先ほどの〇〇の話に戻ると"
- Build on earlier: "ここまでで〇〇を見たけど、実は〜"
- Use connectors: "ところで", "そういえば", "実は"
- **Engage readers directly:**
  - "お察しの通り、これには問題があります"
  - "皆さんならどうしますか"
  - "ご存知の方も多いでしょうが"
  - "これを見て『なんで？』と思った方"
- Example: "さて、すでに何か勉強した気になったかもしれませんが、これはReact 17と特に関係ない話なのでまだ復習です。"

**Vary section lengths dramatically:**
- Some sections: 1-2 paragraphs (brief point)
- Others: 5-8+ paragraphs (deep dive when genuinely interested)
- Never make all sections roughly equal length
- Follow your interest, not templates

### 5.5 Strong Assertions (Not Everything Hedged)

**CRITICAL: Don't Qualify Every Statement**

Excessive hedging ("〜だと思います", "〜気がします") is an AI tell.

❌ **Over-qualified (weak):**
- "個人的には〜だと思います" (repeats every section)
- "〜という気がします"
- "〜のような気もします"
- Balanced view on everything: "一方で〜というメリットもありますが、デメリットもあります"

✅ **Mix assertion strengths:**
- Strong opinions on some topics: "これは間違いです", "〜すべきです", "〜は無駄"
- Direct statements without hedging: "useRefの本質は〜です。" (just state it)
- Save qualifiers for genuinely uncertain claims
- Example: "なんでもかんでもstateに突っ込むと、無駄なレンダリングが増えるだけ。" (no hedging)

**Humans vary conviction levels:**
- Strong when confident: "〜です", "〜だ"
- Hedged when exploring: "〜かもしれない"
- Don't hedge facts: "useRefは再レンダリングを引き起こさない" (not "引き起こさないと思います")

### 5.6 Conclusions (まとめ)

**CRITICAL: Avoid synthesizing everything neatly**

Real conclusions are messy and incomplete.

❌ **Avoid:**
- "重要なポイントをまとめると: [bullet list]" (too organized)
- "結局、核心は3つだけ：1. 〜 2. 〜 3. 〜" (numbered synthesis)
- "今回は〜を見てきました。〜が重要です。" (textbook recap)
- Neat takeaways that tie everything together

✅ **Better ending styles:**
- End abruptly with final observation
- Raise remaining questions or uncertainties
- Take controversial stance without balancing it
- Admit limitations, unknowns, unresolved issues
- Mix resolved insights with loose ends
- Trail off into tangential thoughts

**Example:**
"結局どう使えばいいのか。個人的には小規模なら最高。でも大規模だと厳しい。あと、この機能を使うなら型システムの理解が必須。"

---

## 🟢 POLISH: Final Refinements

These are final touches that push quality from good to excellent.

### 7. Micro-Imperfections for Authenticity

**CRITICAL: Perfect polish is an AI tell**

Humans writing in flow have minor inconsistencies. Include 1-2 per article:

✅ **Acceptable imperfections** (NOT typos/errors):
- Casual contractions: "んだけど" (not "のだけど"), "んで" (not "ので")
- Self-corrections: "〜というか、正確には〜"
- Informal abbreviations: "ちな" for "ちなみに" (sparingly)
- Slight repetition: Repeating word/phrase in adjacent sentences naturally
- Trailing thoughts: Sentences ending with "..."

❌ **Don't include:** Typos, grammatical errors, broken code

**Example:** "で、実際に試してみたんだけど、思ったより簡単だった。というか、簡単すぎて拍子抜けした。"

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
- Pedagogical scaffolding: "それでは〜を見ていきましょう", "この実装の何が良いかって"
- Uniform politeness: Every concept explained with same care level
- **Finished solutions only: No V1→problem→V2 progression**
- Isolated sections: No callbacks to previous content, no reader engagement
- Single anecdote: One story, rest is theory
- Weak assertions: "〜だと思います" repeated everywhere
- Shallow depth: Describes "what" but not "why/how"
- No footnotes: Everything inline, disrupting flow
- Numbered patterns: パターン1/2/3
- Neat conclusions: Synthesized bullet lists
- Formal GitHub citations: PR/issue numbers as subjects
- Excessive "筆者": >5 times per article

---

---

**Last updated:** Iteration 6
**Version:** 2.2 (Added trial-and-error process, reader dialogue)
**Target:** <350 lines | **Current:** 362 lines
