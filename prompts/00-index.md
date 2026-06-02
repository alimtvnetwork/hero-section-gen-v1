# Prompts — Reusable AI Process Library

> **Entry point.** Any AI given an image and asked to "read the prompt and follow
> this process" reads THIS file first, then follows the linked steps in order.

## What lives here

Reusable, project-agnostic prompts that turn one or more reference images
into a production-grade, AI-shippable spec, and then drive implementation
in predictable 10-item batches the user can drive with a single keyword
(`next`).

## The full loop (TL;DR for humans)

```
 user gives 1..N images
        │
        ▼
 ┌──────────────────────────────────────────────┐
 │ STEP 1 — prompts/01-image-to-html.md         │
 │   AI saves canonical image(s), builds the    │
 │   anatomy table, extracts tokens, writes a   │
 │   self-contained HTML/CSS/JS prototype.      │
 └──────────────────────────────────────────────┘
        │
        ▼
 ┌──────────────────────────────────────────────┐
 │ STEP 2 — prompts/02-write-spec.md            │
 │   AI scaffolds spec/<NN-site>/<NN-section>/  │
 │   with the full lowercase-hyphen file tree   │
 │   (00-index.md … 56-lighthouse-ci.md).       │
 └──────────────────────────────────────────────┘
        │
        ▼
 ┌──────────────────────────────────────────────┐
 │ STEP 3 — prompts/03-audit.md                 │
 │   AI gap-analyses image vs spec, scores      │
 │   /100, writes 20-gap-analysis.md and        │
 │   populates 45-priorities.md with P0/P1/P2.  │
 └──────────────────────────────────────────────┘
        │
        ▼
 ┌──────────────────────────────────────────────┐
 │ STEP 4 — prompts/04-next-10.md  (LOOPED)     │
 │   User types "next" (or "next 10").          │
 │   AI executes 10 items, reports remaining,   │
 │   loop until 45-priorities.md is empty.      │
 └──────────────────────────────────────────────┘
        │
        ▼
 ┌──────────────────────────────────────────────┐
 │ STEP 5 — prompts/05-write-memory.md          │
 │   User types "write memory" to persist       │
 │   session state for the next AI.             │
 └──────────────────────────────────────────────┘
```

**Hard rule:** Steps 1 → 2 → 3 always run together when the user gives an
image. The AI MUST finish all three before stopping, because Step 3 is what
produces `45-priorities.md` — the queue that the `next` keyword consumes.
If the AI stops after Step 1 or 2, the user has nothing to drive.

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
7. `06-color-and-typography.md` — **MANDATORY** color (oklch + hex + rgb + hsl)
   and typography extraction rules. Includes the project-wide text-shadow
   token `--shadow-text-default: rgb(0 0 0) 1px 0.7px 0px`.
8. `07-text-shadow.md` — canonical `--shadow-text-default` token + apply/opt-out matrix.
9. `08-color-format-rule.md` — 4-format color contract (oklch + hex + rgb + hsl).

> Color, text-shadow, and typography rules live ONLY in `prompts/` — not in
> `mem/`. Any AI starting a new session reads these files directly from
> `prompts/` instead of expecting a memory mirror.

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
| "here is an image, follow the prompt" (or attaches 1..N images) | Run 01 → 02 → 03 end-to-end. Output initial spec + audit + `45-priorities.md`. Stop only after Step 3. |
| "next" / "next 10" / "next 10 with reasoning and time" | Execute the next 10 outstanding items in `45-priorities.md` per `04-next-10.md`. End reply with `Remaining items: <N>`. |
| "gap analysis" / "re-audit" | Re-run `03-audit.md` against the current spec + canonical image. Rewrite `20-gap-analysis.md` and refresh `45-priorities.md`. |
| "add this observation" / new image | Re-run Step 1 partially (anatomy + tokens diff), then Step 3 to refresh priorities. |
| "write memory" / "end memory" | Run `05-write-memory.md`. |

## Multiple images

If the user provides several images for the same section (e.g. desktop +
mobile, or "before / after" iterations):

1. Save each as `NN-<section>-<variant>.<ext>` — the first is
   `00-<section>-canonical.<ext>`, the rest are `01..`, `02..` etc.
2. The canonical (`00`) is the source of truth. Others are reference-only
   and must be listed in `19-archived.md` with a one-line rationale.
3. Anatomy table includes a `responsive-variant` column if the variants
   represent different breakpoints.

## Hand-off contract

Each prompt file ends with a **Deliverables** checklist. The next prompt in
the chain assumes those deliverables exist. If any are missing, stop and
complete the previous step before continuing.

## Changelog

- v1 (2026-06-02) — initial extraction from the hero-section workflow.
- v2 (2026-06-02) — added explicit 5-step loop diagram, multi-image rules,
  and clarified that Steps 1-3 always run together so `next` has a queue.