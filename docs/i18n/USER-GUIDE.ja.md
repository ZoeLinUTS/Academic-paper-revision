# ユーザーガイド

> [English](../USER-GUIDE.md) | [中文](USER-GUIDE.zh-CN.md) | **日本語** | [한국어](USER-GUIDE.ko.md) | [Español](USER-GUIDE.es.md) | [Français](USER-GUIDE.fr.md) | [Deutsch](USER-GUIDE.de.md) | [Português](USER-GUIDE.pt.md)

`academic-paper-revision` skill の使い方、何を渡すか、何が返るか、そして 2 つの動作モードの違い。

## 1. 始める前に：用意するもの

| モード | 提供する入力 | Claude が行うこと |
| --- | --- | --- |
| **フルリビジョン** | **LaTeX ソース**：メイン `.tex`、`references.bib`、会議のクラス/スタイル（`llncs.cls` + `*.bst`、または `acl.sty` …）、`figures/`。Overleaf の**プロジェクトエクスポート（zip）**が理想。 | ソースに外科的な編集を行い、増えた分量を相殺し、再構築したアップロード用 zip を返す。 |
| **レビューのみ** | **PDF**（またはソース）で十分。 | 読み取ってコメントするのみ——**ファイルは変更しない**。 |

フルリビジョンにソースが必要なのは、PDF を外科的に編集できず、PDF からソースを再構築すると
書式や数値が失われる恐れがあるためです。レンダリング済みの `main.pdf` はレンダリング時の
プロパティ（ページ数、フロート配置）の確認に役立つ*補助的な*入力ですが、単独では決して十分では
ありません。

## 2. 目標の会議を Claude に伝える

これは決定的に重要です。同じ編集でも、ある会議には正しく、別の会議には即リジェクトになりえます。
会議名（ACL、NAACL、EMNLP、AJCAI、NeurIPS …）を明示してください。会議の正確な規則が不明な場合、
Claude は編集前に最新の投稿募集を調べます。ファイル名がある会議を示し、メモが別の会議を示す場合、
その矛盾を指摘して尋ねます——決して臆測しません。

## 3. モードを選んで依頼する

**フルリビジョン** — 例：
- 「これを EMNLP 向けにリビジョンして、採択論文のように読めるようにして。」
- 「これを AJCAI のページ制限に削減して、RQ の足場を外して。」
- 「手法に数式/アルゴリズムを加えて、相互参照を直して。」

**レビューのみ** — 例：
- 「このドラフトをレビューして——概観とコメントだけ、何も編集しないで。」
- 「新規性を評価して、想定される査読者の反対意見を教えて。」
- 「ここの範囲と主な問題は？先行研究とどう違う？」

### 対話言語 — 任意の言語

**任意の言語**で作業し、その言語で結果を受け取れます。Claude は*対話*をどの言語で行うか——中国語、
スペイン語、アラビア語その他——を尋ねる（または推測する）し、すべての概観・コメント・所見・要約を
その言語で届けます。これは原稿とは独立です：**論文は英語のまま**（または会議が要求する言語）で、
対話だけがあなたの選んだ言語に従います。

## 4. 出力の形

両モードとも同じ査読者風のメモで始まります：

1. **オリエンテーション・ブリーフィング** — *ドラフトが何か* · *中核的貢献* ·
   *新規性（率直、査読者攻撃の箇条書き付き）* · *完成度*。会議を確認し、望む範囲を尋ねて締めます。
2. **所見リスト** — 問題ごとに 1 行、グループ化・重要度順、各行が修正を示す。例：
   - `[venue]` 目標 = ACL (ARR)：本文 8 ページ、Limitations/Ethics 無料、ダブルブラインド。
   - `[structure]` 明示的な RQ1–RQ3 の足場 → 散文形式の貢献に変換。
   - `[formalization]` スコアリング規則が中心なのに数式ゼロ → 2–3 式を追加。
   - `[rigor]` 目玉の向上に有意性検定なし / 単一分割 → 最大の弱点として指摘。
   - `[bug]` `\ref{fig:pipeline}` が `\label` のない図を指す → label を追加。
   - `[cross-ref]` 関連研究が年代順のリスト → テーマ別に再編成。
   - `[page-fit]` +0.5 ページ超過 → それを相殺する正確な `[trim]` 手順とペア
     （冗長な段落を統合、ワークト例を付録へ移動）。

**レビューのみはここで終了**（加えて具体的な提案と 1 つの率直な注意）。ファイルは触れません。

**フルリビジョンは続く：**

3. **変更サマリー**（編集後）— 何を*なぜ*変えたか、順に：適用した会議慣習 · 枠組み/構造 ·
   追加した形式化 · 図 · ビルド/相互参照の修正 · ページ調整。次に**あなたがすべきこと**：再コンパイル
   （例：Overleaf で）、指名されたレンダリング時項目（ページ数、特定のグリフ/フロート）の確認、
   チェックが失敗した場合の次の具体的手段。ローカルで確認できなかった点や未確認の会議規則を明示します。
4. **1 つの率直な注意** — 最大の査読者リスクを率直に述べる。

## 4b. 実例（入力 → 出力）

具体的には、同梱の `examples/student-draft/` での実行はこう見えます。

**期待される入力** — LaTeX ソース（PDF ではない）と、指定した会議：

```
examples/student-draft/
  main.tex          # article クラスのドラフト：RQ1–RQ3、汎用的な見出し、
                    #   数式ゼロ、\label のない図への \ref{fig:pipeline}
  references.bib    # 提供されるが \cite されない
```

> プロンプト：*「これを **ACL (ARR)** 向けにレビューし、採択論文のように読めるよう改稿して。」*

**サンプル出力 — Claude が最初に返すブリーフィング + 所見リスト：**

```
Orientation briefing
  What it is   : a sentiment classifier that keeps punctuation as tokens; 3 sections.
  Contribution : punctuation-aware tokenization improves accuracy on movie reviews.
  Novelty      : thin as written — the idea is plausible but reads as one ablation.
                 Reviewer attack: "this is a preprocessing tweak, where's the analysis?"
  Completeness : results present but no significance test; one figure renders "??".

Findings (severity-ordered)
  [venue]         Target = ACL (ARR): 8-page body, Limitations/Ethics free, double-blind.
  [structure]     Explicit RQ1–RQ3 scaffolding → convert to prose contributions.
  [formalization] Zero equations though the scoring rule is central → add 2–3 eqs.
  [rigor]         Headline gain has no significance test / single split → top weakness.
  [bug]           \ref{fig:pipeline} targets a figure with no \label → add the label.
  [cross-ref]     Related work is a dated list → regroup thematically.
  [venue]         No Limitations section — ACL requires it and it is page-free → add.
```

**レビューのみ**はそこで終わります（加えていくつかの具体的提案と 1 つの率直な注意）。
**フルリビジョン**はその後編集を行い、何を再コンパイルし確認すべきかを正確に伝える変更サマリーを
返します。どの実行もサンプルの `EXPECTED-FINDINGS.md` に照らして採点できます。

## 5. Overleaf の往復（フルモード）

1. Overleaf で：**Menu → Download → Source**（zip）。
2. その zip（または展開したソース）を Claude に渡す。
3. Claude はソースを編集し、ビルド入力（`.tex`、`.bib`、クラス/スタイル、`figures/`）だけを含む
   アップロード用 zip を再構築します——`main.pdf`、README、git ファイルは除外。
4. zip を Overleaf に再アップロードして再コンパイル。
5. Claude が指名したレンダリング時項目を確認（ページ数はレンダリング時のプロパティなので、
   コンパイル後にしか確認できません）。

## 6. 研究誠実性の保証

- **数字を決して捏造しない。** 結果のプレースホルダーは、あなたが提供した実際の保存済み実行からのみ
  埋められます。結果がまだ存在しなければ、プレースホルダーのまま残します。
- **技術的内容を保持する。** 編集は外科的で、知的内容は残り、提示方法だけが変わります。
- **求められない共有操作をしない。** あなたの依頼なしに commit、push、送信は行いません。

## 7. サンプルで試す

2 つの合成ドラフトが含まれます（架空の著者・データセット・数値——IP なし）。それぞれに実行を採点
できる `EXPECTED-FINDINGS.md` 解答例が付いています：

- **`examples/student-draft/main.tex`** — ページ制限*未満*で、典型的な問題を仕込み済み：
  RQ1–RQ3 の足場、汎用的な見出し（"Approach"、"Experiment 1"）、年代順の関連研究、数式ゼロ
  （スコアリング規則が中心なのに）、そして図に `\label` のない `\ref{fig:pipeline}`（`??` と表示）。
  有意性検定もなし——`[rigor]` の所見。
- **`examples/over-length/main.tex`** — ACL ショートペーパー（4 ページ）制限が対象だが約 5〜6 ページ。
  他は仕上がっているので、**削減ステップ**を練習：冗長な導入段落 2 つを統合、繰り返しの関連研究を
  引き締め、長いワークト例を付録へ移動。

いずれのドラフトでもいずれのモードを実行して、ブリーフィング、所見リスト、そして——フルモードでは
——編集を確認できます。

## 8. FAQ

**PDF だけ渡してもいい？** レビューのみなら可。フルリビジョンは不可——Claude が LaTeX ソースを求めます。

**論文を私のチャット言語に翻訳する？** いいえ。原稿は会議の言語のまま。対話だけがあなたの選んだ
言語に従います。

**非 ACL の会議でも使える？** はい。`references/venue-conventions.md` は ACL 系、Springer/LNCS/AJCAI、
ML 会議をカバー。それ以外は Claude が最新の CFP を先に読み、学んだ内容を記録します。

## 9. 他の AI モデルで使う（Codex、DeepSeek、Gemini …）

この skill は**モデルに依存しない Markdown 指示**にすぎません——Claude 固有なのは `SKILL.md` 先頭の
YAML フロントマター（`---` の間の `name:`/`description:` ブロック）だけで、それは自動読み込み用の
メタデータです。その下はすべて、有能なモデルなら従える普通のプロンプトです。他所で使うには：

1. **`SKILL.md` の本体をコピー** — 閉じ `---` *以降*のすべて — を他ツールの**システムプロンプト**に
   貼る（または最初のメッセージとして貼る）。YAML フロントマターは落として構いません。Claude の
   skill 自動発見にのみ関係します。
2. **参照ファイル** `references/venue-conventions.md` をコンテキストとして添付（貼るか、ツールの
   ファイル/コンテキスト欄に追加）し、モデルに会議規則を持たせます。
3. **論文も同じ方法で渡す** — フルリビジョンは `.tex` ソース、レビューのみは PDF — そして目標の会議を
   指名します。

ツール別メモ：

- **OpenAI Codex / ChatGPT、DeepSeek、Gemini、ローカルモデル：** `SKILL.md` 本体をシステム/開発者
  プロンプトに、venue-conventions ファイルをコンテキストに貼り、上記の通り進めます。形式の変更は不要
  ——すべて散文の指示です。
- **Cursor / Windsurf / Continue などの IDE エージェント：** ツールが読むプロジェクトルールファイル
  （例：`.cursorrules`、`AGENTS.md`）に `SKILL.md` 本体を入れて自動読み込みし、`references/` をリポジトリ
  に保持します。
- **異なりうる点：** ツール呼び出しやファイル編集は各ホスト独自の仕組みで行われるため、リポジトリへの
  書き込み権限のないモデルでも**レビューのみ**（読み取りとコメント）は可能ですが、**フルリビジョン**には
  編集可能なホストが必要です。研究誠実性の規則——*結果の数値を決して捏造しない*——はプロンプト自体に
  書かれているので、指示に従うどのモデルにも引き継がれます。
