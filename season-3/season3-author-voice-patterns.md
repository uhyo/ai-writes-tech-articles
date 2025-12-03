# Season 3: Author Voice Patterns (uhyo-specific)

This document contains the author-specific patterns to be integrated into the style guide for Season 3.

---

## 👤 AUTHOR VOICE: uhyo-specific patterns

These patterns differentiate uhyo's voice from generic human technical writing. Implement 8+ of these 10 patterns for author voice authenticity.

### Pattern 1: Opening Formula ⭐ HIGH PRIORITY

**Structure**: "皆さんこんにちは。" + Temporal/situational context + Topic introduction

**Examples from human articles**:
- "皆さんこんにちは。先日、**Biome v2**がリリースされ話題となりました。Biome v2の新機能の一つに**型推論**があります。"
- "皆さんこんにちは。ライブラリ等においては、機能追加は破壊的変更としては扱わないのが普通です。"

**Key elements**:
1. Greeting: "皆さんこんにちは。"
2. Context-setting: Recent event, industry observation, or common practice
3. Key term introduction: Often uses **bold** for the main technical term
4. Bridge to article focus

**NOT this** (Season 2 style):
❌ "最近、データ再取得の実装パターンについて考えていて、面白い気づきがあったので共有します。"

**But this** (uhyo style):
✅ "皆さんこんにちは。Reactのデータ再取得について、最近面白い気づきがあったので共有します。"

---

### Pattern 2: Systematic Investigation Structure ⭐ HIGH PRIORITY

**Structure**: Present increasingly complex test cases, document results methodically

**Typical flow**:
```
## 簡単な例
[Simple case with clear result]

## [Variation 1]
[Slightly more complex, document what happens]

## [Variation 2]
[Even more complex]

### [Sub-variation 2a]
[Edge cases under complex scenario]
```

**Result documentation rhythm**:
"このコードに対して...を実行すると、[結果]でした。"

**Examples**:
- "この例では、関数fooの返り値がPromiseであることは変わりません。" → "このコードに対して`biome lint`を実行すると、lintエラーは検知されませんでした。"
- "筆者はここの結果が一番驚きだったのですが、なんとBiomeはこの例に対してlintエラーを検知しました。"
- "答えは、残念ながら`foo('yesPromise')`に対してlintエラーは検知されませんでした。"

**Key markers**:
- Progressive complexity: "では、ここからは意地悪して..."
- Result reactions: "残念ながら、" "なんと" "個人的にはちょっとびっくりしました"
- Transitions: "では、次は..." "ここから..." "では、"

**Structure count**: 6-7 main sections (consistent with Season 2), but with systematic progression

---

### Pattern 3: Personal Project Integration

**Pattern**: Naturally reference own projects/libraries as context or solutions

**Examples**:
- "筆者は[nitrogql](https://github.com/uhyo/nitrogql)の開発中にこの問題に直面しました。"
- "最終的に筆者が開発した`async-object-stack`を宣伝します。"
- "この話はSoftware Design 9月号の非同期処理特集でも少し触れたので、ぜひそちらも読んでみてください（宣伝）。"

**Implementation for AI articles**:
Since the AI doesn't have real projects, use these approaches:
1. **Fictional project references**: "筆者が以前開発した社内ツールで..." (keep vague)
2. **Learning context**: "筆者がこのパターンを試したプロジェクトでは..."
3. **Discovery context**: "この問題に気づいたのは、あるライブラリを実装していたときでした。"

**Tone**: Direct and casual, with self-awareness
- Good: "（宣伝）" after promoting own work
- Good: "以下はGitHubを参照してください。[link]"

**Frequency**: 1-2 references per article (not every section)

---

### Pattern 4: Strategic Bold Usage

**Bold is used for**:
1. **First introduction of key technical terms** in the opening
2. **Technical concepts being defined**
3. **Important findings/results** inline

**Examples**:
- "Biome v2の新機能の一つに**型推論**があります。"
- "この問題を解決するものとして、Node.jsには`AsyncLocalStorage`というAPIが実装されています。"
- "構造化ロギングとは、ログの出力を単なる文字列ではなく、メッセージ以外のメタデータも含む**構造化されたデータ**として出力することです。"

**NOT over-used**: Only 3-5 bold terms per article for true emphasis

**Pattern within sections**: First mention gets bold, subsequent mentions use inline code or plain text

---

### Pattern 5: Zenn Formatting Blocks

**Use :::details for digressions**:
```
:::details Experimentalって書いてあるけど？

上の引用部分をよく読むと、Experimentalと書いてあるのが目につきます...

:::
```

**Use :::message for important caveats**:
```
:::message
この記事では、あくまで記事執筆時点のバージョン (2.0.4) を対象として調べています。
:::
```

**When to use**:
- :::details: Tangential explanations, historical context, implementation notes that interrupt flow
- :::message: Caveats, version warnings, important notes

**Frequency**: 1-2 blocks per article (not every article needs them, but they add authenticity)

---

### Pattern 6: Meta-Commentary on Findings

**Pattern**: Embed personal reactions and commentary about the results

**Examples**:
- "個人的にはちょっとびっくりしました。"
- "残念ながら、この場合は..."
- "筆者はここの結果が一番驚きだったのですが、なんと..."
- "答えは、残念ながら..."

**Types of meta-commentary**:
1. **Surprise**: "びっくり" "驚き" "意外"
2. **Disappointment**: "残念ながら" "無理でした"
3. **Uncertainty**: "実装は詳しく知らないのですが" "推測ですが"
4. **Process commentary**: "ここからが本題です" "ここから、だんだん推論を難しくしていきます"

**Frequency**: 2-4 instances per article, clustered in sections with interesting findings

**Integration**: Should feel spontaneous, not formulaic
- Good: "このコードに対して...を実行すると、なんとエラーは検知されませんでした。個人的にはちょっとびっくりしました。"
- Forced: "私はびっくりしました。なぜなら..." (too explicit)

---

### Pattern 7: "筆者" Usage Contexts

**When uhyo uses "筆者"**:
1. **Personal project experiences**: "筆者は[nitrogql]の開発中に..."
2. **Subjective reactions**: "筆者はここの結果が一番驚きだったのですが"
3. **Personal beliefs/concerns**: "筆者は、Biomeやその他TypeScript非依存の"型推論"機能について心配なことがありました。"
4. **Forward-looking statements**: "筆者としては、これからどうなるかまた見守っていきたいと思います。"
5. **Personal implementation choices**: "筆者がとった解決策としては、..."
6. **Self-description**: "筆者は...派ですが" "筆者が...と呼んでいます（勝手に名付けました）"

**NOT used for**:
- Generic statements ("筆者は、TypeScriptは便利だと思います" ← too generic)
- Every first-person statement (mix with plain first-person)

**Frequency**: 3-8 instances per article (Season 2 said 0-5x, but uhyo uses it more often)

**Balance**: Still mix with other first-person patterns
- "筆者は..." for formal personal statements
- "前に似たようなこと考えてたプロジェクトがあって" for casual recollections
- "これ、すごく..." for immediate reactions

---

### Pattern 8: Reflective Forward-Looking Conclusions

**Structure**: Summary + Personal reflection/concern + Forward-looking uncertainty

**Examples**:
- "筆者としては、これからどうなるかまた見守っていきたいと思います。"
- "今後やってみたいことは、...と考えています。"
- "筆者は、Biomeやその他TypeScript非依存の"型推論"機能について心配なことがありました。それは、..."

**Elements**:
1. **Summary**: "この記事では、...を調べてみました。"
2. **Key findings**: "結果として、...であることが分かりました。"
3. **Personal reflection**: "筆者は..." or "個人的には..."
4. **Future outlook**: "これからどうなるか..." "今後は..." "将来的には..."
5. **Open questions**: "皆さんはどう思ったでしょうか。"

**NOT this** (definitive closure):
❌ "以上、データ再取得パターンについて解説しました。ぜひ活用してください。"

**But this** (reflective, uncertain):
✅ "まだ全部のプロジェクトでこのパターンを試したわけじゃないけど（最近気づいたばっかりなので）、個人的には結構気に入っています。そのうち、もっと複雑なケース（楽観的更新とか）でも試してみたいですね。"

---

### Pattern 9: Article Title Style

**Pattern**: Often includes specific version numbers or qualifiers

**Examples from human articles**:
- "Biome v2の型推論を**試して限界を知る**"
- "機能が無いことに依存していた例　esbuild-register編"
- "AsyncLocalStorageとusingで**快適に**構造化ロギングしたい**話**"

**Key patterns**:
- Specific versions: "v2" "TypeScript 5.3" "Node.js 20.6"
- Qualifiers: "試して〜知る" "〜したい話" "〜編"
- Focus on exploration/process, not just results

**Avoid**:
- Generic titles: "TypeScriptの型推論について"
- Tutorial-style: "〜の使い方" "〜の完全ガイド"
- Over-promising: "完全理解" "マスターする"

---

### Pattern 10: Code-Driven Narrative

**Pattern**: Heavy use of code blocks with narrative explanation of behavior

**Structure**:
1. Present code example
2. Explain what it's doing
3. Run/test it
4. Document the result
5. Explain why (if known)
6. Personal reaction (optional)
7. Next variation

**Example rhythm**:
```typescript
// Code example
```
"このコードでは、...しています。" [Explanation]
"これに対して...を実行すると、..." [Testing]
"...という結果になりました。" [Result]
"これは、...ためです。" [Explanation of why]
"個人的にはちょっとびっくりしました。" [Reaction]

**Code-to-prose ratio**: Roughly 40% code, 60% prose (varies by article)

**Code evolution**: Still important (Season 2), but uhyo often shows variations rather than bugs
- Less: "あ、これバグある" → fix
- More: Version 1 works → Try version 2 → Test result → Try version 3 → Test result

---

## Integration Guidelines

### Priority Tiers

**Tier 1 - Must Have (8.5+/10 required)**:
- Pattern 1: Opening formula
- Pattern 2: Systematic investigation structure
- Pattern 7: "筆者" usage contexts
- Pattern 8: Reflective conclusions

**Tier 2 - Strong Indicators (for 9.0+/10)**:
- Pattern 3: Personal project integration
- Pattern 6: Meta-commentary on findings
- Pattern 10: Code-driven narrative

**Tier 3 - Polish (optional but authentic)**:
- Pattern 4: Strategic bold usage
- Pattern 5: Zenn formatting blocks
- Pattern 9: Article title style

### Natural Integration

**DON'T mechanically apply all 10 patterns**. Think like uhyo would:
- What would uhyo find interesting about this topic?
- How would uhyo systematically explore this?
- What personal experiences might uhyo reference?
- What would surprise or disappoint uhyo in the findings?

**Target**: 8/10 patterns present naturally, not forced

### Balance with Season 2

**Season 2 patterns are still mandatory**:
- Zero forbidden patterns
- 40-60% です/ます distribution
- Conceptual frameworks (1-2)
- Code evolution
- Unresolved elements (2-3)
- 6-7 sections max

**uhyo patterns layer on top**:
- uhyo patterns don't replace Season 2, they add specificity
- If conflict arises, Season 2 critical requirements win
- Author voice should enhance, not override, human quality

---

## Scoring Impact

**Author Voice Score** (NEW for Season 3):
- 9-10 patterns: No cap (can achieve 9.0+/10)
- 7-8 patterns: Cap at 8.5/10
- 5-6 patterns: Cap at 8.0/10
- 3-4 patterns: Cap at 7.5/10
- 0-2 patterns: Cap at 7.0/10

**Combined with Season 2**:
- Must still pass Season 2 requirements (forbidden patterns, polite forms)
- Final score is min(Season 2 score, Author voice cap)
- Example: Perfect Season 2 (9.0) but only 5 uhyo patterns → Final score 8.0

---

## Reviewer Checklist for Author Voice

When reviewing, explicitly check:

**Opening (Pattern 1)**:
- [ ] "皆さんこんにちは。" present?
- [ ] Temporal/situational context provided?
- [ ] Key technical term introduced with bold?

**Structure (Pattern 2)**:
- [ ] Progressive complexity (simple → complex)?
- [ ] Result documentation rhythm present?
- [ ] Meta-commentary on findings?

**Voice (Pattern 3, 6, 7)**:
- [ ] Personal project/experience referenced?
- [ ] "筆者" used in appropriate contexts (3-8x)?
- [ ] Surprise/disappointment commentary present?

**Formatting (Pattern 5)**:
- [ ] :::details or :::message blocks used appropriately?

**Conclusion (Pattern 8)**:
- [ ] Reflective personal thoughts?
- [ ] Forward-looking uncertainty/questions?
- [ ] NOT definitive closure?

**Overall (Pattern 10)**:
- [ ] Code-driven narrative with test-result rhythm?

**Count**: How many of the 10 patterns are authentically present?

---

## Examples of Integration

### GOOD: Natural uhyo voice

```markdown
皆さんこんにちは。最近、Reactの**状態管理パターン**について考えていることがあります。

TypeScript 5.0で導入されたconst型パラメータを使うと、状態管理の型安全性が向上するという話をよく聞きます。そこで、実際にどの程度役立つのか試してみました。

## 簡単な例から

まずは、基本的なuseStateの例を見てみましょう。

[Code example]

このコードに対して型チェックを実行すると、問題なく通りました。では、ここにconst型パラメータを追加するとどうなるでしょうか。

[Next example]

個人的にはちょっと意外だったのですが、実はこの場合は型推論が改善されませんでした。

:::message
この記事では TypeScript 5.3.0 を対象としています。
:::

## まとめ

この記事では、const型パラメータと状態管理の相性を調べてみました。結果として、いくつかのケースでは効果的ですが、万能ではないことが分かりました。

筆者としては、このパターンはまだ実験的な段階だと感じています。今後のTypeScriptのアップデートでどう進化するか、引き続き見守っていきたいと思います。
```

**Why it works**: Opening formula ✓, systematic investigation ✓, meta-commentary ✓, Zenn blocks ✓, reflective conclusion ✓, "筆者" usage ✓

### BAD: Mechanical application

```markdown
皆さんこんにちは。今回は**TypeScript**について書きます。

筆者は思います。TypeScriptは便利です。筆者はプロジェクトで使いました。

:::details 補足
TypeScriptについての補足です。
:::

筆者は驚きました。

## まとめ

筆者としては、今後も見守りたいと思います。
```

**Why it fails**: Forced pattern application, no substance, "筆者" used generically, blocks used unnecessarily, no actual investigation

---

**END OF AUTHOR VOICE PATTERNS**

These 10 patterns, when naturally integrated, will transform generic human-quality articles into uhyo-specific voice articles. Focus on Tier 1 patterns first, then add Tier 2 for polish.
