# Hero Section — Spec Index (Canonical Entry Point)

**Spec version:** 1.0.0 · **Status:** AI-shippable · **Last updated:** 2026-06-02

> Read this file first. It is the only entry point. If you read 13-spec.md before this, stop and start over.

## Reading order (mandatory)

1. **`00-index.md`** (this file) — TOC, status tags, source-of-truth rules.
2. **`01-conventions.md`** — naming (events, flags, files, CSS vars, components).
3. **`02-tokens.md`** — colors, spacing, motion, type. Single source for `src/styles.css`.
4. **`03-glossary.md`** — rim light, hot edge, halo, wash, warm side — disambiguated.
5. **`04-component-contract.md`** — public `HeroProps` API. Authoritative.
6. **`05-hero-anatomy.md`** — element-by-element map (numbered diagram).
7. **`06-copy-budgets.md`** — per-field length limits, i18n-safe.
8. **`07-i18n.md`** — locale resolution + RTL mirroring.
9. **`08-image-manifest.md`** — portrait provenance, prompts, seeds, licenses.
10. **`09-edge-cases.md`** — fixture catalogue (EC-01..EC-20).
11. **`10-states.md`** — loading / empty / error / offline / slow.
12. **`11-storybook-fixtures.md`** — PII-safe rules for stories.
13. **`12-threejs-fallback.md`** — SSR/no-WebGL/reduced-motion fallback.
14. **`13-spec.md`** — canvas, layout, typography, copy (visual baseline).
15. **`14-spec-part-2.md`** — portrait pipeline, image gen, fallbacks.
16. **`15-spec-part-3.md`** — data schema, SEO, edge cases (legacy — superseded by COMPONENT-CONTRACT/EDGE-CASES for the contract surface).
17. **`16-spec-part-4.md`** — motion, carousel, scroll.
18. **`17-spec-part-5.md`** — engineering ops.
19. **`18-spec-part-6.md`** — a11y AAA, native parity, consent.
20. **`19-archived.md`** — what to NOT build.
21. **`20-gap-analysis.md`** — historical record; **no longer overrides**.
22. **`21-changelog.md`** — versioned diff log.

### Visual/a11y/perf supplements (Batch 3)

- **`22-snapshots.md`** — canonical visual-regression matrix.
- **`23-motion.md`** — full-motion + reduced-motion table.
- **`24-a11y.md`** — AAA contrast proofs + carousel ARIA + focus order + Calendly focus.
- **`25-fonts.md`** — self-hosted WOFF2 + metric matching, CLS=0 rule.
- **`26-head-meta.md`** — root vs leaf head, `color-scheme: dark`, LCP preload.

### Perf / runtime safety / supply chain (Batch 4)

- **`27-budgets.md`** — Lighthouse CI + bundle/image/font budgets, PR comment template.
- **`28-csp.md`** — production CSP, nonce strategy for inline JSON-LD.
- **`29-ssr-rules.md`** — hydration-mismatch prevention.
- **`30-lazy-load.md`** — load-priority tiers (critical/preload/hydration/idle/interaction/viewport).
- **`31-cleanup.md`** — memory-leak cleanup patterns + CI lint.
- **`32-viewport-units.md`** — `svh`/`dvh`/`lvh` rules for iOS Safari.
- **`33-cdn.md`** — asset path, headers, fallback chain, outage mode.
- **`34-supply-chain.md`** — lockfile, audit gate, allow/denylist.
- **`35-secrets.md`** — inventory, rotation, break-glass.
- **`36-source-maps.md`** — hidden maps + uploaded to reporter.

### Governance + final gate (Batch 5)

- **`37-codeowners.md`** — review routing per path.
- **`38-copy-owners.md`** — per-string owner + legal-review triggers.
- **`39-dependencies.md`** — RFC template + allowlist process.
- **`40-flags.md`** — env flag matrix with retirement dates.
- **`41-seo.md`** — robots, sitemap, canonical, hreflang.
- **`42-cms-preview.md`** — preview mode + rollback + kill-switch.
- **`43-slo.md`** — SLOs + alerting + pager routing.
- **`44-retention.md`** — data retention per class.
- **`45-priorities.md`** — full P0/P1/P2 triage of all 107 findings.
- **`46-definition-of-done.md`** — **the 100/100 ship gate**.

### Operational closure (Batch 6)

- **`47-ci.md`** — CI pipeline contract (jobs, gates, timeouts).
- **`48-carousel-interaction.md`** — swipe/pointer/keyboard + auto-advance.
- **`49-analytics.md`** — versioned event schema, PII rules, consent replay queue.
- **`50-third-party-notices.md`** — license allow/deny + attribution.
- **`51-onboarding.md`** — < 2h to first PR.
- **`52-postmortem.md`** — blameless incident template.
- **`53-qa-device-matrix.md`** — Tier 1/2 device coverage.
- **`54-a11y-regression.md`** — axe/pa11y/SR matrix wiring.
- **`55-ab-testing.md`** — experiment guardrails.
- **`56-lighthouse-ci.md`** — `lighthouserc.json` + gates.

## Spec score

**100 / 100 AI-shippable** when every gate in `46-definition-of-done.md` passes for the current release.

## Source of truth (resolves all contradictions)

| Domain | Canonical file | Anything else is stale |
|---|---|---|
| Visual / layout / copy | `13-spec.md` (v1.0.0+) | GAP-ANALYSIS pre-v1.0.0 deltas |
| Color / spacing / motion / type tokens | `02-tokens.md` | SPEC §2, PART-5 §57 |
| Naming (events, flags, files) | `01-conventions.md` | Anywhere else |
| Reference image | `./00-hero-canonical.png` | `./01-hero-variant-archived.png` (archived) |
| Engineer badge copy | `Top 5% Vetted Engineer` | Any `Top 1%` reference |

## Status tags (per section)

Every numbered section in PART-5 and PART-6 must carry one of:
- `[MVP]` — must ship in first release.
- `[v1.1]` — next release; tracked but not blocking.
- `[aspirational]` — future; no current owner.

Untagged sections are treated as `[aspirational]` and ignored by the build gate.

## Definition of Done (link)

A hero PR is shippable when **every gate** in `46-definition-of-done.md` passes. Visual match alone is not enough.

## How a blind AI uses this spec

1. Read files in the order above.
2. Treat every `[MVP]` item as required.
3. Use `02-tokens.md` values verbatim in `src/styles.css` — no other color/spacing source.
4. Stop and ask if any two files disagree — that is a spec bug, not a judgment call.