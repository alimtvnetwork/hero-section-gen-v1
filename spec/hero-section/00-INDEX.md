# Hero Section — Spec Index (Canonical Entry Point)

**Spec version:** 1.0.0 · **Status:** AI-shippable · **Last updated:** 2026-06-02

> Read this file first. It is the only entry point. If you read SPEC.md before this, stop and start over.

## Reading order (mandatory)

1. **`00-INDEX.md`** (this file) — TOC, status tags, source-of-truth rules.
2. **`CONVENTIONS.md`** — naming (events, flags, files, CSS vars, components).
3. **`TOKENS.md`** — colors, spacing, motion, type. Single source for `src/styles.css`.
4. **`GLOSSARY.md`** — rim light, hot edge, halo, wash, warm side — disambiguated.
5. **`COMPONENT-CONTRACT.md`** — public `HeroProps` API. Authoritative.
6. **`HERO-ANATOMY.md`** — element-by-element map (numbered diagram).
7. **`COPY-BUDGETS.md`** — per-field length limits, i18n-safe.
8. **`I18N.md`** — locale resolution + RTL mirroring.
9. **`IMAGE-MANIFEST.md`** — portrait provenance, prompts, seeds, licenses.
10. **`EDGE-CASES.md`** — fixture catalogue (EC-01..EC-20).
11. **`STATES.md`** — loading / empty / error / offline / slow.
12. **`STORYBOOK-FIXTURES.md`** — PII-safe rules for stories.
13. **`THREEJS-FALLBACK.md`** — SSR/no-WebGL/reduced-motion fallback.
14. **`SPEC.md`** — canvas, layout, typography, copy (visual baseline).
15. **`SPEC-PART-2.md`** — portrait pipeline, image gen, fallbacks.
16. **`SPEC-PART-3.md`** — data schema, SEO, edge cases (legacy — superseded by COMPONENT-CONTRACT/EDGE-CASES for the contract surface).
17. **`SPEC-PART-4.md`** — motion, carousel, scroll.
18. **`SPEC-PART-5.md`** — engineering ops.
19. **`SPEC-PART-6.md`** — a11y AAA, native parity, consent.
20. **`ARCHIVED.md`** — what to NOT build.
21. **`GAP-ANALYSIS.md`** — historical record; **no longer overrides**.
22. **`CHANGELOG.md`** — versioned diff log.

### Visual/a11y/perf supplements (Batch 3)

- **`SNAPSHOTS.md`** — canonical visual-regression matrix.
- **`MOTION.md`** — full-motion + reduced-motion table.
- **`A11Y.md`** — AAA contrast proofs + carousel ARIA + focus order + Calendly focus.
- **`FONTS.md`** — self-hosted WOFF2 + metric matching, CLS=0 rule.
- **`HEAD-META.md`** — root vs leaf head, `color-scheme: dark`, LCP preload.

### Perf / runtime safety / supply chain (Batch 4)

- **`BUDGETS.md`** — Lighthouse CI + bundle/image/font budgets, PR comment template.
- **`CSP.md`** — production CSP, nonce strategy for inline JSON-LD.
- **`SSR-RULES.md`** — hydration-mismatch prevention.
- **`LAZY-LOAD.md`** — load-priority tiers (critical/preload/hydration/idle/interaction/viewport).
- **`CLEANUP.md`** — memory-leak cleanup patterns + CI lint.
- **`VIEWPORT-UNITS.md`** — `svh`/`dvh`/`lvh` rules for iOS Safari.
- **`CDN.md`** — asset path, headers, fallback chain, outage mode.
- **`SUPPLY-CHAIN.md`** — lockfile, audit gate, allow/denylist.
- **`SECRETS.md`** — inventory, rotation, break-glass.
- **`SOURCE-MAPS.md`** — hidden maps + uploaded to reporter.

### Governance + final gate (Batch 5)

- **`CODEOWNERS`** — review routing per path.
- **`COPY-OWNERS.md`** — per-string owner + legal-review triggers.
- **`DEPENDENCIES.md`** — RFC template + allowlist process.
- **`FLAGS.md`** — env flag matrix with retirement dates.
- **`SEO.md`** — robots, sitemap, canonical, hreflang.
- **`CMS-PREVIEW.md`** — preview mode + rollback + kill-switch.
- **`SLO.md`** — SLOs + alerting + pager routing.
- **`RETENTION.md`** — data retention per class.
- **`PRIORITIES.md`** — full P0/P1/P2 triage of all 107 findings.
- **`DEFINITION-OF-DONE.md`** — **the 100/100 ship gate**.

## Spec score

**100 / 100 AI-shippable** when every gate in `DEFINITION-OF-DONE.md` passes for the current release.

## Source of truth (resolves all contradictions)

| Domain | Canonical file | Anything else is stale |
|---|---|---|
| Visual / layout / copy | `SPEC.md` (v1.0.0+) | GAP-ANALYSIS pre-v1.0.0 deltas |
| Color / spacing / motion / type tokens | `TOKENS.md` | SPEC §2, PART-5 §57 |
| Naming (events, flags, files) | `CONVENTIONS.md` | Anywhere else |
| Reference image | `./00-hero-canonical.png` | `./01-hero-variant-archived.png` (archived) |
| Engineer badge copy | `Top 5% Vetted Engineer` | Any `Top 1%` reference |

## Status tags (per section)

Every numbered section in PART-5 and PART-6 must carry one of:
- `[MVP]` — must ship in first release.
- `[v1.1]` — next release; tracked but not blocking.
- `[aspirational]` — future; no current owner.

Untagged sections are treated as `[aspirational]` and ignored by the build gate.

## Definition of Done (link)

A hero PR is shippable when **every gate** in `DEFINITION-OF-DONE.md` passes. Visual match alone is not enough.

## How a blind AI uses this spec

1. Read files in the order above.
2. Treat every `[MVP]` item as required.
3. Use `TOKENS.md` values verbatim in `src/styles.css` — no other color/spacing source.
4. Stop and ask if any two files disagree — that is a spec bug, not a judgment call.