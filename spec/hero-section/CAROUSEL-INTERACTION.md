# Carousel Interaction Spec — Hero

**Owner:** Frontend · **Closes:** #49

## Pointer / swipe (mobile + tablet)
- Library: `@use-gesture/react` v10+ (`useDrag`).
- Swipe threshold: **40px** horizontal OR **velocity > 0.3 px/ms**.
- Direction: horizontal only; vertical drag releases to native scroll.
- Resistance at edges: 0.3 rubber-band; never wraps (no infinite loop — see `HERO-ANATOMY.md` §carousel).

## Keyboard (per APG carousel pattern — see `A11Y.md`)
| Key | Action |
|---|---|
| ← / → | prev / next slide |
| Home / End | first / last |
| Tab | focus next control (Prev → Play/Pause → Next → tabs) |
| Space (on Play/Pause) | toggle auto-advance |

## Auto-advance
- Interval: **6000ms** (paused under `prefers-reduced-motion`, see `MOTION.md`).
- Pauses on: hover, focus-within, pointerdown, document hidden (`visibilitychange`).
- Resumes 2000ms after last interaction ends.

## Pointer event policy
- `touch-action: pan-y` on track (allows vertical scroll, claims horizontal).
- `pointer-events: none` on slides while transitioning (320ms — see `MOTION.md`).

## Test fixtures
See `STORYBOOK-FIXTURES.md` §carousel + Playwright `carousel.swipe.spec.ts`.
