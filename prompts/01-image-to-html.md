# 01 — Image → HTML/CSS/JS Prototype

**Goal:** Convert a single reference image into a faithful static prototype
that becomes the visual ground truth for the spec.

## Inputs

- One canonical image (`.png` / `.webp` / `.jpg`) provided by the user.
- Optional: user observations / corrections.

## Process

1. **Save the image** to `spec/<site>/<section>/00-<section>-canonical.<ext>`.
   Never overwrite — if a canonical already exists, archive the old one as
   `01-<section>-variant-archived.<ext>` and add it to `19-archived.md`.
2. **Identify the section type** (hero, pricing, testimonials, footer…) and
   pick a layout taxonomy from `spec/<site>/<section>/05-<section>-anatomy.md`
   (create if missing).
3. **Map every visible element** in a numbered anatomy table:
   `# · element · role · token · spacing · interaction · a11y note`.
   No element may be missing or skipped (use `N/A` only when truly absent).
4. **Extract tokens** — colors in `oklch`, spacing on a 4px grid, type pair,
   motion durations/easings — into `02-tokens.md`. No hex literals.
5. **Write the prototype** as a single self-contained HTML file in
   `spec/<site>/<section>/_prototype/index.html` plus `styles.css` and
   optional `motion.js`. The prototype:
   - Uses only the tokens from `02-tokens.md`.
   - Renders correctly at 360 / 768 / 1024 / 1440.
   - Has no external network calls (inline SVGs, system fonts as fallback).
   - Includes a `prefers-reduced-motion` branch.
6. **Compare prototype vs image** at three widths. Note every visible delta
   (pixel diff > 4px or color delta > 2 ΔE) and either fix or log to
   `20-gap-analysis.md`.

## Anti-patterns

- Pasting the image into the spec without an anatomy table.
- Naming colors by their hex (`--amber-F5A524`) instead of role (`--color-accent`).
- Skipping the prototype because "the image is enough".

## Deliverables

- `00-<section>-canonical.<ext>` saved.
- `02-tokens.md` populated.
- `05-<section>-anatomy.md` populated with a numbered table.
- `_prototype/` renders at all four widths.
- Hand off to `02-write-spec.md`.