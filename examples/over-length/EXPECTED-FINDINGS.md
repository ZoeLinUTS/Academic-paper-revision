# Expected findings — answer key for the over-length sample

Use this to grade a run on `main.tex` (target: an **ACL-family short paper**,
4-page body limit, excluding References / Limitations / Ethics). This sample's
purpose is to exercise the skill's **page-fit / trimming step (Step 4)**: it runs
~5-6 pages and must be trimmed to 4 without losing content.

## Primary: page-fit / trimming (the point of this sample)

- `[page-fit]` Draft is ~1.5-2 pages over the 4-page short-paper limit → must
  trim the **body** (References/Limitations are free) using content-preserving
  edits, not by deleting results.
- `[trim]` Two overlapping **Introduction** paragraphs make the same motivation
  twice → merge into one.
- `[trim]` **Related Work** repeats the "accurate-but-opaque vs. transparent-but-
  shallow" contrast three times → tighten to two short paragraphs.
- `[trim]` The long inline **worked example** in Method belongs in an appendix →
  move it out, leave a one-sentence pointer in the body. (Appendix is page-free.)
- `[trim]` Two **Discussion** paragraphs restate the same result → merge.
- `[tone]` Hedgy filler sentences throughout → cut.

## Secondary (structure / build)

- `[venue]` No **Limitations** section — ACL-family requires it and it is
  page-free; adding it does *not* cost body space. *(edit)*
- `[refs]` `references.bib` provided but the draft hard-codes an informal
  `thebibliography` and never `\cite`-s → wire `\cite` + `\bibliography`. *(edit)*
- `[claims]` Table omits confidence intervals ("intervals omitted for space") →
  once trimmed there is room; at least note variance.

## What a good run demonstrates

Unlike the student-draft sample, this draft is otherwise fairly polished (prose
contributions, one equation `eq:score`, a real baseline comparison). The skill
should therefore spend its effort on **trimming to fit** while preserving every
result — the inverse of the student-draft sample, which was *under* length.

## Note on page count

Page count is a **render-time** property. Confirm the overflow (and that trims
bring it under 4 pages) by compiling; do not assume from the source alone.
