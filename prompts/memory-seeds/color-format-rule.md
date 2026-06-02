---
name: 4-format color rule
description: Every color token in every spec must be expressed in oklch + hex + rgb + hsl.
type: design
---
# 4-format color rule

Every color row in `spec/<site>/<section>/02-tokens.md` MUST list the color
in **four formats** so a blind AI can implement without conversion:

1. **oklch** — authoritative source.
2. **hex** — `#RRGGBB` (or `#RRGGBB @ NN%` for alpha).
3. **rgb** — `rgb(R G B)` or `rgb(R G B / A)`.
4. **hsl** — `hsl(H S% L%)` or `hsl(H S% L% / A)`.

Rules:
- oklch is edited first. hex/rgb/hsl regenerated in the same commit.
- Tolerance: derived values round-trip to ΔE < 1.5 of the oklch.
- Every shade / hover / focus / glow / gradient stop gets its own row.
- Every section (hero, nav, card, footer, …) lists its colors part-by-part
  (background, surface, border, headline, body, eyebrow, accent, CTA fill,
  CTA text, CTA glow, icon, divider, etc.). No part shares a row.
- Every header level (display-1, h2, h3, eyebrow, lede, body, caption, …)
  gets its own typography row naming the explicit font (e.g. "General Sans",
  "Inter") — never `sans-serif`.

Source of truth: `prompts/06-color-and-typography.md`.