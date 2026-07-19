# Expected findings — answer key for the sample draft

Use this to grade a run of the skill on `main.tex` (target: a generic ACL-family
conference). A good review-only run should surface most of these; a full-revision
run should also fix the ones marked *(edit)*.

## Must-catch (high severity)

- `[bug]` `\ref{fig:pipeline}` (Intro) points at a figure with **no `\label`** →
  renders `??`. *(edit: add `\label{fig:pipeline}`)*
- `[rigor]` Headline ~2-point gains have **no significance test, no variance/CI,
  a single 80/20 split**, and the **threshold is tuned on the training set**. The
  central claim is within noise as written — top reviewer risk.
- `[venue]` **No Limitations section** — ACL-family requires it and it is
  page-free. *(edit: add Limitations + a short data/ethics statement)*

## Should-catch (structure & tone)

- `[structure]` RQ1–RQ3 scaffolding → prose contributions/claims. *(edit)*
- `[structure]` Generic headings "Approach / Experiment 1 / Experiment 2" →
  declarative noun phrases. *(edit)*
- `[related-work]` Chronological dated list ("In 2013… In 2014…") → thematic
  paragraphs, each ending in how this paper differs. *(edit)*
- `[formalization]` Zero equations though the score/threshold rule is the method's
  core → add a numbered feature/score/decision equation. *(edit)*
- `[tone]` Hedgy abstract/intro ("very important", "a lot of people", "seems to
  help a little bit") → precise, declarative rewrite. *(edit)*
- `[refs]` `references.bib` exists but the draft hard-codes an informal
  `thebibliography` and never `\cite`-s anything → wire `\cite` +
  `\bibliography{references}`. *(edit)*

## Nice-to-catch (rigor detail)

- Missing dev split (tuning done on train).
- No dataset provenance / license / size table ("reviews we collected").
- Only bag-of-words; no TF-IDF or pretrained baseline for reference.

## Known limitation of this sample

This draft is **under** a typical ACL page limit, so it does **not** exercise the
skill's trimming step (Step 4). Here "page-fit" inverts to *adding* free sections
(Limitations). A separate over-length sample would be needed to test trimming.
