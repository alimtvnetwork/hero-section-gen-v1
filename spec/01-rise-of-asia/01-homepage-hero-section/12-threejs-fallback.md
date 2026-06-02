# Three.js Background — Fallback Decision (MVP)

**Decision:** Three.js particle field is **optional polish**, NOT required for MVP. The hero must render and pass all gates without it.

## Layer order

1. **Base (always rendered, SSR-safe):** static CSS radial gradient using `--color-bg` → `--color-accent-soft` at ~20% opacity, anchored behind the portrait.
2. **Particles (client-only, progressive):** Three.js canvas mounted via `useEffect` after hydration, behind the gradient, blend-mode `screen`.
3. **`<noscript>` underlay:** the base gradient + canonical first-paint HTML (H1, lede, CTAs, portrait `<img>`). No "enable JS" banner — the page must look complete without JS.

## When Three.js is skipped (mandatory)

- `prefers-reduced-motion: reduce` is set.
- `navigator.hardwareConcurrency < 4` OR `navigator.deviceMemory < 4`.
- WebGL2 context creation returns `null`.
- `HERO_3D_ENABLED=false` (env or flag).
- SSR / build prerender (no `window`).

In any skip path, the base gradient is the final state. No layout shift, no opacity flash.

## Acceptance

- Lighthouse run with JS disabled: hero visible, LCP element = portrait `<img>`, CLS = 0.
- `prefers-reduced-motion: reduce` snapshot: identical to base gradient state (no particles, no transform animations).
- Bundle: Three.js code-split into its own chunk, lazy-loaded; not in the critical path. Budget: ≤ 60 KB gz, see `27-budgets.md` (future).

## Failure handling

- WebGL context loss → remove canvas, revert to gradient. Fire `hero_3d_disabled` analytics event with `reason: "context_lost"`.
- Init error → swallow, log to error reporter, gradient remains. Never throw to user.