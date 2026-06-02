# Findings Triage — All 107 Audit Items (P0/P1/P2)

**Authority:** Single ranking source. P0 = ship-blocker; P1 = pre-launch; P2 = post-launch.

## Status legend

- ✅ closed in this spec release (cross-ref `21-changelog.md`)
- 🟡 doc shipped, implementation pending
- ⬜ not started

## P0 — ship-blockers (must close before any production launch)

| # | Finding | Doc | Status |
|---|---|---|---|
| 1 | GAP-ANALYSIS contradicts SPEC | 13-spec.md / 00-INDEX | ✅ |
| 2 | Broken image ref (`01-hero.png`) | 13-spec.md / 14-spec-part-2.md | ✅ |
| 3 | Top 1% vs Top 5% reconciliation | 13-spec.md / 00-INDEX | ✅ |
| 5 | Token drift (Tailwind v3 vs v4) | 02-tokens.md | ✅ |
| 11 | No component contract / props shape | 04-component-contract.md | ✅ |
| 48 | Three.js SSR fallback missing | 12-threejs-fallback.md | ✅ |
| 54 | Font-loading CLS budget | 25-fonts.md | ✅ |
| 55 | `color-scheme: dark` meta missing | 26-head-meta.md | ✅ |
| 56 | Bundle budgets missing | 27-budgets.md | ✅ |
| 57 | No Lighthouse CI gate | 27-budgets.md | 🟡 |
| 72 | Hydration mismatch risk unbounded | 29-ssr-rules.md | ✅ |
| 73 | CSP nonce for inline JSON-LD | 28-csp.md | ✅ |
| 77 | Portrait 404 fallback chain | 33-cdn.md / 08-image-manifest.md | ✅ |
| 82 | iOS viewport units (svh/dvh) | 32-viewport-units.md | ✅ |
| 86 | Copy ownership unclear (legal claims) | 38-copy-owners.md | ✅ |
| 97 | Priority labels missing | 45-priorities.md (this file) | ✅ |
| 107 | Definition of Done missing | 46-definition-of-done.md | ✅ |

## P1 — pre-launch (must close before public launch / marketing)

| # | Finding | Doc | Status |
|---|---|---|---|
| 4 | Naming conventions undefined | 01-conventions.md | ✅ |
| 6 | Archived elements still referenced | 19-archived.md | ✅ |
| 7 | No TOC / entry point | 00-index.md | ✅ |
| 8 | No version / changelog | 21-changelog.md | ✅ |
| 12–17 | Data schema / copy / i18n / image / edge | COMPONENT-CONTRACT / COPY-BUDGETS / I18N / IMAGE-MANIFEST / EDGE-CASES | ✅ |
| 41 | Motion + reduced-motion coverage | 23-motion.md | ✅ |
| 49 | Carousel swipe / pointer | (impl) | ⬜ |
| 50 | `<noscript>` markup unspecified | 12-threejs-fallback.md §3 | ✅ |
| 51 | 37-codeowners.md missing | 37-codeowners.md | ✅ |
| 52 | Glossary missing | 03-glossary.md | ✅ |
| 53 | Canonical-image versioning | 08-image-manifest.md | ✅ |
| 58–67 | Print / preview / AVIF / Calendly XSS / ARIA / i18n budgets / TanStack Query / PR comment / Storybook PII / release cadence | various | ✅ |
| 68–77 | Anatomy / snapshots / token rename / lazy load / hydration / CSP / cleanup / debug / CDN / portrait | various | ✅ |
| 79 | robots / sitemap | 41-seo.md | ✅ |
| 80 | Canonical URL policy | 41-seo.md | ✅ |
| 81 | Calendly focus restore | 24-a11y.md | ✅ |
| 83 | High-contrast snapshots | 22-snapshots.md | ✅ |
| 84 | QA device matrix | (in DEFINITION-OF-DONE) | 🟡 |
| 85 | Env flag matrix | 40-flags.md | ✅ |
| 87 | Dependency allowlist | 39-dependencies.md / 34-supply-chain.md | ✅ |
| 89 | Error-boundary copy | 10-states.md | ✅ |
| 91 | Data retention | 44-retention.md | ✅ |
| 93 | CMS rollback | 42-cms-preview.md | ✅ |
| 94 | Visual QA signoff | 46-definition-of-done.md | ✅ |
| 95 | A11y regression matrix | 24-a11y.md + 22-snapshots.md | 🟡 |
| 96 | Third-party outage mode | 33-cdn.md / 10-states.md / 09-edge-cases.md | ✅ |
| 98 | CI pipeline contract | (47-ci.md — Batch 6 future) | ⬜ |
| 100 | Secret rotation | 35-secrets.md | ✅ |
| 101 | Supply-chain pinning | 34-supply-chain.md | ✅ |
| 102 | Observability / SLOs | 43-slo.md | ✅ |
| 103 | Source-map upload | 36-source-maps.md | ✅ |
| 107 | Definition of Done | 46-definition-of-done.md | ✅ |

## P2 — post-launch (track, not blocking)

| # | Finding | Doc | Status |
|---|---|---|---|
| 9  | Time-format normalization | (sweep complete) | ✅ |
| 10 | Three.js decision | 12-threejs-fallback.md | ✅ |
| 18–47 | Discovery passes 3–5 items (analytics payload PII, native parity, cost model, etc.) | various | mix ✅/🟡/⬜ |
| 78 | Analytics consent replay queue | (Batch 6 candidate) | ⬜ |
| 88 | A/B testing guardrails | 40-flags.md (partial) | 🟡 |
| 90 | Analytics schema versioning | (Batch 6) | ⬜ |
| 92 | Localization fallback order | 07-i18n.md | ✅ |
| 99 | Preview deploy URL convention | 42-cms-preview.md / 41-seo.md | ✅ |
| 104 | License/attribution file | (Batch 6) | ⬜ |
| 105 | Postmortem template | 43-slo.md (ref'd) | 🟡 |
| 106 | Onboarding doc | 00-index.md (partial) | 🟡 |

## Summary

- **P0:** 17 items, **all ✅ except #57 🟡** (LH config shipped; CI wiring to repo pending).
- **P1:** 40 items, **38 ✅ / 4 🟡 / 1 ⬜** (carousel swipe impl, a11y matrix wiring, CI pipeline contract).
- **P2:** 50 items, mix; none blocking ship.

## Re-triage cadence

- Weekly during pre-launch.
- Monthly post-launch.
- On any new finding: triage assigned within 2 business days.