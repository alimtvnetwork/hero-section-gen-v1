# Project Memory

## Core
Spec lives in `spec/hero-section/`. There is NO `app/` folder — TanStack Start uses `src/routes/`.
User audits spec phase-by-phase: each "next" = exactly one small phase from `spec-issues/README.md`. Never batch phases.
Never fix spec issues without an explicit "next" — discovery and fixing are separate modes.
Canonical hero image is `00-hero-canonical.png`. `01-hero-variant-archived.png` is reference-only — do not build from it.
GAP-ANALYSIS.md overrides SPEC.md until SPEC.md is patched (Phase 1).
Repo rule: semantic tokens only, `oklch()` in `src/styles.css`, Tailwind v4 `@theme`. No raw hex / `text-white` / `bg-neutral-950` in components.
Every color in every spec MUST be listed in 4 formats: oklch + hex + rgb + hsl. Section by section, part by part. See [4-format color rule](mem/design/color-format-rule.md).
Project-wide default text-shadow: `--shadow-text-default: rgb(0 0 0) 1px 0.7px 0px;` — apply to headline/body text on dark surfaces. See [Common text-shadow](mem/design/text-shadow.md).
Every header level gets its own typography row in `02-tokens.md` naming the explicit font ("General Sans", "Inter"), never `sans-serif`.

## Memories
- [Spec audit](spec-issues/README.md) — headline verdict, 15-phase fix plan, queued discovery passes.
- [Canonical vs archived deltas](spec/hero-section/GAP-ANALYSIS.md) — 15 visual rules where canonical wins over archived.
- [Common text-shadow](mem/design/text-shadow.md) — canonical `--shadow-text-default` token + apply/opt-out matrix.
- [4-format color rule](mem/design/color-format-rule.md) — every color in oklch + hex + rgb + hsl, part by part, per section.
