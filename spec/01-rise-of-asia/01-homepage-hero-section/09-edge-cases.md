# Edge-Case Fixtures

**Authority:** Every state listed here must have a Storybook story AND a Playwright snapshot. Untested states are not shippable.

## Data states

| ID | State | Fixture | Expected render |
|---|---|---|---|
| EC-01 | `engineers.length === 1` | `single-engineer.fixture.ts` | Carousel controls hidden; auto-advance disabled. |
| EC-02 | `engineers.length === 0` | `no-engineers.fixture.ts` | Portrait zone shows static gradient + "Meet our team" CTA. No analytics fired. |
| EC-03 | `engineers.length > 6` | `many-engineers.fixture.ts` | First 6 used; warn in dev; sixth is the loop end. |
| EC-04 | `stats.length !== 3` | `wrong-stats.fixture.ts` | Renders first 3; logs `console.error` in dev. |
| EC-05 | Missing `portraitSrc` | `no-portrait.fixture.ts` | Triggers fallback chain (manifest §5). |
| EC-06 | Quote 250 chars (overflow) | `long-quote.fixture.ts` | Clamps to 5 lines @ 375; ellipsis on word boundary. |
| EC-07 | German headline (+35%) | `de-headline.fixture.ts` | Wraps to 3 lines @ 375; no clip; no `…` allowed on headline. |
| EC-08 | Arabic content (RTL) | `ar-full.fixture.ts` | Full mirror per `07-i18n.md` table. |
| EC-09 | Missing `engineer.badge` | `no-badge.fixture.ts` | Pill not rendered; layout collapses gracefully (no gap). |
| EC-10 | Missing `linkedinUrl` | `no-linkedin.fixture.ts` | Icon button not rendered. |

## Runtime / network states

| ID | State | How to trigger | Expected |
|---|---|---|---|
| EC-11 | CMS down | Mock 500 on `/api/cms/hero` | Stale-while-revalidate from last cache; if no cache, render `<noscript>` baseline. |
| EC-12 | Calendly blocked (ad-blocker) | Block `*.calendly.com` | CTA opens mailto fallback `mailto:hello@…?subject=Intro%20call`. |
| EC-13 | CDN portrait 404 | Bad path | Fallback chain runs; `hero_portrait_error` fires. |
| EC-14 | WebGL unavailable | Force `getContext('webgl2') === null` | No particles; gradient remains. |
| EC-15 | `prefers-reduced-motion: reduce` | Chrome DevTools toggle | No particles, no auto-advance, no entrance animation; cross-fade replaces slide. |
| EC-16 | `forced-colors: active` | Windows HC mode | CTAs use `ButtonText` / `Highlight` system colors; portrait outline `1px solid CanvasText`. |
| EC-17 | Slow 3G | Throttle to 400 kbps | LCP image preload still wins; particles defer to idle. |
| EC-18 | JS disabled | `<noscript>` | Static snapshot per `12-threejs-fallback.md` §3. |

## A11y states

| ID | State | Expected |
|---|---|---|
| EC-19 | Tab navigation | Focus order: skip-link → eyebrow → H1 (passthrough) → primary CTA → secondary CTA → carousel prev → dots → next → profile card → LinkedIn. |
| EC-20 | Screen reader | Carousel announces "Slide 2 of 3, Aisha Rahman, Chief Software Engineer" via `aria-live="polite"`. |

## File layout

```
src/components/hero/__fixtures__/
  single-engineer.fixture.ts
  no-engineers.fixture.ts
  ...
src/components/hero/__snapshots__/
  EC-01-1440.png  EC-01-768.png  EC-01-375.png
  ...
```