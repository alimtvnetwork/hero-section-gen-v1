# Phase 21 — Discovery Pass 7 (findings 58–67)

**Mode:** discovery only, no fixes. Drawn from the queued passes at end of Phase 20.

---

## 58. Print export target is ambiguous — A4 vs Letter
**What:** Part 6 §62 says the hero should render as an A4/Letter landscape PDF, but it never chooses a default paper size, margin model, or acceptance screenshot. A4 and US Letter have different aspect ratios, so one layout cannot be pixel-perfect for both without separate rules.
**Why fails:** A blind AI will tune for its local default printer format; sales decks then clip footer text, crop portraits, or change scale between regions.
**Time:** **15 min** — lock default to one format, add alternate only if required, define `@page size`, safe margins, and one golden screenshot per print target.

## 59. CMS preview mode is undefined
**What:** Part 3 defines CMS-driven hero content, but does not specify draft vs published behavior, preview tokens, cache bypass, or whether unpublished engineer portraits may appear in public preview URLs.
**Why fails:** AI either hard-codes published-only content so editors cannot preview, or exposes draft content publicly through query params.
**Time:** **25 min** — add preview-mode contract: signed preview URL, draft reads only for authenticated editors, no indexing, cache bypass, and visible preview watermark.

## 60. AVIF image format skipped without rationale
**What:** The spec references WebP repeatedly for portraits/CDN delivery, but never states whether AVIF is supported, rejected, or deferred. AVIF usually beats WebP for photographic portraits, but compatibility and encoding time need a decision.
**Why fails:** One AI ships WebP-only and misses byte savings; another adds AVIF without fallback/source ordering and breaks older clients.
**Time:** **15 min** — add image format policy: `avif` first when available, `webp` fallback, JPEG emergency fallback, plus encode-quality and browser-support notes.

## 61. Calendly UTM passthrough has XSS / injection risk
**What:** Part 6 §61 and §64 mention Calendly deep links with `utm_*` and `{referrer}` style values. There is no allowlist, max length, encoding rule, or stripping of script-like input before values enter URLs.
**Why fails:** Blind AI concatenates `location.search` into a Calendly URL; malicious values can poison analytics, break URLs, or create reflected-link issues in downstream embeds.
**Time:** **20 min** — define `sanitizeUtm()` with allowlisted keys, `URLSearchParams`, 100-char value caps, origin/referrer allowlist, and test vectors for hostile input.

## 62. Carousel ARIA pattern choice is missing
**What:** The spec mentions carousel controls, keyboard behavior, and screen-reader needs across multiple parts, but never chooses an ARIA model: tabs, carousel region with buttons, or feed/listbox. Each has different roles and announcements.
**Why fails:** AI mixes `tablist`, `aria-roledescription="carousel"`, live regions, and roving tabindex incorrectly, producing noisy or unusable screen-reader output.
**Time:** **25 min** — choose one pattern, document roles/labels/live-region behavior, define focus retention, and add VoiceOver/NVDA acceptance steps.

## 63. i18n copy-length budgets are not operational
**What:** Storybook references German long headline and Arabic RTL, but the spec has no max lengths for eyebrow, headline, accent phrase, quote, name, role, or CTA text. It also lacks line-height and wrapping rules for RTL scripts.
**Why fails:** Translated copy overflows the hero, collides with portrait art, or gets shrunk ad hoc until hierarchy breaks.
**Time:** **25 min** — add per-field character/line budgets, German +35% and Arabic line-height acceptance cases, truncation rules, and required screenshots at 1440/768/375.

## 64. TanStack Query `staleTime` / `gcTime` not specified
**What:** CMS-backed engineer data is semi-static, but no cache policy exists. Defaults can refetch too often, while overlong caching can hide fresh CMS publishes.
**Why fails:** AI leaves defaults, causing unnecessary network traffic and flicker; or caches forever, making editor publishes appear broken.
**Time:** **15 min** — define query options: stable key, `staleTime` aligned to CMS publish SLA, `gcTime`, preview-mode override, and invalidation trigger after publish webhook.

## 65. Performance budget PR comment template missing
**What:** Phase 20 added the need for Lighthouse CI and budgets, but reviewers still need a standard PR summary that explains pass/fail deltas, asset regressions, and accepted exceptions.
**Why fails:** CI output becomes a wall of numbers; humans ignore it, and budget failures get waived without context.
**Time:** **15 min** — add PR-comment template with before/after LCP, CLS, JS/CSS/image bytes, third-party bytes, failing assertion, owner, and waiver expiry.

## 66. Storybook fixtures may look like real PII
**What:** Part 6 §56 asks for engineer names, quotes, long-headline, RTL, and missing-portrait stories. It does not require fake names, synthetic portraits, or a no-real-customer-data rule.
**Why fails:** AI copies production-looking names/photos into public Storybook or visual-regression snapshots; legal/privacy review flags it late.
**Time:** **20 min** — add fixture policy: synthetic names only, generated portraits or approved assets, no customer/company names, and a lint/checklist item before publishing Storybook.

## 67. Release cadence vs semver discipline is incomplete
**What:** Part 5 §45 references versioning/changelog concepts, but not release cadence, what counts as patch/minor/major for a visual hero, or who approves breaking visual changes.
**Why fails:** Every edit becomes `v1.0.1` or unversioned; embeds/email signatures using `?v=1` drift without a clear breaking-change signal.
**Time:** **15 min** — add cadence rule, semver examples, deprecation window for embed versions, changelog owner, and release checklist.

---

## Subtotal Phase 21 (if all 10 promoted to fix phases)

~3h 15m of fix work. Add 25% buffer → **~4h 05m**.

## Cumulative project (Phases 1–15 + 16 + 17 + 18 + 19 + 20 + 21)

~21h 35m + 4h 05m = **~25h 40m** to a truly AI-shippable + governance + perf-gated + privacy-aware spec.

## Remaining digging passes (queued — not yet run)

Each likely yields 3–5 more issues:

1. **Component anatomy doc** — no labeled diagram mapping visual parts to props/tokens.
2. **Canonical snapshots at 768/375** — only one desktop-ish canonical image exists.
3. **Color-token semantic naming audit** — token names do not clearly encode role vs value.
4. **Lazy-load triggers** — Calendly, particles, and below-fold assets need exact load timing.
5. **Hydration mismatch surface** — random particles, dates, and CMS fallbacks can diverge SSR/client.
6. **CSP `nonce` for inline JSON-LD** — strict CSP needs script nonce/hash handling.
7. **Memory-leak cleanup rules** — observers, timers, RAF, and event listeners need teardown rules.
8. **Dev debug flags** — `?grid=1` exists, but safe-mode/slow-motion/debug flags are not standardized.
9. **Asset CDN strategy** — CDN ownership, cache purge, and image transform rules are absent.
10. **Portrait 404 fallback chain** — missing/500 image behavior is not specified.
11. **Analytics consent replay queue** — events before consent need drop vs replay decision.
12. **Robots/sitemap policy** — shareable routes and preview URLs need indexing rules.
13. **Canonical URL policy** — query-param variants (`?print=1`, `?v=1`, preview) need canonical handling.
14. **Calendly return/focus behavior** — modal/deep-link close path and focus restoration are undefined.
15. **Mobile viewport units** — `100vh` vs `svh/dvh` with browser chrome not specified.
16. **High-contrast visual snapshots** — forced-colors is mentioned, but no golden references exist.
17. **QA device ownership** — real-device matrix has no named owner or signoff cadence.
18. **Environment flag matrix** — production/staging/preview feature flags can drift.
19. **Copy source ownership** — CMS vs code literals vs legal-approved copy source is unclear.
20. **Dependency allowlist** — animation/carousel/analytics packages need approval boundaries.

---

## Updated Phase Plan additions

Phases 21-A through 21-J — promote individually as user says "next" after Phases 1–15 ship. Phase 22+ to be authored after pass 8.