# Memory-Leak Cleanup Checklist

**Authority:** Every effect that creates a subscription, timer, observer, or listener must register its teardown. Reviewed in PR template.

## Patterns

```tsx
// Timer
useEffect(() => {
  const id = setTimeout(fn, 6000)
  return () => clearTimeout(id)
}, [])

// Interval (carousel auto-advance)
useEffect(() => {
  if (paused) return
  const id = setInterval(advance, autoAdvanceMs)
  return () => clearInterval(id)
}, [paused, autoAdvanceMs])

// RAF (particle loop)
useEffect(() => {
  let raf = 0
  const tick = () => { draw(); raf = requestAnimationFrame(tick) }
  raf = requestAnimationFrame(tick)
  return () => cancelAnimationFrame(raf)
}, [])

// IntersectionObserver (hero_view, lazy portraits)
useEffect(() => {
  const io = new IntersectionObserver(cb, { threshold: 0.5 })
  io.observe(el)
  return () => io.disconnect()
}, [])

// Pointer / keyboard listeners (carousel swipe, ESC)
useEffect(() => {
  const onKey = (e: KeyboardEvent) => { if (e.key === "Escape") close() }
  window.addEventListener("keydown", onKey)
  return () => window.removeEventListener("keydown", onKey)
}, [close])

// WebGL context (Three.js)
useEffect(() => {
  const renderer = new WebGLRenderer({ canvas })
  return () => { renderer.dispose(); renderer.forceContextLoss() }
}, [])
```

## CI lint

- `eslint-plugin-react-hooks/exhaustive-deps` — error.
- Custom rule `no-unhandled-listener` — error on any `addEventListener` inside `useEffect` whose returned cleanup doesn't include `removeEventListener`.

## PR checklist row

`[ ] Every useEffect with side-effects returns a cleanup, OR the side-effect has no teardown surface (documented in code comment).`

## Acceptance

- Chrome DevTools Memory profile across 5 carousel auto-advances + navigate-away shows no detached DOM and no growing retained size.
- `hero_memory_warn` event (sampled) fires only on `performance.memory.usedJSHeapSize > 90 MB`.