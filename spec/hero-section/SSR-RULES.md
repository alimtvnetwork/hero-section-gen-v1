# SSR Determinism + Hydration Rules

**Authority:** Hydration mismatches in the hero are P0 — they trash LCP and flash content. These rules eliminate the class of bug.

## Forbidden at render time (module + component scope, server AND client paths)

- `Math.random()`, `Date.now()`, `new Date()`, `crypto.randomUUID()` inside render. Generate in loader; pass via props/loaderData.
- `window`, `document`, `navigator`, `localStorage`, `matchMedia` at module scope or render-time. Wrap in `useEffect` or guard with `typeof window !== "undefined"` only inside event handlers.
- Unstable sort (`engineers.sort((a, b) => Math.random() - 0.5)`). Use server-side stable sort by `id`.
- Locale-sensitive `Date`/`Number` formatting in render without explicit `locale`. Always pass `locale` from loader.
- Reading CSS variables to drive layout (`getComputedStyle`) — server can't.

## Allowed pattern — seeded randomness for variant assignment

```ts
// src/lib/variant.ts
import seedrandom from "seedrandom"
export function pickVariant(userId: string, variants: string[]) {
  const rng = seedrandom(userId)
  return variants[Math.floor(rng() * variants.length)]
}
```

Same `userId` ⇒ same variant on server and client ⇒ zero mismatch.

## Particle field (client-only)

```tsx
const HeroParticles = lazy(() => import("./HeroParticles.client"))

function Hero() {
  const mounted = useMounted()  // useEffect → setMounted(true)
  return (
    <>
      <HeroGradient />          {/* SSR-safe, always rendered */}
      {mounted && <Suspense fallback={null}><HeroParticles /></Suspense>}
    </>
  )
}
```

File suffix `.client.tsx` is enforced by the project's import-protection (see template rules).

## Hydration mismatch detection

- React's hydration warning is treated as a CI failure via `error` handler in `__root.tsx` posting to the error reporter with `tag: "hydration"`.
- Alarms when `hero_hydration_mismatch` event count > 1 / hour.

## Acceptance

- 0 hydration warnings in CI Playwright run.
- 0 hydration warnings in production for 7 days post-deploy (SLO breach otherwise).