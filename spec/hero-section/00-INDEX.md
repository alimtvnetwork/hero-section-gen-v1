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