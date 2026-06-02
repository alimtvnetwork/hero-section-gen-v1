# 06 — Color & Typography Extraction (MANDATORY)

> **Color is the #1 source of "blind AI" failure.** A spec that omits a single
> shade, gradient stop, or text-shadow forces the next AI to guess — and it
> will guess wrong. This file is the contract every spec MUST satisfy.

## Rule 1 — Section-by-section, part-by-part

For every section of the page (hero, nav, card, footer, modal, …) and every
visible part inside that section (background, surface, border, headline,
body text, eyebrow, accent word, CTA fill, CTA text, CTA glow, icon, divider,
stat number, stat label, quote bar, badge, hover state, focus ring, …),
the spec MUST list a dedicated color row. No part may share a row with
another part "because they look the same" — even if values match today,
they diverge tomorrow.

## Rule 2 — Four-format color table (NON-NEGOTIABLE)

Every color row in `spec/<site>/<section>/02-tokens.md` MUST be expressed in
**four formats** so any AI / designer / tool can consume it without
conversion:

| role / token | oklch (authoritative) | hex | rgb | hsl | opacity variants | used by |
|---|---|---|---|---|---|---|
| `--color-bg` | `oklch(0.10 0 0)` | `#1A1A1A` | `rgb(26 26 26)` | `hsl(0 0% 10%)` | `/0.6` overlay | hero canvas, page bg |
| `--color-accent` | `oklch(0.79 0.16 70)` | `#F5A524` | `rgb(245 165 36)` | `hsl(38 90% 55%)` | `/0.12` soft, `/0.35` glow | accent words, CTA fill |
| … | … | … | … | … | … | … |

- **oklch is the source of truth.** hex/rgb/hsl are derived once and frozen
  alongside it. If oklch changes, regenerate all three siblings in the same
  edit.
- Tolerance: derived values must round-trip to within **ΔE < 1.5** of the oklch.
- Gradients get their own row with each stop listed in all four formats:
  `linear-gradient(135deg, <stop1 oklch>, <stop2 oklch>)` + a `stops` column
  listing every stop in hex/rgb/hsl.

## Rule 3 — Typography per header level

Every header / text role on screen gets a row in the typography table:

| role | element | font-family | weight | size (rem / px) | line-height | letter-spacing | text-transform | color token | text-shadow |
|---|---|---|---|---|---|---|---|---|---|
| display-1 | `<h1>` headline | `var(--font-display)` "General Sans" | 600 | `clamp(2.5rem, 6vw, 5rem)` | 1.05 | -0.02em | none | `--color-text` | `--shadow-text-default` |
| eyebrow | `<p class="eyebrow">` | `var(--font-body)` "Inter" | 500 | 0.75rem / 12px | 1.2 | 0.12em | uppercase | `--color-accent` | none |
| body | `<p>` | `var(--font-body)` "Inter" | 400 | 1rem / 16px | 1.6 | 0 | none | `--color-text-muted` | none |
| … | … | … | … | … | … | … | … | … | … |

- Always name the **specific font** ("General Sans", "Satoshi", "Inter"),
  never "sans-serif". System fallbacks belong in the token, not the spec row.
- Always specify whether text-shadow applies (see Rule 4).

## Rule 4 — Common text-shadow (project-wide)

The project's default body / headline text-shadow is a hairline drop used for
legibility on near-black surfaces. **Always include it in `02-tokens.md` and
apply it to any text that sits on `--color-bg` / `--color-bg-elev`** unless a
row explicitly opts out.

```css
/* canonical token — copy verbatim */
--shadow-text-default: rgb(0 0 0) 1px 0.7px 0px;
```

Multi-format reference:

| token | css | hex | rgb | hsl |
|---|---|---|---|---|
| `--shadow-text-default` | `rgb(0 0 0) 1px 0.7px 0px` | `#000000` | `rgb(0 0 0)` | `hsl(0 0% 0%)` |

Opt-out cases (no text-shadow): inputs, code blocks, tooltips, badges with
colored fills, any element where the shadow would smudge anti-aliasing on a
light surface.

## Rule 5 — Shades, glows, gradients

Whenever a color appears with a shade variation (hover, active, disabled,
soft, glow, overlay), the spec MUST add a row for **each variant** with its
own opacity / mix recipe:

| variant | recipe | css |
|---|---|---|
| accent / soft | `--color-accent` @ 12% | `oklch(0.79 0.16 70 / 0.12)` |
| accent / glow | `--color-accent` blurred 32px @ 35% | `box-shadow: 0 0 32px oklch(0.79 0.16 70 / 0.35)` |
| accent / hover | mix with white 8% | `color-mix(in oklab, var(--color-accent) 92%, white)` |

## Rule 6 — Where this lives in the spec

- `02-tokens.md` → the four-format color table, typography table,
  text-shadow token. Authoritative.
- `13-spec.md` → references tokens by name only, never re-declares values.
- `04-component-contract.md` → every visible prop that maps to a color
  cites the token.
- `05-<section>-anatomy.md` → "color token" column points at `02-tokens.md`.

## Deliverables checklist

- [ ] Every visible part has a color row in `02-tokens.md`.
- [ ] Every color row has oklch + hex + rgb + hsl.
- [ ] Every shade / hover / glow / gradient stop is its own row.
- [ ] Every header / text role has a typography row.
- [ ] `--shadow-text-default` is declared and applied where appropriate.
- [ ] No component file contains a raw hex, rgb, or hsl literal.

## Changelog

- v1 (2026-06-02) — initial. Four-format rule + canonical text-shadow.