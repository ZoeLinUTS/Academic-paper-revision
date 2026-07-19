# 学术论文修订 Skill

<!-- badges -->
[![stars](https://img.shields.io/github/stars/ZoeLinUTS/Academic-paper-revision?style=flat)](https://github.com/ZoeLinUTS/Academic-paper-revision/stargazers)
[![last commit](https://img.shields.io/github/last-commit/ZoeLinUTS/Academic-paper-revision)](https://github.com/ZoeLinUTS/Academic-paper-revision/commits)
[![license](https://img.shields.io/github/license/ZoeLinUTS/Academic-paper-revision)](../../LICENSE)
[![sponsor](https://img.shields.io/badge/sponsor-%E2%9D%A4-ff69b4)](https://github.com/sponsors/ZoeLinUTS)

<!-- i18n language switcher -->
[English](../../README.md) | **中文**
<!-- Add more languages here as translations land, e.g.:
     | [한국어](README.ko.md) | [日本語](README.ja.md) | ... -->

---

一个可复用的 **Claude skill**，把粗糙的、学生风格的研究论文草稿（LaTeX）改写成读起来像
在**特定目标会议**上被*录用*的投稿。它是*会议优先*的：先吃透目标会议的规则、研读该会议
已录用的论文，然后再改写——而不是套用那些对特定会议往往并不适用的通用「好文笔」建议。

> **状态：** skill 和一份合成示例草稿现在就可以直接使用。一个托管的、只读的 PDF 审阅器
> （Vercel / GitHub Pages）在[路线图](#路线图)中——见下文。

## 仓库内容

```
SKILL.md                            skill 本体（Claude 遵循的指令）
references/venue-conventions.md     会议惯例（ACL 系、LNCS/AJCAI、ML 会议）
docs/USER-GUIDE.md                  分步用法、两种模式、常见问题
docs/i18n/                          翻译版 README（上方的语言切换器）
examples/student-draft/             合成的字数不足草稿 + .bib + 参考答案
examples/over-length/               合成的超长草稿（用于练习删减）
```

## skill 做什么

- **会议优先的惯例。** 在动笔改写之前，先载入目标会议的规则（页数限制、必需章节、
  匿名要求、模板）。比如它知道 ACL 系的 Limitations/Ethics 是*免费的*（不计入页数限制），
  以及 LNCS 把 future work 并入 conclusion。
- **录用论文的语气。** 去掉 RQ/RO 脚手架，改用散文式的贡献陈述，使用陈述性名词短语标题，
  把相关工作按主题而非时间重新组织。
- **均衡的形式化。** 在方法形式化不足的地方，加入带编号的公式和 `algorithm` 浮动体，
  并为每个公式加上防误读的说明。
- **结构 / 交叉引用修复。** 接上未被引用的图，解决悬空的 `\ref`/`\cite`，补上缺失的
  导言区宏包。
- **页数适配。** 用保留内容的编辑（合并段落、把演示例子移到附录）把正文修剪到限制之内。
- **新颖性与定位。** 应要求，重建这条工作线的脉络，把先前工作按主题分组，点明差异点，
  并给出一份坦诚的、可能的审稿人攻击清单。

## 两种使用方式

1. **完整修订（编辑你的 LaTeX）。** 你给 Claude 可编译的源文件（`.tex` + `.bib` +
   类/样式文件 + `figures/`，或一个 Overleaf 导出的 zip），它会做外科手术式的编辑、
   抵消任何增加的篇幅，并交回一个重新打包好的上传 zip。**它绝不编造结果数字。**
2. **仅审阅（不编辑）。** Claude 读草稿，只返回*总览与评论*——研究主题/范围、核心贡献、
   坦诚的新颖性评估、问题、以及具体建议——不改动任何文件。这正是计划中的网页应用将暴露
   的模式。

两种模式都可以用**任意语言**工作：用中文、西班牙语、阿拉伯语或任何其他语言提问并接收
总览、评论和摘要。只有*对话*跟随你的语言——稿件本身仍保持会议要求的语言（通常是英语）。

详细走查见 [`docs/USER-GUIDE.md`](../USER-GUIDE.md)。

## 快速开始

在包含此 skill 的仓库中，用 Claude（例如 Claude Code）使用该 skill：

1. 把 Claude 指向你的论文源文件（或下面的示例），并说明你的**目标会议**。
2. 让它*「把这个改投 <会议>」*（完整模式）或*「只审阅这份草稿，给我总览和评论，
   什么都别改」*（仅审阅模式）。
3. Claude 先返回一份**定位简报**和一份**发现清单**，然后——在完整模式下——做出编辑
   并明确告诉你需要重新编译和核对哪些内容。

在附带的合成草稿上试试（两份都是**虚构的**——虚构的作者、数据集和数字，没有 IP 问题——
每份都带一个 `EXPECTED-FINDINGS.md` 参考答案）：

```
examples/student-draft/main.tex   字数不足；经典的学生草稿问题
examples/over-length/main.tex     超出页数限制；练习删减
```

- **`student-draft`** 预置了 RQ1–RQ3 脚手架、通用标题、按时间排列的相关工作、零公式，
  以及一个指向无 `\label` 的图的 `\ref`。它*低于*限制，因此用于练习增补免费章节。
- **`over-length`** 目标是 ACL 短文（4 页）限制，但跑到约 5–6 页；它其他方面已较打磨，
  因此用于练习**删减步骤**（合并冗余段落、把演示例子移到附录）。

### 一次运行长什么样

给它 `.tex` 源文件和一个会议，它先返回一份简报 + 发现清单：

```
定位简报
  它是什么   : 把标点保留为 token 的情感分类器。
  新颖性     : 目前写得偏薄——读起来像一次消融；需强化分析。
  完成度     : 有结果，无显著性检验，有一张图渲染成 "??"。

发现（按严重程度排序）
  [venue]         目标 = ACL (ARR)：正文 8 页，Limitations/Ethics 免费，双盲。
  [structure]     显式的 RQ1–RQ3 脚手架 → 改为散文式贡献。
  [formalization] 评分规则很核心却零公式 → 加 2–3 个公式。
  [rigor]         头条提升无显著性检验 / 单一划分 → 头号弱点。
  [bug]           \ref{fig:pipeline} 指向无 \label 的图 → 补上 label。
```

仅审阅模式到此为止；完整修订则接着编辑源文件并告诉你要重新编译什么。完整的
输入→输出走查见 [`docs/USER-GUIDE.md`](../USER-GUIDE.md)。

### 用于其他 AI 模型

此 skill 只是纯 Markdown 指令——只有 `SKILL.md` 顶部的 YAML front-matter 是 Claude
专用的。要在 **Codex、DeepSeek、Gemini、Cursor** 等中使用，把 `SKILL.md` 正文
（结尾 `---` 之后的全部内容）粘贴为系统提示，并把 `references/venue-conventions.md`
作为上下文附上。详见[用户手册](../USER-GUIDE.md#9-using-it-with-other-ai-models-codex-deepseek-gemini-)。

## 路线图

一个托管的**只读 PDF 审阅器**：用户上传一份 PDF 草稿，收到总览与评论——研究主题与范围、
它处理的问题、核心贡献、坦诚的新颖性评估、以及改进建议——**不做任何 LaTeX 编辑**。
计划做成一个小型网页前端（Vercel 或 GitHub Pages），架在上文所述的仅审阅模式之上。
这是同一仓库中追踪的一个独立任务；分析逻辑已经存在于 skill 中。

## 关于输入的说明

完整修订模式需要 **LaTeX 源文件**，而非 PDF——你无法对 PDF 做外科手术式的编辑，
而从 PDF 重建源文件会损坏作者的排版和数字。仅审阅模式可以从 PDF 工作，因为它只读取
和评论。
