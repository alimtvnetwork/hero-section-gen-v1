# Prompts — Reusable AI Process Library

> **Entry point.** Any AI given an image and asked to "read the prompt and follow
> this process" reads THIS file first, then follows the linked steps in order.

## What lives here

Reusable, project-agnostic prompts that turn a reference image into a
production-grade, AI-shippable spec, and then drive implementation in
predictable 10-item batches.

## Read order

1. `00-index.md` — this file.
2. `01-image-to-html.md` — how to translate a reference image into a faithful
   HTML/CSS/JS prototype (the visual ground truth).
3. `02-write-spec.md` — how to build the canonical `spec/<site>/<section>/`
   folder (file naming, numbering, source-of-truth rules).
4. `03-audit.md` — how to gap-analyze the spec against the image and assign
   P0 / P1 / P2 findings.
5. `04-next-10.md` — the "next 10" loop the user invokes repeatedly until
   the spec reaches 100/100.
6. `05-write-memory.md` — session close-out (mirrors `.lovable/prompts/01-write-memory.md`).

## Naming rules (apply to every spec this process produces)

- Top folder: `spec/NN-<site-slug>/` (e.g. `spec/01-rise-of-asia/`).
- Section folder: `spec/NN-<site>/NN-<section-slug>/`
  (e.g. `01-homepage-hero-section/`).
- All files lowercase-hyphen, prefixed with a sortable 2-digit number that
  matches the reading order in `00-index.md`.
- `00-index.md` is always the single entry point.
- Images keep their own `NN-<name>.png|webp|jpg` scheme; canonical image is
  `00-<section>-canonical.<ext>`.

## Trigger phrases (what the user will say)

| User says | AI does |
|---|---|
| "read the prompt and follow this process for `<image>`" | Run 01 → 02 → 03 from a clean state, output initial spec + audit. |
| "next 10" | Execute the next 10 outstanding items in `45-priorities.md` (or equivalent) per `04-next-10.md`. |
| "gap analysis" | Re-run `03-audit.md` against the current spec + canonical image. |
| "write memory" / "end memory" | Run `05-write-memory.md`. |

## Hand-off contract

Each prompt file ends with a **Deliverables** checklist. The next prompt in
the chain assumes those deliverables exist. If any are missing, stop and
complete the previous step before continuing.

## Changelog

- v1 (2026-06-02) — initial extraction from the hero-section workflow.