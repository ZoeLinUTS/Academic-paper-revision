# Venue conventions

Load the family that matches the target venue. When a venue is missing, look up its
current call-for-papers and add a section here. Rules change year to year — verify
page limits and mandatory sections against the *current* CFP before relying on them.

## ACL family (ACL, NAACL, EMNLP; via ARR)

- **Page limit (long paper):** 8 pages of main content. Short papers: 4.
- **Free (do NOT count toward the limit):** Limitations, Ethics/Broader Impacts,
  References, and Appendix. Camera-ready typically adds one page.
- **Limitations: MANDATORY.** Since ACL 2023, a paper without a Limitations section is
  desk-rejected. It is unnumbered, after the conclusion, before references. Never trim or
  drop it to save space — it is page-free anyway.
- **Ethics / Broader Impacts:** recommended, also page-free. Keep it unless the paper is
  purely theoretical with no data/subjects; treat it as free insurance, not a luxury.
- **Anonymity:** ARR is double-blind. No author names, no deanonymizing self-references,
  and be careful about linking a public repo under a real-name account during review —
  prefer a private repo or anonymized artifact.
- **Style:** prose contributions (no RQ1/RO2 labels), declarative noun-phrase section
  headings, thematically organized related work, method sections that freely use numbered
  equations and `algorithm` floats. Result subheadings often state the finding.
- **Template:** `\documentclass[11pt]{article}` + `\usepackage[review]{acl}` (two-column,
  line numbers in review mode).

Consequence: the instinct to "cut the Limitations and Ethics sections to fit" is correct
for Springer/LNCS but **wrong** for ACL — there they are mandatory/recommended and free.

## Springer LNCS / AJCAI / many CS conferences

- Page limit counts (roughly) everything including references; check the specific CFP.
- **No mandatory Limitations section.** Future work is usually a brief paragraph folded
  into the Conclusion, not a standalone required section.
- Ethics statements are typically optional and short.
- Template: `llncs` document class; often single-column.

Consequence: the "brief future-work-in-conclusion" style is the LNCS/AJCAI convention,
NOT the ACL convention. Do not port it to an ACL submission.

## ML conferences (NeurIPS, ICML, ICLR)

- Page limits count main content; appendices/refs handled per-venue (often unlimited
  appendix in supplementary). Verify per CFP.
- Broader-impact/limitations expectations vary by year — check the current CFP.
- Checklists (e.g. NeurIPS reproducibility/paper checklist) are often mandatory.

## Preferred writing style (venue-agnostic)

The accepted-paper voice to imitate, regardless of venue:

- **Prose-first, no RQ/RO scaffolding.** Contributions and questions are stated as
  declarative claims in prose, never as "RQ1/RO2/O3" machinery.
- **Confident, structured framing.** Say plainly what is known vs. open; declarative
  noun-phrase headings that name the idea, not the slot ("Falsification-first reasoning",
  not "Approach").
- **Thematic synthesis over chronology.** Related work is grouped by theme, one claim per
  paragraph, each ending in how the paper differs — not a dated list.
- **Well-organized supporting structure.** Strong taxonomies/tables, numbered equations
  for the estimand/method, and `algorithm` floats for deterministic procedures, kept
  balanced so no single component is over- or under-formalized.

## Quick decision aid

- Target is ACL/NAACL/EMNLP → keep Limitations (mandatory) + Ethics; 8-page body;
  prose contributions; equations + algorithms welcome; watch double-blind anonymity.
- Target is AJCAI/LNCS → future work in conclusion; no mandatory Limitations; check if
  single-column.
- Unsure → read the current CFP before editing, and record it here.
