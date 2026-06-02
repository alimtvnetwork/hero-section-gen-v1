# Phase 19 — Discovery Pass 5 (findings 38–47)

**Mode:** discovery only, no fixes. Drawn from the queued passes at end of Phase 18.

---

## 38. SSR loader skeleton + hydration gap above the fold
**What:** Hero is above-the-fold and SSR'd. Portrait is preloaded (Part 2 §16) but engineer data (carousel) and stats also need to be in the initial HTML. Spec has no rule for: loader returns full hero data → first paint shows real content vs skeleton. Currently §host implies CMS-fetched at request time; no `pendingComponent` shape, no streaming strategy.
**Why fails:** AI ships client-side fetch → blank hero → LCP target (Part 5 §54: LCP ≤ 2.0s) misses. Or ships skeleton that flashes for 200ms = CLS bug.
**Time:** **20 min** — rule: hero data resolved server-side in route loader; no skeleton above the fold; `pendingComponent` only for slow connections (>1s) with same-dimension placeholder.

## 39. Token export pipeline circularity (Style Dictionary ↔ `src/styles.css`)
**What:** Part 5 §57 says Style Dictionary generates CSS vars, Tailwind theme, Swift, Kotlin. SPEC §2 defines CSS vars directly. `src/styles.css` is also a token source per repo convention. Three potential sources → which is upstream? Without an arrow direction declared, every editor changes a different file and they drift.
**Why fails:** AI edits `src/styles.css` directly (fastest); Style Dictionary run later overwrites; mobile tokens stale.
**Time:** **15 min** — single ADR: source = `tokens/tokens.json` (W3C format), `src/styles.css` is *generated*, Tailwind theme is *generated*. Manual edits to generated files = CI fails.

## 40. Portrait dimensions math never stated
**What:** SPEC implies portrait box `aspect-[4/5]` (GAP §portrait-swap-contract), Part 2 §16 says "~1440px tall" at 2x. Math: 1152×1440 (2x), 576×720 (1x). Never written. `srcset` widths (`w=576`, `w=864`, `w=1152`) not enumerated.
**Why fails:** AI exports arbitrary widths; CDN cache fragments; preload `imagesrcset` doesn't match what `<img>` requests → preload wasted, LCP misses.
**Time:** **10 min** — one table: width / DPR / file size budget / WebP+PNG variants.

## 41. Visual-regression ±px tolerance budget missing
**What:** Part 3 §32 Playwright `toHaveScreenshot` at 4 widths. Default `maxDiffPixelRatio` is 0. Anti-aliasing differences across runners (Linux CI vs macOS dev) trigger false failures every 3rd run. No tolerance budget defined.
**Why fails:** Flaky tests → ignored tests → real regressions miss.
**Time:** **10 min** — set `maxDiffPixelRatio: 0.001` (0.1%), `threshold: 0.2` per-pixel; document why; mask known noise regions (particles, carousel progress).

## 42. Currency/locale of stat values
**What:** Stat chips show values like `$2M+`, `50+ engineers`, `99.9% uptime` (implied). `$` is hardcoded; spec has no `Intl.NumberFormat` rule, no per-locale stat copy, no currency-conversion stance.
**Why fails:** EUR/JPY/INR markets see `$` literally → trust hit. Or AI invents conversion (`€1.85M+`) and the number drifts from reality.
**Time:** **15 min** — rule: stats are CMS strings, **not numbers**; copywriters localize per market; no client-side conversion; document why.

## 43. Light-theme stub — kill it or commit
**What:** Part 3 §host says "light-theme stub: provide `data-theme="light"` override with placeholder values + `TODO` comment — do not ship visible". Half-baked: tokens defined, no design, no QA, no toggle. GAP §portrait-container says portrait stays dark even in light theme = double commitment.
**Why fails:** AI either implements full light theme (out-of-scope rework) or strips stub (loses future option). Editors see `TODO` in production code and file bugs.
**Time:** **10 min** — decision: **kill stub for v1**. Add ADR: "dark-only. Light theme = v2 with separate spec." Strip `--bg-light` etc from token table.

## 44. Carousel autoplay interval vs analytics view-event window
**What:** §29 fires `hero_slide_view` after 2s visible. Carousel autoplay interval is never stated (3s? 5s? 7s?). If interval = 3s, view fires on every slide. If = 7s, fires ~half. Funnel math broken.
**Why fails:** AI picks 5s by feel; analytics ratio is meaningless; A/B tests on carousel order untrustworthy.
**Time:** **10 min** — pin interval = **6s** (industry convention; safely > 2s threshold + buffer for slow render); state in Part 2 §24; document the 2s threshold reasoning.

## 45. Skip-link / landmark semantics missing
**What:** Hero is the homepage's first section. Spec uses `<div>`/`<section>` interchangeably; no `<main>` boundary specified; no `aria-labelledby` linking hero to its H1; no "Skip to main content" link defined for keyboard users.
**Why fails:** Screen-reader nav by landmarks lands on unnamed regions; keyboard users tab through every header link to reach content.
**Time:** **15 min** — landmark map: `<header>` (nav) → `<main>` → `<section aria-labelledby="hero-h1">` → ... Skip-link `<a href="#hero-h1" class="sr-only focus:not-sr-only">`. Part 6 §55 a11y bundle absorbs the wiring.

## 46. OG image generation pipeline impossible on Cloudflare Workers
**What:** Part 3 §metadata says og:image is a "1200×630 generated screenshot of this hero". No tool named. Puppeteer/Playwright don't run on Workers (server-runtime rule: no `child_process`, no native binaries). Only viable Workers-compatible options: **Satori** (HTML → SVG → PNG via `@vercel/og`) or **pre-generated static PNG** committed to repo and updated via CI.
**Why fails:** AI installs `puppeteer` → runtime crash on deploy. Or skips OG entirely → sharing shows blank card.
**Time:** **25 min** — pick Satori (`@vercel/og` works on Workers), spec a route `src/routes/api/og.tsx` that renders a JSX template using the same tokens; or commit static 1200×630 PNG and regenerate via CI. Recommend static for v1 (zero runtime cost), Satori v1.1.

## 47. Animation library — `Motion / GSAP` listed interchangeably; pick one
**What:** SPEC §0 says "optional Framer Motion / GSAP". They are not interchangeable: different bundle sizes (Motion for React ~30KB, GSAP ~50KB+ with plugins), different licensing (GSAP commercial restrictions for some plugins), different APIs. Part 4 entrance/hover animations don't say which.
**Why fails:** AI installs both (double bundle); or installs GSAP and triggers a license question at legal review.
**Time:** **15 min** — ADR: **Motion for React** (small, MIT, React-idiomatic). Reserve GSAP for animations Motion can't do (none in this spec). Document the bundle-size budget rationale.

---

## Subtotal Phase 19 (if all 10 promoted to fix phases)

~2h 25m of fix work. Add 25% buffer → **~3h**.

## Cumulative project (Phases 1–15 + 16 + 17 + 18 + 19)

~3h 45m + 3h 25m + 3h 50m + 4h 15m + 3h = **~18h 15m** to a truly AI-shippable spec.

## Remaining digging passes (queued — not yet run)

Each likely yields 3–6 more issues:

1. **Three.js SSR fallback** — canvas browser-only; no spec for placeholder during hydration.
2. **Touch / swipe support on carousel** — mobile users expect swipe; not specified.
3. **`<noscript>` content** — §32 mentions; no actual block content.
4. **Spec ownership / CODEOWNERS** for `spec/hero-section/`.
5. **Glossary** — rim light / hot edge / halo / wash / warm side used interchangeably.
6. **Versioning canonical image** — overwrite vs `00-hero-canonical-v2.png`?
7. **Font loading strategy** — `font-display`, fallback metric matching (CLS=0 needs it).
8. **`<meta name="color-scheme" content="dark">`** for dark-only flash suppression.
9. **JS/CSS bundle budget for hero** — LCP target meaningless without it.
10. **Lighthouse CI integration** — manual runs drift.
11. **Print stylesheet test target** — partially addressed (§14), needs Letter/A4 decision lock.
12. **CMS preview-mode** — Sanity preview pane mentioned (§host); no draft-vs-published rule.
13. **Image format negotiation** — WebP/PNG named; AVIF skipped without rationale (better compression).
14. **Calendly UTM passthrough** — covered in §28; URL-param sanitization rule missing (XSS via `utm_source=<script>`).
15. **Carousel ARIA pattern choice** — "tabs", "carousel", or "feed"? Each has different SR expectations; spec implicitly picks none.
16. **Hero copy length budgets** — H1 hard-wrapped to 3 lines; no character limits for translated languages (German ~30% longer).
17. **TanStack Query staleTime / gcTime** for engineer list — no values stated; defaults usually wrong for CMS data.
18. **Performance budget enforcement** — bundle size goals without CI gate = aspirational only.
19. **Test-data privacy** — Storybook fixtures (§56) use real-looking names; PII-in-Storybook is a common audit finding.
20. **Release cadence** — Part 5 §45 semver, no release cadence (weekly? on-merge?); affects changelog discipline.

---

## Updated Phase Plan additions

Phases 19-A through 19-J — promote individually as user says "next" after Phases 1–15 ship. Phase 20+ to be authored after pass 6.
