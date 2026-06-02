# Lazy-Load Priority Matrix

**Authority:** Every asset/component in the hero has one load tier. Untiered = treated as `idle`.

## Tiers

| Tier | When loads | Mechanism | Examples |
|---|---|---|---|
| `critical` | inline in HTML, blocks render | bundled, no split | TOKENS CSS, base gradient, first H1 paint |
| `preload`  | in `<head>` `<link rel=preload>`, parallel with HTML | preload | LCP portrait (first engineer AVIF), 2 WOFF2 fonts |
| `hydration`| after React mount | normal import | `<Hero />` interactive layer, CTAs, carousel JS |
| `idle`     | after first interaction OR `requestIdleCallback(5s)` | dynamic import | Three.js particles, analytics SDK, share buttons |
| `interaction` | on user action only | dynamic import | Calendly modal iframe, LinkedIn share dialog |
| `viewport` | on `IntersectionObserver` enter | dynamic import or `loading=lazy` | Below-fold engineer portraits 2..N, logo strip images |

## Code template

```tsx
// idle tier
useEffect(() => {
  const ric = (window as any).requestIdleCallback ?? ((cb: any) => setTimeout(cb, 5000))
  ric(() => import("./HeroParticles.client"))
}, [])

// interaction tier
const [calendlyLoaded, setCalendlyLoaded] = useState(false)
<button onClick={() => { setCalendlyLoaded(true); import("./CalendlyModal") }}>Book a call</button>
```

## Forbidden

- Loading Three.js in `critical` or `preload`.
- Loading Calendly script before user click.
- `loading="eager"` on any image other than the first engineer's LCP portrait.

## Acceptance

- Lighthouse "Unused JavaScript" < 30 KB on first paint.
- Network panel: only `critical` + `preload` assets in the first 1 s.