# Research notebook rules

This is a small, searchable library of research, options, recommendations,
solutions, plot structures, transcripts, and creative work. Write concise
Markdown. Start with the finding, then give the evidence and open questions.

## Where work goes

- `raw/`: source captures and transcripts. Give each new `.md` file a title,
  one-sentence synopsis, and source link when available; keep the captured
  text verbatim. Never edit or delete an existing raw file.
- `notes/`: checked research and synthesis. One topic or question per page.
- `ideas/`: exploratory possibilities. Keep discarded ideas for context.
  Do not run automatic lint fixes or rewriting here.
- `drafts/`: human writing. Agents may make a separate review note but must
  not rewrite a draft without an explicit request.
- `index.md` files: short navigation links with one-line descriptions.
- `README.md`: exact copy of the root `index.md` for GitHub's repo page.
- `log.md`: dated, material changes only; newest date first.

Use subfolders when they help, such as `raw/transcripts/` or
`notes/plots/`.
Every new content file must end in `.md`. The GitHub workflow file is the
only non-Markdown repository file needed for validation.

## File names

- Use a stable, lowercase `kebab-case` topic name for ongoing pages:
  `notes/solar-battery-options.md`.
- Prefix `YYYY-MM-DD-` only when the date identifies an event or snapshot:
  `raw/transcripts/2026-09-29-expert-interview.md` or
  `notes/2026-09-29-solar-battery-prices.md`.
- Keep the human-readable title in frontmatter and other search terms in
  tags. Avoid renaming linked pages just to record a later update.

## Page format and vocabulary

Every new page in `notes/`, `ideas/`, or `drafts/` other than `index.md`
uses OKF v0.2-style frontmatter. Control files and raw captures are exempt.
The `description` is its one-sentence synopsis;
make it understandable without opening the page. Use this small shape:

```markdown
---
type: Research
title: A specific question or topic
description: One sentence with the main finding or current hypothesis.
tags: [topic, subtopic]
status: draft
---

# A specific question or topic

Short answer or current direction.

## Evidence

- Claim with a [direct source](https://example.com).

## Open questions

- What remains uncertain?
```

Types: `Research` (findings), `Comparison` (options and tradeoffs),
`Recommendation` (a preferred option and why), `Solution` (steps or design),
`Plot` (story structure), `Transcript` (edited or summarized dialogue),
`Exploration` (unfiltered ideas), `Draft` (human writing), and `Review`
(feedback on another page). Pick one primary type. Put actual source
transcripts in `raw/`; use `Transcript` for a processed account.

Tags are lowercase `kebab-case` topics, not status labels or folder names.
Use one to four useful tags; reuse an existing tag when it means the same
thing. Prefer `status: draft`, `stable`, or `deprecated` when status matters.
Keep claims separate from interpretations, and give direct Markdown links
for references. If a page has no external sources, say whether it is
original reasoning, fiction, or a question to investigate.

## Lightweight workflow

1. Capture a source in `raw/` if the source text matters. Link it from the
   related note and keep it unchanged.
2. Write the synopsis and answer first. Compare credible options when the
   question calls for a choice; note uncertainty and link references.
3. For brainstorming, collect distinct ideas in `ideas/`, then group and
   refine promising ones into `notes/`. Preserve rejected options briefly.
4. For consequential or contested findings, ask an independent reviewer
   to check evidence and alternatives. Put concrete objections in a linked
   `Review` note; resolve or record each one before calling a page stable.
   Parallel reviewers are optional and should have different questions.
5. Update the relevant `notes/`, `ideas/`, or `drafts/` index and add a short
   `log.md` entry for material findings, decisions, corrections, or
   deprecations. Keep old links working. Copy root `index.md` to `README.md`
   whenever it changes.

The human owner chooses final creative direction and recommendations.
Do not import a software delivery process or mandatory approval gates for
ordinary notebook entries.

## References

- [Open Knowledge Format v0.2 specification](https://github.com/GoogleCloudPlatform/open-knowledge-format/blob/main/SPEC.md)
- [Superpowers brainstorming and review source](https://github.com/obra/superpowers)
