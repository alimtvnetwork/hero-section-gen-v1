# Phase 22 — Discovery Pass 8 (findings 68–77)

**Mode:** discovery only, no fixes. Drawn from the queued passes at end of Phase 21.

---

## 68. Component anatomy doc is missing
**What:** The spec describes many visual pieces — eyebrow, H1, italic accent, stat chips, portrait, rim light, carousel controls, CTAs — but there is no labeled anatomy diagram mapping each visible element to its prop, token, analytics event, and acceptance rule.
**Why fails:** A blind AI cannot tell which visual detail is decorative vs contractually required; it may rename props, collapse layers, or omit analytics hooks because the visual/component boundary is implicit.
**Time:** **20 min** — add `05-hero-anatomy.md` with a numbered screenshot overlay and table: element → prop/data source → token(s) → behavior → test/assertion.

## 69. Canonical snapshots only cover one viewport
**What:** The repo has one canonical hero image, but the spec requires responsive behavior at 1440/1024/768/375. There are no canonical references for tablet or mobile, where layout decisions are most likely to diverge.
**Why fails:** AI optimizes for the desktop screenshot and invents mobile/tablet layouts; reviewers then argue subjectively because no small-screen golden image exists.
**Time:** **20 min** — capture and store canonical snapshots for 1440, 1024, 768, and 375 widths; include crop boundaries, DPR, browser, and acceptable diff tolerance.

## 70. Color-token semantic naming needs an audit
**What:** Tokens are discussed across SPEC, Part 6, and the Tailwind migration notes, but names like `accent`, `accent-strong`, `glow`, `rim`, and `warm` do not clearly separate semantic role from raw color value.
**Why fails:** AI uses the amber token everywhere because it “looks close,” causing one-note visuals and making future brand changes risky.
**Time:** **20 min** — define naming rules: role-based tokens (`hero-cta-bg`, `hero-rim-light`) map to value tokens; ban raw color usage; add a token usage table and examples.

## 71. Lazy-load triggers are not specified
**What:** Calendly, particle canvas, non-current carousel portraits, analytics extras, and below-fold assets all have different loading priorities. The spec says what exists, but not exactly when each loads.
**Why fails:** AI either loads everything at first paint and misses LCP, or defers too aggressively and makes CTA/carousel interactions feel broken.
**Time:** **20 min** — add load-priority matrix: critical, preloaded, idle, interaction-triggered, viewport-triggered; include `requestIdleCallback` fallback and no-JS behavior.

## 72. Hydration mismatch risks are not bounded
**What:** Random particles, CMS fallback ordering, date/time formatting, AB-test flags, and viewport-dependent rendering can differ between SSR and the browser. The spec does not define deterministic seeds or server/client parity rules.
**Why fails:** AI introduces `Math.random()` or `window.innerWidth` during render; React hydration warnings appear, layout shifts, and visual-regression tests become flaky.
**Time:** **25 min** — add SSR determinism rules: seeded randomness, no render-time browser globals, stable CMS sort, locale passed from loader, and hydration-warning acceptance test.

## 73. CSP nonce / hash strategy for inline JSON-LD is absent
**What:** Earlier findings add JSON-LD for SEO, but strict CSP requires a nonce or hash for inline scripts. The embed/security sections mention CSP generally, not how route `head()` scripts receive a nonce.
**Why fails:** AI adds inline JSON-LD that works locally but is blocked under production CSP, or weakens CSP with unsafe-inline.
**Time:** **25 min** — document nonce/hash approach, forbid `unsafe-inline`, define how JSON-LD is serialized safely, and add a production-header verification step.

## 74. Memory-leak cleanup rules are missing
**What:** The hero may use timers for carousel auto-advance, `IntersectionObserver` for analytics/lazy loading, pointer listeners for swipe, and RAF for particles. No teardown contract exists.
**Why fails:** AI wires effects without cleanup; navigation, route preloading, and repeated mounts accumulate timers/listeners and cause duplicate analytics events or battery drain.
**Time:** **20 min** — add lifecycle checklist: clear timers, disconnect observers, cancel RAF, remove listeners, abort pending image preloads, and test remounting twice without duplicate events.

## 75. Dev debug flags are not standardized
**What:** The spec mentions `?grid=1`, but not a complete debug flag matrix for safe mode, slow motion, reduced animation, no analytics, forced consent state, or forced carousel slide.
**Why fails:** Each engineer invents different query params; support/debug links become inconsistent and some flags leak into production behavior.
**Time:** **15 min** — define allowed debug flags, dev-only vs production-safe behavior, precedence rules, and one parser helper with typed outputs.

## 76. Asset CDN strategy is under-specified
**What:** The spec references CDN URLs and immutable cache headers, but does not choose ownership, path structure, purge workflow, image transforms, signed URLs, or cache invalidation after portrait swaps.
**Why fails:** AI hard-codes local assets or third-party URLs; marketing swaps images and old portraits remain cached indefinitely, or cache busting breaks canonical references.
**Time:** **25 min** — add CDN contract: canonical asset host, versioned paths, immutable caching, purge steps, transform parameters, fallback origin, and ownership for credentials.

## 77. Portrait 404 / 500 fallback chain is undefined
**What:** The spec covers missing portrait stories and object-fit behavior, but not runtime failures: CDN 404, 500, slow image, corrupt image, or blocked domain. The visual fallback and analytics event are unspecified.
**Why fails:** AI leaves broken image icons, shifts layout when fallback appears, or loops retries and hurts performance.
**Time:** **20 min** — define fixed aspect-ratio fallback: approved silhouette/initials, one retry max, `hero_portrait_error` event without PII, and no layout shift.

---

## Subtotal Phase 22 (if all 10 promoted to fix phases)

~3h 30m of fix work. Add 25% buffer → **~4h 25m**.

## Cumulative project (Phases 1–15 + 16 + 17 + 18 + 19 + 20 + 21 + 22)

~25h 40m + 4h 25m = **~30h 05m** to a truly AI-shippable + governance + perf-gated + privacy-aware + runtime-safe spec.

## Remaining digging passes (queued — not yet run)

Each likely yields 3–5 more issues:

1. **Analytics consent replay queue** — events before consent need drop vs replay decision.
2. **Robots/sitemap policy** — shareable routes and preview URLs need indexing rules.
3. **Canonical URL policy** — query-param variants (`?print=1`, `?v=1`, preview) need canonical handling.
4. **Calendly return/focus behavior** — modal/deep-link close path and focus restoration are undefined.
5. **Mobile viewport units** — `100vh` vs `svh/dvh` with browser chrome not specified.
6. **High-contrast visual snapshots** — forced-colors is mentioned, but no golden references exist.
7. **QA device ownership** — real-device matrix has no named owner or signoff cadence.
8. **Environment flag matrix** — production/staging/preview feature flags can drift.
9. **Copy source ownership** — CMS vs code literals vs legal-approved copy source is unclear.
10. **Dependency allowlist** — animation/carousel/analytics packages need approval boundaries.
11. **A/B testing guardrails** — experiment variants can violate canonical visual contract.
12. **Error-boundary copy** — loader/CMS failure states need approved fallback text.
13. **Analytics schema versioning** — events need versioned payload contracts.
14. **Data retention policy** — analytics/debug logs retention is not specified.
15. **Localization fallback order** — missing locale strings need deterministic fallback rules.
16. **CMS publish rollback** — bad hero publish rollback process is undefined.
17. **Visual QA signoff checklist** — final human acceptance checklist is scattered.
18. **Accessibility regression matrix** — SR/browser combinations need explicit pass criteria.
19. **Third-party outage mode** — Calendly/analytics/CDN failure behavior needs fallback rules.
20. **Spec priority labels** — P0/P1/P2 severity labels are still informal across files.

---

## Updated Phase Plan additions

Phases 22-A through 22-J — promote individually as user says "next" after Phases 1–15 ship. Phase 23+ to be authored after pass 9.