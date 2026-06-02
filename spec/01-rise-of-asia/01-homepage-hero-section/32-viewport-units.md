# Mobile Viewport Units

**Authority:** iOS Safari's URL bar collapse breaks `100vh`. These rules eliminate hero clip / overflow bugs.

## Rules

| Use case | Unit | Why |
|---|---|---|
| Hero `min-height` | `100svh` | Smallest viewport — guarantees hero fits when URL bar is shown. |
| Sticky overlays (CTA bar) | `dvh` | Dynamic — recalculates on URL-bar toggle, no jump under finger. |
| Full-bleed backdrop / portrait edge | `100lvh` | Largest — fills under the collapsed URL bar (visual completeness). |
| Desktop fallback | `100vh` | UA without `*vh` support: ≥99% browsers in 2026 support the new units. |

## Implementation

```css
.hero { min-height: 100svh; }
@supports not (height: 100svh) {
  .hero { min-height: 100vh; }
}
.hero__backdrop { height: 100lvh; }
.hero__cta-bar  { bottom: 0; height: 64px; position: sticky; }   /* dvh-aware via JS-less layout */
```

## Forbidden

- `height: 100vh` on the hero root (iOS bug).
- JS-based `--vh` polyfills (`window.innerHeight * 0.01`) — obsolete; layout shift on URL-bar toggle.
- Mixing `svh`/`dvh`/`lvh` arbitrarily — each role above has exactly one unit.

## Acceptance

- iOS 16/17 Safari: hero visible without scroll on first paint; URL-bar collapse causes no layout shift inside hero text column.
- Android Chrome: same behaviour.
- Snapshot S-375-en taken with URL bar **shown** and **collapsed**; both within tolerance.