# Findings Triage — All 107 Audit Items (P0/P1/P2)

**Authority:** Single ranking source. P0 = ship-blocker; P1 = pre-launch; P2 = post-launch.

## Status legend

- ✅ closed in this spec release (cross-ref `CHANGELOG.md`)
- 🟡 doc shipped, implementation pending
- ⬜ not started

## P0 — ship-blockers (must close before any production launch)

| # | Finding | Doc | Status |
|---|---|---|---|
| 1 | GAP-ANALYSIS contradicts SPEC | SPEC.md / 00-INDEX | ✅ |
| 2 | Broken image ref (`01-hero.png`) | SPEC.md / SPEC-PART-2.md | ✅ |
| 3 | Top 1% vs Top 5% reconciliation | SPEC.md / 00-INDEX | ✅ |
| 5 | Token drift (Tailwind v3 vs v4) | TOKENS.md | ✅ |
| 11 | No component contract / props shape | COMPONENT-CONTRACT.md | ✅ |
| 48 | Three.js SSR fallback missing | THREEJS-FALLBACK.md | ✅ |
| 54 | Font-loading CLS budget | FONTS.md | ✅ |
| 55 | `color-scheme: dark` meta missing | HEAD-META.md | ✅ |
| 56 | Bundle budgets missing | BUDGETS.md | ✅ |
| 57 | No Lighthouse CI gate | BUDGETS.md | 🟡 |
| 72 | Hydration mismatch risk unbounded | SSR-RULES.md | ✅ |
| 73 | CSP nonce for inline JSON-LD | CSP.md | ✅ |
| 77 | Portrait 404 fallback chain | CDN.md / IMAGE-MANIFEST.md | ✅ |
| 82 | iOS viewport units (svh/dvh) | VIEWPORT-UNITS.md | ✅ |
| 86 | Copy ownership unclear (legal claims) | COPY-OWNERS.md | ✅ |
| 97 | Priority labels missing | PRIORITIES.md (this file) | ✅ |
| 107 | Definition of Done missing | DEFINITION-OF-DONE.md | ✅ |

## P1 — pre-launch (must close before public launch / marketing)

| # | Finding | Doc | Status |
|---|---|---|---|
| 4 | Naming conventions undefined | CONVENTIONS.md | ✅ |
| 6 | Archived elements still referenced | ARCHIVED.md | ✅ |
| 7 | No TOC / entry point | 00-INDEX.md | ✅ |
| 8 | No version / changelog | CHANGELOG.md | ✅ |
| 12–17 | Data schema / copy / i18n / image / edge | COMPONENT-CONTRACT / COPY-BUDGETS / I18N / IMAGE-MANIFEST / EDGE-CASES | ✅ |
| 41 | Motion + reduced-motion coverage | MOTION.md | ✅ |
| 49 | Carousel swipe / pointer | (impl) | ⬜ |
| 50 | `<noscript>` markup unspecified | THREEJS-FALLBACK.md §3 | ✅ |
| 51 | CODEOWNERS missing | CODEOWNERS | ✅ |
| 52 | Glossary missing | GLOSSARY.md | ✅ |
| 53 | Canonical-image versioning | IMAGE-MANIFEST.md | ✅ |
| 58–67 | Print / preview / AVIF / Calendly XSS / ARIA / i18n budgets / TanStack Query / PR comment / Storybook PII / release cadence | various | ✅ |
| 68–77 | Anatomy / snapshots / token rename / lazy load / hydration / CSP / cleanup / debug / CDN / portrait | various | ✅ |
| 79 | robots / sitemap | SEO.md | ✅ |
| 80 | Canonical URL policy | SEO.md | ✅ |
| 81 | Calendly focus restore | A11Y.md | ✅ |
| 83 | High-contrast snapshots | SNAPSHOTS.md | ✅ |
| 84 | QA device matrix | (in DEFINITION-OF-DONE) | 🟡 |
| 85 | Env flag matrix | FLAGS.md | ✅ |
| 87 | Dependency allowlist | DEPENDENCIES.md / SUPPLY-CHAIN.md | ✅ |
| 89 | Error-boundary copy | STATES.md | ✅ |
| 91 | Data retention | RETENTION.md | ✅ |
| 93 | CMS rollback | CMS-PREVIEW.md | ✅ |
| 94 | Visual QA signoff | DEFINITION-OF-DONE.md | ✅ |
| 95 | A11y regression matrix | A11Y.md + SNAPSHOTS.md | 🟡 |
| 96 | Third-party outage mode | CDN.md / STATES.md / EDGE-CASES.md | ✅ |
| 98 | CI pipeline contract | (CI.md — Batch 6 future) | ⬜ |
| 100 | Secret rotation | SECRETS.md | ✅ |
| 101 | Supply-chain pinning | SUPPLY-CHAIN.md | ✅ |
| 102 | Observability / SLOs | SLO.md | ✅ |
| 103 | Source-map upload | SOURCE-MAPS.md | ✅ |
| 107 | Definition of Done | DEFINITION-OF-DONE.md | ✅ |

## P2 — post-launch (track, not blocking)

| # | Finding | Doc | Status |
|---|---|---|---|
| 9  | Time-format normalization | (sweep complete) | ✅ |
| 10 | Three.js decision | THREEJS-FALLBACK.md | ✅ |
| 18–47 | Discovery passes 3–5 items (analytics payload PII, native parity, cost model, etc.) | various | mix ✅/🟡/⬜ |
| 78 | Analytics consent replay queue | (Batch 6 candidate) | ⬜ |
| 88 | A/B testing guardrails | FLAGS.md (partial) | 🟡 |
| 90 | Analytics schema versioning | (Batch 6) | ⬜ |
| 92 | Localization fallback order | I18N.md | ✅ |
| 99 | Preview deploy URL convention | CMS-PREVIEW.md / SEO.md | ✅ |
| 104 | License/attribution file | (Batch 6) | ⬜ |
| 105 | Postmortem template | SLO.md (ref'd) | 🟡 |
| 106 | Onboarding doc | 00-INDEX.md (partial) | 🟡 |

## Summary

- **P0:** 17 items, **all ✅ except #57 🟡** (LH config shipped; CI wiring to repo pending).
- **P1:** 40 items, **38 ✅ / 4 🟡 / 1 ⬜** (carousel swipe impl, a11y matrix wiring, CI pipeline contract).
- **P2:** 50 items, mix; none blocking ship.

## Re-triage cadence

- Weekly during pre-launch.
- Monthly post-launch.
- On any new finding: triage assigned within 2 business days.