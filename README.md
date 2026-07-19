# Academic Paper Revision Skill

<!-- badges -->
[![stars](https://img.shields.io/github/stars/ZoeLinUTS/Academic-paper-revision?style=flat)](https://github.com/ZoeLinUTS/Academic-paper-revision/stargazers)
[![last commit](https://img.shields.io/github/last-commit/ZoeLinUTS/Academic-paper-revision)](https://github.com/ZoeLinUTS/Academic-paper-revision/commits)
[![license](https://img.shields.io/github/license/ZoeLinUTS/Academic-paper-revision)](LICENSE)
[![sponsor](https://img.shields.io/badge/sponsor-%E2%9D%A4-ff69b4)](https://github.com/sponsors/ZoeLinUTS)

<!-- i18n language switcher -->
**English** | [中文](docs/i18n/README.zh-CN.md) | [日本語](docs/i18n/README.ja.md) | [한국어](docs/i18n/README.ko.md) | [Español](docs/i18n/README.es.md) | [Français](docs/i18n/README.fr.md) | [Deutsch](docs/i18n/README.de.md) | [Português](docs/i18n/README.pt.md)
<!-- Translations are community-maintained; English is the source of truth and may be ahead. -->
<!-- To add a language: create docs/i18n/README.<lang>.md and append it to every switcher row. -->

---

A reusable **Claude skill** that turns a rough or student-style research-paper
draft (LaTeX) into a submission that reads like an *accepted* paper at a
**specific target venue**. It is *venue-first*: it nails the target's rules,
studies the venue's accepted papers, then rewrites — instead of applying generic
"good writing" advice that is often wrong for a given venue.

> **Status:** the skill and a synthetic sample draft are ready to use today. A
> hosted, read-only PDF reviewer (Vercel / GitHub Pages) is on the
> [roadmap](#roadmap) — see below.

## What's in this repo

```
SKILL.md                            The skill itself (instructions Claude follows)
references/venue-conventions.md     Venue conventions (ACL-family, LNCS/AJCAI, ML confs)
docs/USER-GUIDE.md                  Step-by-step usage, the two modes, FAQ
docs/i18n/                          Translated READMEs (language switcher above)
examples/student-draft/             Synthetic under-length draft + .bib + answer key
examples/over-length/               Synthetic over-length draft (exercises trimming)
```

## What the skill does

- **Venue-first conventions.** Loads the target venue's rules (page limit,
  mandatory sections, anonymity, template) before touching prose. Knows, e.g.,
  that ACL-family Limitations/Ethics are *free* (don't count toward the limit)
  and that LNCS folds future work into the conclusion.
- **Accepted-paper tone.** Removes RQ/RO scaffolding in favour of prose
  contributions, uses declarative noun-phrase headings, and reorganizes related
  work by theme instead of by date.
- **Balanced formalization.** Adds numbered equations and `algorithm` floats
  where the method is under-formalized, and guards each formula against
  misreading.
- **Structural/cross-reference fixes.** Wires in unreferenced figures, resolves
  dangling `\ref`/`\cite`, adds missing preamble packages.
- **Page-fit.** Trims the main body to the limit using content-preserving edits
  (merging paragraphs, moving worked examples to an appendix).
- **Novelty & positioning.** On request, reconstructs the line of work's
  lineage, groups prior work thematically, names the differentiator, and gives a
  candid list of likely reviewer attacks.

## Two ways to use it

1. **Full revision (edits your LaTeX).** You give Claude the compilable source
   (`.tex` + `.bib` + class/style + `figures/`, or an Overleaf export zip) and it
   makes surgical edits, offsets any added length, and hands back a rebuilt
   upload zip. **It never invents result numbers.**
2. **Review-only (no edits).** Claude reads the draft and returns an *overview
   and comments only* — research topic/scope, core contribution, candid novelty,
   problems, and concrete suggestions — without changing any files. This is the
   mode the planned web app will expose.

Either mode works in **any language** you like: ask your questions and receive the
overview, comments, and summaries in Chinese, Spanish, Arabic, or any other language.
Only the *conversation* follows your language — the manuscript itself stays in the
venue's required language (usually English).

See [`docs/USER-GUIDE.md`](docs/USER-GUIDE.md) for the detailed walkthrough.

## Quick start

Using the skill with Claude (e.g. Claude Code) in a repo that contains this
skill:

1. Point Claude at your paper source (or the sample below) and name your
   **target venue**.
2. Ask it to *"revise this for <venue>"* (full mode) or *"just review this draft
   and give me an overview and comments"* (review-only mode).
3. Claude first returns an **orientation briefing** and a **findings list**, then
   — in full mode — makes the edits and tells you exactly what to recompile and
   verify.

Try it on the included synthetic drafts (both are **made-up** — fictional
authors, datasets, and numbers, no IP — each with an `EXPECTED-FINDINGS.md`
answer key):

```
examples/student-draft/main.tex   under-length; classic student-draft problems
examples/over-length/main.tex     over the page limit; exercises trimming
```

- **`student-draft`** is seeded with RQ1–RQ3 scaffolding, generic headings, a
  dated-list related work, zero equations, and a `\ref` to a figure with no
  `\label`. It is *under* the limit, so it exercises adding free sections.
- **`over-length`** targets an ACL short-paper (4-page) limit but runs ~5-6
  pages; it is otherwise polished, so it exercises the **trimming step**
  (merging redundant paragraphs, moving a worked example to an appendix).

### What a run looks like

Give it the `.tex` source and a venue; it first returns a briefing + findings list:

```
Orientation briefing
  What it is   : sentiment classifier that keeps punctuation as tokens.
  Novelty      : thin as written — reads as one ablation; sharpen the analysis.
  Completeness : results present, no significance test, one figure renders "??".

Findings (severity-ordered)
  [venue]         Target = ACL (ARR): 8-page body, Limitations/Ethics free, double-blind.
  [structure]     Explicit RQ1–RQ3 scaffolding → convert to prose contributions.
  [formalization] Zero equations though the scoring rule is central → add 2–3 eqs.
  [rigor]         Headline gain has no significance test / single split → top weakness.
  [bug]           \ref{fig:pipeline} targets a figure with no \label → add the label.
```

Review-only stops there; full revision then edits the source and tells you what to
recompile. See [`docs/USER-GUIDE.md`](docs/USER-GUIDE.md) for a full input→output
walkthrough.

### Use it with other AI models

The skill is plain Markdown instructions — only the YAML front-matter at the top of
`SKILL.md` is Claude-specific. To use it with **Codex, DeepSeek, Gemini, Cursor**, etc.,
paste the `SKILL.md` body (everything after the closing `---`) as the system prompt and
attach `references/venue-conventions.md` as context. Details in
[the guide](docs/USER-GUIDE.md#9-using-it-with-other-ai-models-codex-deepseek-gemini-).

## Roadmap

A hosted **read-only PDF reviewer**: the user uploads a PDF draft and receives an
overview and comments — research topic and scope, the problems it tackles, the
core contribution, a candid novelty read, and improvement suggestions — with
**no LaTeX editing**. Planned as a small web front-end (Vercel or GitHub Pages)
over the review-only mode described above. This is a separate task tracked in the
same repo; the analysis logic already lives in the skill.

## A note on inputs

The full-revision mode needs the **LaTeX source**, not a PDF — you cannot make
surgical edits to a PDF, and reconstructing source risks the author's formatting
and numbers. The review-only mode can work from a PDF because it only reads and
comments.
