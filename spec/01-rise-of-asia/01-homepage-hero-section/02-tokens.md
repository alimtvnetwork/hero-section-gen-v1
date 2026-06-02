# Design Tokens — Single Source of Truth

**Authority:** This file is the only source for color/space/motion/type values. `src/styles.css` must mirror this file verbatim. Any token defined in 13-spec.md §2 or PART-5 §57 is **derived** from here and treated as documentation.

Stack: **Tailwind v4** via `@theme` in `src/styles.css`. All colors in **oklch**. No raw hex in components — use semantic Tailwind classes (e.g. `bg-bg`, `text-text-muted`, `border-border-strong`).

## `src/styles.css` (canonical block)

```css
@import "tailwindcss";

@theme {
  /* Surfaces */
  --color-bg:            oklch(0.10 0   0);       /* #0A0A0A */
  --color-bg-elev:       oklch(0.16 0   0);       /* #141414 */
  --color-bg-elev-2:     oklch(0.18 0   0);       /* #1A1A1A */

  /* Borders */
  --color-border:        oklch(1 0 0 / 0.06);
  --color-border-strong: oklch(1 0 0 / 0.10);

  /* Text */
  --color-text:          oklch(1    0 0);
  --color-text-muted:    oklch(0.78 0 0);         /* #B5B5B5 */
  --color-text-dim:      oklch(0.55 0 0);         /* #7A7A7A */

  /* Accent (amber/orange — all share hue) */
  --color-accent:        oklch(0.79 0.16 70);     /* ≈ #F5A524 */
  --color-accent-strong: oklch(0.74 0.18 60);     /* ≈ #FF9500 */
  --color-accent-soft:   oklch(0.79 0.16 70 / 0.12);

  /* Type */
  --font-display: "General Sans", "Satoshi", Inter, system-ui, sans-serif;
  --font-body:    "Inter", system-ui, sans-serif;

  /* Spacing scale (4px base) */
  --space-1: 0.25rem; --space-2: 0.5rem; --space-3: 0.75rem; --space-4: 1rem;
  --space-6: 1.5rem;  --space-8: 2rem;   --space-10: 2.5rem; --space-12: 3rem;
  --space-16: 4rem;   --space-20: 5rem;  --space-24: 6rem;

  /* Motion */
  --ease-standard: cubic-bezier(0.2, 0.8, 0.2, 1);
  --ease-emphasis: cubic-bezier(0.16, 1, 0.3, 1);
  --dur-fast: 180ms; --dur-med: 320ms; --dur-slow: 640ms;

  /* Text shadow — project-wide default for headline/body on dark surfaces */
  --shadow-text-default: rgb(0 0 0) 1px 0.7px 0px;

  /* Accent CTA glow */
  --shadow-cta-glow: 0 0 32px oklch(0.79 0.16 70 / 0.35);
}

:root { color-scheme: dark; }
```

## Color table — 4-format (oklch + hex + rgb + hsl)

Per `prompts/06-color-and-typography.md`. oklch is authoritative; the other
three are derived and must round-trip to ΔE < 1.5. Edit oklch first, then
regenerate hex/rgb/hsl in the same commit.

| token / role | oklch (source) | hex | rgb | hsl | variants | used by |
|---|---|---|---|---|---|---|
| `--color-bg` / page canvas | `oklch(0.10 0 0)` | `#0A0A0A` | `rgb(10 10 10)` | `hsl(0 0% 4%)` | — | hero bg, page bg |
| `--color-bg-elev` / card surface | `oklch(0.16 0 0)` | `#141414` | `rgb(20 20 20)` | `hsl(0 0% 8%)` | — | profile card |
| `--color-bg-elev-2` / hover surface | `oklch(0.18 0 0)` | `#1A1A1A` | `rgb(26 26 26)` | `hsl(0 0% 10%)` | — | card hover |
| `--color-border` | `oklch(1 0 0 / 0.06)` | `#FFFFFF @ 6%` | `rgb(255 255 255 / 0.06)` | `hsl(0 0% 100% / 0.06)` | — | dividers |
| `--color-border-strong` | `oklch(1 0 0 / 0.10)` | `#FFFFFF @ 10%` | `rgb(255 255 255 / 0.10)` | `hsl(0 0% 100% / 0.10)` | hover | card edge |
| `--color-text` / primary | `oklch(1 0 0)` | `#FFFFFF` | `rgb(255 255 255)` | `hsl(0 0% 100%)` | — | headline, name |
| `--color-text-muted` | `oklch(0.78 0 0)` | `#B5B5B5` | `rgb(181 181 181)` | `hsl(0 0% 71%)` | — | body, lede |
| `--color-text-dim` | `oklch(0.55 0 0)` | `#7A7A7A` | `rgb(122 122 122)` | `hsl(0 0% 48%)` | — | meta, dots-inactive |
| `--color-accent` / amber | `oklch(0.79 0.16 70)` | `#F5A524` | `rgb(245 165 36)` | `hsl(38 91% 55%)` | `/0.12` soft, `/0.35` glow, hover `+8% white` | accent words, CTA, eyebrow |
| `--color-accent-strong` | `oklch(0.74 0.18 60)` | `#FF9500` | `rgb(255 149 0)` | `hsl(35 100% 50%)` | — | CTA pressed, quote bar |
| `--color-accent-soft` | `oklch(0.79 0.16 70 / 0.12)` | `#F5A524 @ 12%` | `rgb(245 165 36 / 0.12)` | `hsl(38 91% 55% / 0.12)` | — | accent-word underlay, badge bg |

### Shadows (text + box)

| token | css value | notes |
|---|---|---|
| `--shadow-text-default` | `rgb(0 0 0) 1px 0.7px 0px` | **Apply to all `<h1>..<h3>` and body text on `--color-bg*` surfaces.** Opt out for inputs, code, tooltips, badges. |
| `--shadow-cta-glow` | `0 0 32px oklch(0.79 0.16 70 / 0.35)` | Primary CTA hover/idle glow. |

## Typography table — per header level

| role | element | font-family | weight | size | line-height | letter-spacing | text-transform | color token | text-shadow |
|---|---|---|---|---|---|---|---|---|---|
| display-1 / headline | `<h1>` | `var(--font-display)` "General Sans" | 600 | `clamp(2.5rem, 6vw, 5rem)` | 1.05 | -0.02em | none | `--color-text` | `--shadow-text-default` |
| display-1 / accent span | `<span>` inside `<h1>` | `var(--font-display)` "General Sans" | 600 italic | inherit | inherit | -0.02em | none | `--color-accent` | `--shadow-text-default` |
| eyebrow | `<p class="eyebrow">` | `var(--font-body)` "Inter" | 500 | `0.75rem` (12px) | 1.2 | 0.12em | uppercase | `--color-accent` | none |
| lede | `<p class="lede">` | `var(--font-body)` "Inter" | 400 | `1.125rem` (18px) | 1.55 | 0 | none | `--color-text-muted` | `--shadow-text-default` |
| body | `<p>` | `var(--font-body)` "Inter" | 400 | `1rem` (16px) | 1.6 | 0 | none | `--color-text-muted` | `--shadow-text-default` |
| stat-number | `<span class="stat-n">` | `var(--font-display)` "General Sans" | 600 | `2rem` (32px) | 1.1 | -0.01em | none | `--color-text` | `--shadow-text-default` |
| stat-label | `<span class="stat-l">` | `var(--font-body)` "Inter" | 500 | `0.75rem` (12px) | 1.3 | 0.08em | uppercase | `--color-text-dim` | none |
| profile name | `<h3>` | `var(--font-display)` "General Sans" | 600 | `1.125rem` (18px) | 1.3 | 0 | none | `--color-text` | `--shadow-text-default` |
| profile sub-role | `<p class="sub-role">` | `var(--font-body)` "Inter" | 500 | `0.75rem` (12px) | 1.3 | 0.08em | uppercase | `--color-text-dim` | none |
| profile quote | `<blockquote>` | `var(--font-body)` "Inter" | 400 italic | `0.9375rem` (15px) | 1.5 | 0 | none | `--color-text-muted` | `--shadow-text-default` |
| cta primary | `<button>` | `var(--font-body)` "Inter" | 600 | `1rem` (16px) | 1 | 0 | none | `--color-bg` (on amber) | none |
| cta secondary | `<button>` | `var(--font-body)` "Inter" | 500 | `1rem` (16px) | 1 | 0 | none | `--color-text` | `--shadow-text-default` |

## Semantic role rules

- Names express **role**, not raw color (`--color-accent`, not `--color-amber`).
- Opacity variants use `/ <alpha>` notation directly in tokens (no separate `--*-12` siblings).
- No component may import a hex or rgb literal. Lint via `eslint-plugin-no-restricted-syntax`.
- Every color edit MUST update oklch + hex + rgb + hsl in the table above in
  the same commit. Audits fail if any column is stale.
- Body and headline text on `--color-bg*` surfaces MUST apply
  `text-shadow: var(--shadow-text-default)` unless the typography table row
  says `none`.

## Cross-stack export

`02-tokens.md` is the source. PART-5 §57 (Style Dictionary export) consumes this file and emits:
- `tokens.css` (web) — generated, do not edit.
- `tokens.swift` / `tokens.kt` (native, future) — generated.

## Forbidden

- Tailwind v3 `tailwind.config.js` color objects (this repo is v4).
- Raw hex in JSX (`text-[#fff]`, `bg-[#0A0A0A]`).
- Two tokens for the same role with different names.