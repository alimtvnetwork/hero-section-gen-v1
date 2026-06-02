# Phase 23 — Discovery Pass 9 (findings 78–87)

Scope: operational + content governance gaps. No fixes applied.

Each finding: **What / Why blind-AI fails / Fix path / Time**.

---

## 78. Analytics consent replay queue undefined
- **What:** Spec fires `hero_view`/`hero_cta_click` before consent resolves. No buffer/flush rule.
- **Why AI fails:** Either drops pre-consent events (under-reports) or fires them immediately (GDPR violation).
- **Fix:** In-memory queue capped at 50 events, 30s TTL; flush on `consent=granted`, drop on `denied`. Document in Part 6 §64.
- **Time:** **20 min**

## 79. robots.txt / sitemap.xml policy missing
- **What:** No rule for staging `noindex`, sitemap location, or per-route `robots` meta.
- **Why AI fails:** Ships staging indexed to Google, or omits sitemap → SEO target unmet.
- **Fix:** `robots.txt` template per env, `/sitemap.xml` route, `X-Robots-Tag` for preview.
- **Time:** **15 min**

## 80. Canonical URL policy missing
- **What:** No rule for `<link rel="canonical">`, trailing slash, www vs apex, locale variants.
- **Why AI fails:** Duplicate-content penalty; canonical points to preview domain.
- **Fix:** Canonical = prod apex, no trailing slash, `hreflang` table for locales.
- **Time:** **15 min**

## 81. Calendly return + focus restoration undefined
- **What:** After modal close, no rule for focus return, scroll position, or success state.
- **Why AI fails:** Focus lands on `<body>`, screen-reader loses context; A11y audit fails.
- **Fix:** Store trigger ref, restore focus on close, announce success via `aria-live`.
- **Time:** **15 min**

## 82. Mobile viewport units (svh/dvh/lvh) not specified
- **What:** Spec uses `100vh`; iOS Safari URL bar causes jump.
- **Why AI fails:** Hero clips under address bar or overflows on scroll.
- **Fix:** `min-h-[100svh]` for hero, `dvh` for sticky, `lvh` for backdrop; document fallback.
- **Time:** **10 min**

## 83. High-contrast / forced-colors snapshots missing
- **What:** No `forced-colors: active` test case; Windows High Contrast users get invisible CTAs.
- **Why AI fails:** AI ignores forced-colors mode → buttons disappear in HC mode.
- **Fix:** `@media (forced-colors: active)` rules + canonical screenshot in HC mode.
- **Time:** **20 min**

## 84. QA device ownership matrix missing
- **What:** Spec says "test on mobile" but no device list (iOS version, Android version, browser).
- **Why AI fails:** Tests on Chrome desktop only; ships broken on Safari 15.
- **Fix:** Device matrix table: iOS 16+/Safari, Android 12+/Chrome, desktop Chrome/Firefox/Safari/Edge. Owner per row.
- **Time:** **15 min**

## 85. Environment flag matrix missing
- **What:** Flags (`HERO_SAFE_MODE`, `HERO_3D_ENABLED`) have no per-env default table (dev/preview/staging/prod).
- **Why AI fails:** Ships safe-mode ON to prod, or 3D ON to staging where it crashes.
- **Fix:** 4×N matrix of flag × env with defaults + override mechanism.
- **Time:** **15 min**

## 86. Copy source ownership unclear
- **What:** Hero copy (headline, lede, CTAs, testimonial quotes) has no named owner or change-approval flow.
- **Why AI fails:** AI rewrites copy mid-task; legal-sensitive claims ("Top 1%") changed without review.
- **Fix:** `COPY-OWNERS.md` — owner per string, approval path, legal-review trigger keywords.
- **Time:** **15 min**

## 87. Dependency allowlist missing
- **What:** No rule for which npm packages may be added (carousel libs, animation libs, image libs).
- **Why AI fails:** Adds Swiper + Embla + Keen + Splide; bundle bloat; license conflicts.
- **Fix:** Allowlist + denylist with rationale (e.g. "framer-motion ✅, gsap ❌ license").
- **Time:** **15 min**

---

**Phase-23 subtotal:** ~2h 35m raw → **~3h 15m** with buffer.
**Cumulative (Phases 1–22 + 23):** **~33h 20m** to AI-shippable + governance + perf-gated + privacy-aware + runtime-safe + ops-ready spec.

## Remaining digging passes queued (10)

1. A/B testing guardrails (exposure rules, sample-ratio mismatch)
2. Error-boundary copy + retry UX
3. Analytics event schema versioning (`v` prop, breaking-change policy)
4. Data retention policy (analytics, logs, portraits)
5. Localization fallback order (de-AT → de-DE → en)
6. CMS publish rollback procedure
7. Visual QA signoff checklist
8. Accessibility regression matrix (axe rules → fixtures)
9. Third-party outage mode (Calendly/CMS/CDN down)
10. Spec priority labels (P0/P1/P2 across all 87 findings)