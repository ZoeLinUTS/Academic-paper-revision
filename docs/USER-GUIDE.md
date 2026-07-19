# User Guide

How to use the `academic-paper-revision` skill, what to give it, what it returns,
and how the two operating modes differ.

## 1. Before you start: what to have ready

| Mode | Input you provide | What Claude does |
| --- | --- | --- |
| **Full revision** | The **LaTeX source**: main `.tex`, `references.bib`, the venue class/style (`llncs.cls` + `*.bst`, or `acl.sty`, …), and `figures/`. An Overleaf **project export (zip)** is ideal. | Makes surgical edits to the source, offsets added length, hands back a rebuilt upload zip. |
| **Review-only** | A **PDF** (or the source) is enough. | Reads and comments only — **no files are changed**. |

The full-revision mode needs source because you cannot surgically edit a PDF, and
reconstructing source from a PDF risks losing your formatting and numbers. A
rendered `main.pdf` is a useful *secondary* input for checking render-time
properties (page count, float placement) but is never sufficient on its own.

## 2. Tell Claude your target venue

This is load-bearing. The same edit can be correct for one venue and a
desk-reject for another. Name the venue explicitly (ACL, NAACL, EMNLP, AJCAI,
NeurIPS, …). If the venue's exact rules aren't known, Claude will look up the
current call-for-papers before editing. If a filename says one venue but your
notes say another, it will surface the discrepancy and ask — it never assumes.

## 3. Pick a mode and ask

**Full revision** — example prompts:
- "Revise this for EMNLP so it reads like an accepted paper."
- "Trim this to the AJCAI page limit and remove the RQ scaffolding."
- "Add an equation/algorithm for the method and fix the cross-references."

**Review-only** — example prompts:
- "Just review this draft — give me an overview and comments, don't edit anything."
- "Evaluate the novelty and tell me the likely reviewer objections."
- "What's the scope and the main problems here? How is it different from prior work?"

### Communication language — any language

You can work in **any language** and get your results in it. Claude will ask (or
infer) which language you want the *conversation* in — Chinese, Spanish, Arabic,
or anything else — and delivers every overview, comment, finding, and summary in
that language. This is independent of the manuscript: **the paper stays in
English** (or the venue's required language); only the conversation follows your
chosen language.

## 4. What the output looks like

Both modes start with the same reviewer-style memo:

1. **Orientation briefing** — *What the draft is* · *Core contribution* ·
   *Novelty (candid, with reviewer-attack bullets)* · *State of completeness*.
   Ends by confirming the venue and asking what scope you want.
2. **Findings list** — one line per issue, grouped and severity-ordered, each
   naming the fix. Example rows:
   - `[venue]` Target = ACL (ARR): 8-page body, Limitations/Ethics free, double-blind.
   - `[structure]` Explicit RQ1–RQ3 scaffolding → convert to prose contributions.
   - `[formalization]` Zero equations though the scoring rule is central → add 2–3 eqs.
   - `[rigor]` Headline gain has no significance test / single split → flag as top weakness.
   - `[bug]` `\ref{fig:pipeline}` targets a figure with no `\label` → add the label.
   - `[cross-ref]` Related work is a dated list → regroup thematically.
   - `[page-fit]` +0.5 page over → paired with the exact `[trim]` moves that offset it
     (merge redundant paragraphs, move a worked example to an appendix).

**Review-only stops here** (plus concrete suggestions and one honest caveat). No
files are touched.

**Full revision continues:**

3. **Change summary** (after editing) — what changed and *why*, in order: venue
   conventions applied · framing/structure · formalization added · figures ·
   build/cross-ref fixes · page-fit. Then **what you must do**: recompile (e.g. on
   Overleaf), verify the named render-time items (page count, a specific
   glyph/float), and the next concrete lever if a check fails. It flags anything it
   could not verify locally and any unconfirmed venue rule.
4. **One honest caveat** — the single biggest reviewer risk, stated plainly.

## 4b. A worked example (input → output)

Concretely, here is what a run on the bundled `examples/student-draft/` looks like.

**Expected input** — the LaTeX source (not a PDF), plus a named venue:

```
examples/student-draft/
  main.tex          # article-class draft: RQ1–RQ3, generic headings,
                    #   zero equations, \ref{fig:pipeline} to an unlabeled figure
  references.bib    # provided but never \cite-d
```

> Prompt: *"Review this for **ACL (ARR)**, then revise it to read like an accepted paper."*

**Sample output — the briefing + findings list Claude returns first:**

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

**Review-only** stops there (plus a couple of concrete suggestions and one honest
caveat). **Full revision** then makes the edits and returns a change summary telling
you exactly what to recompile and verify. Grade any run against the sample's
`EXPECTED-FINDINGS.md`.

## 5. The Overleaf round-trip (full mode)

1. In Overleaf: **Menu → Download → Source** (a zip).
2. Give Claude the zip (or the unpacked source).
3. Claude edits the source and rebuilds an upload zip containing only the build
   inputs (`.tex`, `.bib`, class/style, `figures/`) — it excludes `main.pdf`,
   READMEs, and git files.
4. Re-upload the zip to Overleaf and recompile.
5. Verify the render-time items Claude named (page count is a render-time
   property, so it can only be confirmed after compiling).

## 6. Research-integrity guarantees

- **Never invents numbers.** Result placeholders are filled only from real,
  saved runs you provide. If results don't exist yet, they stay as placeholders.
- **Preserves your technical content.** Edits are surgical; the intellectual
  content stays, only the presentation changes.
- **No unsolicited shared actions.** It won't commit, push, or send anything
  without you asking.

## 7. Try it on the samples

Two synthetic drafts are included (fictional authors, datasets, numbers — no IP),
each with an `EXPECTED-FINDINGS.md` answer key you can grade a run against:

- **`examples/student-draft/main.tex`** — *under* the page limit, seeded with the
  classic problems: RQ1–RQ3 scaffolding, generic headings ("Approach",
  "Experiment 1"), a chronological related-work section, zero equations though the
  scoring rule is central, and a `\ref{fig:pipeline}` whose figure has no `\label`
  (renders `??`). Also has no significance test — the `[rigor]` finding.
- **`examples/over-length/main.tex`** — targets an ACL short-paper (4-page) limit
  but runs ~5-6 pages. It is otherwise polished, so it exercises the **trimming
  step**: merging two redundant intro paragraphs, tightening a repetitive related
  work, and moving a long worked example to an appendix.

Run either mode on either draft to see the briefing, the findings list, and — in
full mode — the edits.

## 8. FAQ

**Can I give it only a PDF?** For review-only, yes. For full revision, no — ask
Claude and it will request the LaTeX source.

**Will it translate my paper into my chat language?** No. The manuscript stays in
the venue's language; only the conversation follows your chosen language.

**Does it work for non-ACL venues?** Yes. `references/venue-conventions.md`
covers ACL-family, Springer/LNCS/AJCAI, and ML conferences; for anything else
Claude reads the current CFP first and records what it learns.

## 9. Using it with other AI models (Codex, DeepSeek, Gemini, …)

The skill is just **model-agnostic Markdown instructions** — only the YAML
front-matter at the top of `SKILL.md` (the `name:`/`description:` block between the
`---` lines) is Claude-specific auto-loading metadata. Everything below it is a plain
prompt any capable model can follow. To use it elsewhere:

1. **Copy the body of `SKILL.md`** — everything *after* the closing `---` — into the
   other tool's **system prompt** (or paste it as the first message). You can drop the
   YAML front-matter; it only matters for Claude's skill auto-discovery.
2. **Attach the reference file** `references/venue-conventions.md` as context (paste it,
   or add it to the tool's file/context panel) so the model has the venue rules.
3. **Give it the paper the same way** — the `.tex` source for full revision, or a PDF
   for review-only — and name the target venue.

Notes per tool:

- **OpenAI Codex / ChatGPT, DeepSeek, Gemini, local models:** paste the `SKILL.md` body
  as the system/developer prompt and the venue-conventions file as context, then proceed
  as above. No format changes are needed — it is all prose instructions.
- **Cursor / Windsurf / Continue and similar IDE agents:** put the `SKILL.md` body in the
  project rules file the tool reads (e.g. `.cursorrules`, `AGENTS.md`) so it loads
  automatically, and keep `references/` in the repo.
- **What may differ:** tool-calling and file-editing happen through each host's own
  mechanism, so a model without repo write-access can still do **review-only** (it just
  reads and comments) but needs an editing-capable harness for **full revision**. The
  research-integrity rule — *never invent result numbers* — is stated in the prompt itself,
  so it carries over to any model that follows the instructions.
