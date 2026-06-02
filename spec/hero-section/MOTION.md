# Motion + Reduced-Motion Matrix

**Authority:** Every animation in `<Hero />` listed here with `prefers-reduced-motion: reduce` behavior. Anything unlisted is forbidden.

## Full-motion table (default)

| Element | Property | From → To | Duration | Easing | Delay | Loop |
|---|---|---|---|---|---|---|
| Hero container | opacity | 0 → 1 | `--dur-med` 320ms | `--ease-standard` | 0 | once |
| H1 lines | translateY | 12px → 0 | `--dur-med` 320ms | `--ease-emphasis` | 60ms stagger | once |
| CTAs | translateY + opacity | 8px/0 → 0/1 | `--dur-fast` 180ms | `--ease-standard` | 240ms | once |
| Stat chips | opacity | 0 → 1 | `--dur-fast` 180ms | linear | 300ms +60ms stagger | once |
| Portrait | scale | 1.02 → 1.0 | `--dur-slow` 640ms | `--ease-emphasis` | 120ms | once |
| Halo gradient | opacity | 0 → 1 | `--dur-slow` 640ms | linear | 200ms | once |
| Badge pill glow | box-shadow opacity | 0.12 ↔ 0.28 | 4000ms | ease-in-out | 0 | infinite |
| Carousel slide | translateX + opacity | ±32px/0 → 0/1 | `--dur-med` 320ms | `--ease-emphasis` | 0 | per-change |
| Carousel auto-advance | timer | n/a | 6000ms | n/a | n/a | infinite |
| Particle field | per-particle drift | n/a | RAF loop | n/a | n/a | infinite |

## Reduced-motion overrides (`@media (prefers-reduced-motion: reduce)`)

| Element | New behavior |
|---|---|
| Hero container | render final state, no fade |
| H1 lines | render final state, no translate |
| CTAs | render final state |
| Stat chips | render final state |
| Portrait | render final state, no scale |
| Halo gradient | render final state |
| Badge pill glow | static at opacity 0.20 |
| Carousel slide | **cross-fade** 180ms opacity-only, no translate |
| Carousel auto-advance | **disabled** — manual control only; segment counter still updates on user action |
| Particle field | **not mounted** (see `THREEJS-FALLBACK.md`) |

## `prefers-reduced-data` overrides

- Particles not mounted.
- AVIF replaced with JPEG (skip WebP/AVIF download).
- Carousel auto-advance disabled.

## Implementation rule

Use Framer Motion's `useReducedMotion()` hook at the top of `<Hero />` and pass a single `reduced` boolean to all motion components. CSS `@media` block also defined in `src/components/hero/Hero.css` as a defence-in-depth fallback.

## Acceptance

- Snapshot S-1440-rm (per `SNAPSHOTS.md`) renders all final states with zero motion.
- Axe rule `prefers-reduced-motion` enforced in CI (warns if any `transition:` survives in reduced mode).