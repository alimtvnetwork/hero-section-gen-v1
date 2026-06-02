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
}

:root { color-scheme: dark; }
```

## Semantic role rules

- Names express **role**, not raw color (`--color-accent`, not `--color-amber`).
- Opacity variants use `/ <alpha>` notation directly in tokens (no separate `--*-12` siblings).
- No component may import a hex or rgb literal. Lint via `eslint-plugin-no-restricted-syntax`.

## Cross-stack export

`02-tokens.md` is the source. PART-5 §57 (Style Dictionary export) consumes this file and emits:
- `tokens.css` (web) — generated, do not edit.
- `tokens.swift` / `tokens.kt` (native, future) — generated.

## Forbidden

- Tailwind v3 `tailwind.config.js` color objects (this repo is v4).
- Raw hex in JSX (`text-[#fff]`, `bg-[#0A0A0A]`).
- Two tokens for the same role with different names.