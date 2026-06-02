# Project Memory

## Core
Spec lives in `spec/hero-section/`. There is NO `app/` folder — TanStack Start uses `src/routes/`.
User audits spec phase-by-phase: each "next" = exactly one small phase from `spec-issues/README.md`. Never batch phases.
Never fix spec issues without an explicit "next" — discovery and fixing are separate modes.
Canonical hero image is `00-hero-canonical.png`. `01-hero-variant-archived.png` is reference-only — do not build from it.
GAP-ANALYSIS.md overrides SPEC.md until SPEC.md is patched (Phase 1).
Repo rule: semantic tokens only, `oklch()` in `src/styles.css`, Tailwind v4 `@theme`. No raw hex / `text-white` / `bg-neutral-950` in components.

## Memories
- [Spec audit](spec-issues/README.md) — headline verdict, 15-phase fix plan, queued discovery passes.
- [Canonical vs archived deltas](spec/hero-section/GAP-ANALYSIS.md) — 15 visual rules where canonical wins over archived.
