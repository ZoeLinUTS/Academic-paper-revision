# 学術論文リビジョン Skill

<!-- badges -->
[![stars](https://img.shields.io/github/stars/ZoeLinUTS/Academic-paper-revision?style=flat)](https://github.com/ZoeLinUTS/Academic-paper-revision/stargazers)
[![last commit](https://img.shields.io/github/last-commit/ZoeLinUTS/Academic-paper-revision)](https://github.com/ZoeLinUTS/Academic-paper-revision/commits)
[![license](https://img.shields.io/github/license/ZoeLinUTS/Academic-paper-revision)](../../LICENSE)
[![sponsor](https://img.shields.io/badge/sponsor-%E2%9D%A4-ff69b4)](https://github.com/sponsors/ZoeLinUTS)

<!-- i18n language switcher -->
[English](../../README.md) | [中文](README.zh-CN.md) | **日本語** | [한국어](README.ko.md) | [Español](README.es.md) | [Français](README.fr.md) | [Deutsch](README.de.md) | [Português](README.pt.md)
<!-- 翻訳はコミュニティで維持されます。英語が正であり、先行する場合があります。 -->

---

粗削りな、あるいは学生風の研究論文ドラフト（LaTeX）を、**特定の投稿先**で*採択された*論文
のように読める原稿へと変える、再利用可能な **Claude skill** です。これは*会議優先*です。
まず投稿先の規則を押さえ、その会議の採択論文を研究し、それから書き換えます。特定の会議には
しばしば当てはまらない汎用的な「良い文章」の助言を当てはめることはしません。

## ✅ 今すぐオンラインで試せます — セットアップ不要、API キーも不要

この skill の無料ホスト版（**レビューのみ**モード）が
**[zoelin.dev/tools/paper-revision](https://zoelin.dev/tools/paper-revision)** で動いています。
PDF または LaTeX ソースをアップロードし、投稿先を選ぶだけで、ブラウザ上でオリエンテーション・
ブリーフィングと重要度順の所見リストがすぐに得られます——無料のホスト型モデルが同梱されており、
より高品質な出力が欲しい場合は自分の API キーを使うこともできます。ファイルの編集や保存は
一切行いません。**フルリビジョン**モード（Claude が実際に `.tex` を書き換える）を使うには、
下記の説明に従って Claude Code で直接この skill を使ってください。

## このリポジトリの内容

```
SKILL.md                            skill 本体（Claude が従う指示）
references/venue-conventions.md     会議の慣習（ACL 系、LNCS/AJCAI、ML 会議）
docs/USER-GUIDE.md                  ステップごとの使い方、2 つのモード、FAQ
docs/i18n/                          翻訳版 README（上部の言語スイッチャー）
examples/student-draft/             合成の分量不足ドラフト + .bib + 解答例
examples/over-length/               合成の分量超過ドラフト（削減の練習用）
```

## skill が行うこと

- **会議優先の慣習。** 文章に手を付ける前に、投稿先の規則（ページ制限、必須セクション、
  匿名性、テンプレート）を読み込みます。例えば ACL 系の Limitations/Ethics は*無料*
  （制限に数えない）、LNCS は future work を conclusion に畳み込む、といったことを把握します。
- **採択論文のトーン。** RQ/RO の足場を外し、散文形式の貢献に置き換え、宣言的な名詞句の
  見出しを用い、関連研究を時系列ではなくテーマ別に再編成します。
- **バランスの取れた形式化。** 手法の形式化が不足している箇所に、番号付き数式や `algorithm`
  フロートを追加し、各式には誤読を防ぐ説明を添えます。
- **構造・相互参照の修正。** 参照されていない図を接続し、宙に浮いた `\ref`/`\cite` を解決し、
  不足しているプリアンブルのパッケージを補います。
- **ページ調整。** 内容を保持する編集（段落の統合、ワークト例の付録への移動）で本文を制限内に
  収めます。
- **新規性と位置づけ。** 要望に応じて、その研究系譜を再構成し、先行研究をテーマ別に分類し、
  差別化点を明示し、想定される査読者の攻撃点を率直に列挙します。

## 2 つの使い方

1. **フルリビジョン（あなたの LaTeX を編集）。** コンパイル可能なソース（`.tex` + `.bib` +
   クラス/スタイルファイル + `figures/`、または Overleaf エクスポートの zip）を渡すと、外科的
   な編集を行い、増えた分量を相殺し、再構築したアップロード用 zip を返します。**結果の数値を
   決して捏造しません。**
2. **レビューのみ（編集なし）。** Claude はドラフトを読み、*概観とコメントのみ*を返します——
   研究テーマ/範囲、中核的貢献、率直な新規性評価、問題点、具体的な提案——ファイルは一切
   変更しません。これは[ホスト型 Web アプリ](https://zoelin.dev/tools/paper-revision)が
   公開するモードです。

どちらのモードも**任意の言語**で使えます。中国語、スペイン語、アラビア語その他どの言語でも
質問し、概観・コメント・要約を受け取れます。*会話*だけがあなたの言語に従い、原稿自体は会議
が要求する言語（通常は英語）のままです。

詳細なウォークスルーは [`docs/USER-GUIDE.md`](../USER-GUIDE.md) を参照してください。

## クイックスタート

この skill を含むリポジトリで、Claude（例：Claude Code）から使う場合：

1. Claude に論文ソース（または下のサンプル）と**投稿先**を示します。
2. *「これを <会議> 向けにリビジョンして」*（フルモード）または*「このドラフトをレビューして、
   概観とコメントだけちょうだい、何も編集しないで」*（レビューのみモード）と頼みます。
3. Claude はまず**オリエンテーション・ブリーフィング**と**所見リスト**を返し、次に——フルモード
   では——編集を行い、再コンパイルして確認すべき項目を正確に伝えます。

同梱の合成ドラフトで試せます（どちらも**架空**——架空の著者・データセット・数値、IP 問題なし
——それぞれに `EXPECTED-FINDINGS.md` の解答例付き）：

```
examples/student-draft/main.tex   分量不足；典型的な学生ドラフトの問題
examples/over-length/main.tex     ページ制限超過；削減の練習
```

- **`student-draft`** は RQ1–RQ3 の足場、汎用的な見出し、時系列の関連研究、数式ゼロ、`\label`
  のない図への `\ref` を仕込んであります。制限*未満*なので、無料セクションの追加を練習します。
- **`over-length`** は ACL ショートペーパー（4 ページ）制限が対象ですが約 5〜6 ページあります。
  他は仕上がっているので、**削減ステップ**（冗長な段落の統合、ワークト例の付録移動）を練習します。

## 入力についての注意

フルリビジョンには **PDF ではなく LaTeX ソース**が必要です。PDF を外科的に編集することはできず、
PDF からソースを再構築すると著者の書式や数値が損なわれる恐れがあるためです。レビューのみモードは
読み取ってコメントするだけなので PDF で動作します。
