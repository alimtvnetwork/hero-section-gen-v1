# Phase 18 — Discovery Pass 4 (findings 28–37)

**Mode:** discovery only, no fixes. Drawn from the queued passes at end of Phase 17 plus new gaps surfaced while drilling each item.

---

## 28. Native parity (Part 6 §58) — no Swift/Kotlin token-name transform table
**What:** §58 promises tokens, content schema, Lottie usage, analytics naming will be platform-agnostic. CSS uses `--accent-strong` (kebab-case). Swift idiom is `Color.accentStrong` (PascalCase nested), Kotlin idiom is `AccentStrong` or `accent_strong`. No mapping table; Style Dictionary (§57) named as the tool but its transform config is unspecified.
**Why fails:** AI generating mobile tokens picks one casing; design-system PR review pings back; lost half a day per platform.
**Time:** **20 min** — one transform table: token category → CSS / Tailwind / Swift / Kotlin name pattern + 3 worked examples.

## 29. In-app WebView UA detection — no helper, Workers SSR UA unreliable
**What:** Part 6 §63 says "UA detection" for Instagram/FB/TikTok/X browsers. Repo has no UA-parser dependency. SSR on Cloudflare Workers can read `request.headers.get('user-agent')`, but the published edge cache strips `Vary: User-Agent` by default — same cache entry served to every UA. Client-side detection runs after hydration → flash of wrong UI.
**Why fails:** AI installs `ua-parser-js` (heavy), bundles it client-side (adds ~20KB), and either hits cache-poisoning bugs or detection comes too late.
**Time:** **25 min** — pick approach: lightweight regex helper (`isInAppBrowser(ua)`), run server-side, set `Vary: User-Agent` on the hero route only. Document the cache trade-off.

## 30. Portrait license / model-release folder + signoff workflow missing
**What:** Part 5 §52 references "license snapshots" and "human-in-the-loop sign-off" for portrait reproducibility. No `licenses/` folder, no model-release PDF template, no signoff log. For real engineers (not AI-generated portraits), GDPR Art. 6(1)(a) requires explicit, revocable, written consent for portrait use in marketing.
**Why fails:** Legal reviews block launch; or worse, ship without consent and an engineer requests takedown post-launch.
**Time:** **25 min** — folder structure (`licenses/{engineerId}.pdf`), template PDF (1-pager), checklist field in CMS (`engineer.consentSignedOn: date`), takedown SLA (24h).

## 31. Storybook decorators registry — named but not specified
**What:** Part 6 §56 names decorators `Reduced motion`, `High contrast`, `RTL`, `Mobile`, `Loading`. No `.storybook/decorators.ts` proposed. Decorators that flip media queries (reduced-motion, forced-colors) need a wrapping component injecting `@media` overrides, not simple CSS class flips.
**Why fails:** AI writes 5 decorators inline per story (drift); or uses `@storybook/addon-styling` differently in each.
**Time:** **20 min** — single `decorators.ts` file: one factory per axis (`withMediaQuery`, `withDir`, `withViewport`), exports composed `globalTypes` for Storybook toolbar.

## 32. TanStack route conventions for hero — not in spec
**What:** SPEC implies hero lives at `/` (it's the homepage). No spec on: `head()` shape (canonical, OG, structured data), loader strategy (where engineer list comes from), suspense boundary placement, error/notFound components, preload behavior, route `staleTime`. Repo convention: TanStack Query + `ensureQueryData` in loader.
**Why fails:** AI builds the hero as a leaf component and the home route file is half-written; SEO meta lives in the wrong place; preview fetches happen on hover (default), wasting requests.
**Time:** **25 min** — one route-shape doc: `src/routes/index.tsx` skeleton with `head()`, `loader`, `pendingComponent`, `errorComponent`, `notFoundComponent`, `staleTime`.

## 33. Image alt-text copy rules absent
**What:** Schema has `portraitAlt: string` (Part 3 §host). No rule on content. WCAG: avoid "photo of", "picture of"; describe person + context relevant to surrounding copy. CMS validation can't enforce style.
**Why fails:** Marketing fills alt with "engineer.jpg" or "Photo of John"; screen-reader users hear redundant "photo of … image".
**Time:** **10 min** — CMS field description + 3 good/bad examples + a max-length 125 chars validator.

## 34. Carousel keyboard map + focus-trap rules missing
**What:** Part 2 §24 says "keyboard support." Doesn't say: Arrow Left/Right? Home/End to jump first/last? PgUp/PgDn? Tab order through 3 segments? Where does focus go after slide change (preserve on dot, or jump to new content)? No focus-trap (carousel isn't a dialog so probably shouldn't trap — but spec is silent).
**Why fails:** AI wires `←/→`, ships; screen-reader user tabs through and focus is dropped on slide change → orientation lost.
**Time:** **20 min** — key map table + focus-retention rule + ARIA roving-tabindex pattern reference.

## 35. SEO JSON-LD missing
**What:** Part 3 §metadata covers `<title>`, meta description, OG, Twitter card. Zero JSON-LD. Hero is the homepage; expected `Organization` (brand), `Service` (offshore engineering), `Person` (featured engineer with `jobTitle`, `worksFor`). Google rich-result eligibility lost.
**Why fails:** SEO audit flags missing structured data; rich snippets unavailable; competitors with JSON-LD outrank.
**Time:** **25 min** — three JSON-LD blocks (Organization, Service, Person from carousel data); inject via TanStack `head().scripts`.

## 36. SLO table missing (observability has alerts without targets)
**What:** Part 5 §48 alerts: LCP regression, image-404, Calendly failure. No SLO targets — what LCP P75 triggers what alert? What error-rate budget? What Calendly success-rate floor? Alerts without SLOs = noise.
**Why fails:** Either over-alerts (P3 noise during normal traffic dips) or under-alerts (real regression below alert threshold).
**Time:** **20 min** — 4-row SLO table: metric / target / measurement window / burn-rate alert / page-on rule.

## 37. Auth-gated CTA flow undefined
**What:** Calendly default is anonymous booking, but some accounts require sign-in or pre-fill from auth. Spec assumes anonymous. If product later enables "engineer can see who booked" (requires auth), CTA flow is: click → check auth → if anon, redirect to `/auth` with `redirect=/?cta=book` → after auth, re-open Calendly. None of this in spec; `_authenticated` route gate (repo convention) not referenced.
**Why fails:** Day-2 requirement breaks the entire CTA path. Better to spec the contract now (even if anonymous v1).
**Time:** **15 min** — one section: "CTA is anonymous in v1. If gated in v1.1: flow X." Locks the contract.

---

## Subtotal Phase 18 (if all 10 promoted to fix phases)

~3h 25m of fix work. Add 25% buffer → **~4h 15m**.

## Cumulative project (Phases 1–15 + 16 + 17 + 18)

~3h 45m + 3h 25m + 3h 50m + 4h 15m = **~15h 15m** to AI-shippable + governance-ready + a11y-complete.

## Remaining digging passes (queued — not yet run)

Each likely yields 4–8 more issues:

1. **SSR loader skeleton + hydration gap** above the fold — no spec for loading state during portrait fetch.
2. **Token export pipeline circularity** — Style Dictionary (§57) generates CSS vars, but `src/styles.css` is where tokens live. Which is upstream?
3. **Portrait dimensions math** — `~1440px tall` × aspect 4/5 = 1152×1440, never stated; srcset 1x/2x widths not enumerated.
4. **Visual regression tolerance** — "pixel-faithful at 1440×900" but no ±px budget; flaky tests guaranteed.
5. **Currency/locale of stat values** — `$2M+` hardcoded; no Intl.NumberFormat strategy for EUR/JPY markets.
6. **Light-theme stub** (§host) — half-baked; kill it loudly or commit to it.
7. **Carousel autoplay timing** — interval not stated (3s? 5s? 7s?); affects analytics view-event threshold (§29 uses 2s).
8. **Skip-link / landmark order** — hero is a `<section aria-labelledby="hero-h1">`? No landmark spec; screen-reader nav broken.
9. **OG image generation** — Part 3 §metadata says "1200×630 generated screenshot"; no generation pipeline (Puppeteer? Satori? ImageGen?). Puppeteer doesn't run on Workers (server-runtime rule).
10. **Lighthouse CI** — §54 launch gate cites Lighthouse but no CI integration spec; manual runs drift.
11. **Bundle budget** — "≤500KB" only mentioned for PDF (§60); no JS/CSS budget for the hero itself; LCP targets meaningless without it.
12. **Font loading strategy** — General Sans / Satoshi cited; no `font-display`, no `preload`, no fallback metric-matching to prevent CLS (rule says CLS=0).
13. **Color-scheme meta** — `<meta name="color-scheme" content="dark">` required for dark-only sites to suppress browser-chrome flash; not specified.
14. **Animation library decision** — SPEC §0 says "Framer Motion / GSAP" as if interchangeable; pick one (Motion for React) or document both.
15. **Three.js fallback for SSR** — particles use canvas (browser-only); SSR renders nothing; no spec for placeholder during hydration.
16. **Touch / pointer events on carousel** — swipe support? No spec. Mobile users expect swipe.
17. **`<noscript>` content** — §32 mentions; no actual `<noscript>` block content spec.
18. **Spec ownership / RACI** — who reviews changes? Who can merge? No 37-codeowners.md for `spec/01-rise-of-asia/01-homepage-hero-section/`.
19. **Glossary** — "rim light", "hot edge", "halo", "amber wash", "warm side" used interchangeably; reader confusion.
20. **Versioning of canonical image** — if marketing swaps the portrait, does `00-hero-canonical.png` get overwritten or versioned (`00-hero-canonical-v2.png`)?

---

## Updated Phase Plan additions

Phases 18-A through 18-J (one per finding above) — promote individually as user says "next" after Phases 1–15 ship. Phase 19+ to be authored after pass 5.
