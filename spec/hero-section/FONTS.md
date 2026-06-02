# Font Loading + CLS Strategy

**Authority:** Hero CLS target = **0.00**. Font swap is the #1 cause of CLS regressions; this file is the only rule.

## Stack

- **Display:** General Sans (700) — self-hosted WOFF2, subset Latin + Latin-Ext.
- **Body:** Inter (400/500/700) — self-hosted WOFF2, subset Latin + Latin-Ext.
- **Fallback (always):** `system-ui, sans-serif`.

No Google Fonts. No `@import` from external origins.

## Self-hosting + preload

`src/styles.css`:

```css
@font-face {
  font-family: "General Sans";
  src: url("/fonts/general-sans-700.woff2") format("woff2");
  font-weight: 700;
  font-style: normal;
  font-display: swap;
  size-adjust: 100.5%;        /* metric-match Inter (see Fontaine) */
  ascent-override: 95%;
  descent-override: 22%;
  line-gap-override: 0%;
}

@font-face {
  font-family: "Inter";
  src: url("/fonts/inter-var.woff2") format("woff2");
  font-weight: 400 700;
  font-style: normal;
  font-display: swap;
}
```

TanStack `__root.tsx` head links:

```ts
links: [
  { rel: "preload", as: "font", href: "/fonts/inter-var.woff2",       type: "font/woff2", crossOrigin: "anonymous" },
  { rel: "preload", as: "font", href: "/fonts/general-sans-700.woff2", type: "font/woff2", crossOrigin: "anonymous" },
]
```

## Metric matching (mandatory)

Use **Fontaine** at build time to generate metric-matching `@font-face` for `system-ui` fallback so the fallback occupies the same vertical space as the final font. Without this, CLS is non-zero in cold-cache loads.

## Forbidden

- `font-display: block` (FOIT pause hurts LCP).
- `font-display: optional` (skips font on slow networks — brand violation).
- Inline `<style>` blocks defining `@font-face` (must live in `src/styles.css` for CSP hash stability — see Batch 4 CSP step).
- Variable axes other than weight (no italic; hero uses upright only).

## Acceptance

- Lighthouse CLS = 0 cold-cache.
- Both WOFF2 files in the critical path under 80 KB combined gz.
- Snapshot S-1440-en passes within `maxDiffPixelRatio: 0.005` cold vs warm cache.