# Error / Empty / Loading States

**Authority:** Visual + copy spec for every non-happy-path state. Tone-matched to the hero brand (premium, calm, never apologetic).

## Loading (initial / CMS fetch in flight)

- Layout reserved via skeleton blocks at exact final dimensions (CLS = 0).
- Text rows: `bg-bg-elev` rounded rects, shimmer animation only if `prefers-reduced-motion` is NOT set.
- Portrait zone: static gradient (`--color-bg` → `--color-accent-soft` 20%).
- Stat chips: 3 placeholder pills, no shimmer in reduced-motion.
- Max duration before showing error state: **8s**. After 8s with no data → EC-11 path.

## Empty (no engineers from CMS)

- Headline + lede + CTAs render normally.
- Portrait zone: gradient + centered illustration (`src/assets/hero-empty-portrait.svg`, single-color amber line art).
- Profile card: replaced with a single CTA "Meet our team →" linking to `/team`.
- No carousel controls. No analytics for slide events.

## Error (CMS 5xx, hydration failure, runtime throw)

- Triggered by `errorComponent` from TanStack route OR React error boundary in `<Hero />`.
- Layout preserved; replaces only the dynamic zones (portrait + profile).
- Copy (canonical, do not paraphrase):
  - Headline (kept): the static SSR headline remains.
  - Replacement card:
    - Title: `We're recalibrating.`
    - Body: `This section will be back in a moment. In the meantime, our team replies to every intro request within one business day.`
    - Primary CTA: `Email us` → `mailto:hello@example.com?subject=Intro%20call`
    - Secondary CTA: `Try again` → `router.invalidate(); reset()`
- Fires `hero_error_view` with `{ reason, route }`.
- No stack trace ever shown to users.

## Offline (navigator.onLine === false)

- Top-of-hero banner: amber pill, 13px, copy: `You're offline — showing the last version we loaded.`
- Reads from service worker cache (post-MVP; falls through to error state if SW absent).

## Slow (LCP > 4s observed)

- No visual change. Fire `hero_slow_lcp` with measured LCP.
- Engineering signal only; not a user-visible state.

## Acceptance

- Each state has a Storybook story (`HeroLoading`, `HeroEmpty`, `HeroError`, `HeroOffline`).
- Each has a canonical screenshot at 1440 and 375.
- Reduced-motion variant of `HeroLoading` snapshot included.