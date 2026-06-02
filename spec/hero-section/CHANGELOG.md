## 1.3.0 — 2026-06-02 (Batch 4, steps 31–40)

### Added
- `BUDGETS.md` — Lighthouse CI config, bundle/image/font budgets, PR comment template.
- `CSP.md` — production CSP with per-request nonce for inline JSON-LD.
- `SSR-RULES.md` — deterministic SSR + hydration-mismatch elimination.
- `LAZY-LOAD.md` — 6-tier load priority matrix.
- `CLEANUP.md` — memory-leak teardown patterns + CI lint.
- `VIEWPORT-UNITS.md` — `svh`/`dvh`/`lvh` for iOS Safari.
- `CDN.md` — path/headers/purge/fallback contract.
- `SUPPLY-CHAIN.md` — lockfile + audit + allowlist/denylist.
- `SECRETS.md` — full inventory with rotation cadence and break-glass.
- `SOURCE-MAPS.md` — hidden + uploaded source-map policy.

### Findings closed
- #48 (Three.js fallback cross-link complete) · #56 (bundle budgets) · #57 (Lighthouse CI)
- #71 (lazy-load triggers) · #72 (hydration determinism) · #73 (CSP nonce JSON-LD) · #74 (memory leak cleanup)
- #76 (CDN strategy) · #77 (portrait fallback chain) · #82 (svh/dvh/lvh) · #100 (secret rotation) · #101 (supply-chain pinning) · #103 (source-map upload)
## 1.2.0 — 2026-06-02 (Batch 3, steps 21–30)

### Added
- `SNAPSHOTS.md` — visual-regression matrix (4 viewports × locales/motion/HC/print) + state + EC fixtures.
- `MOTION.md` — full + reduced-motion behaviour for every animated element.
- `A11Y.md` — AAA contrast proofs computed from TOKENS.md, carousel ARIA pattern, focus order, Calendly focus restoration.
- `FONTS.md` — self-hosted WOFF2, metric matching, CLS=0 budget.
- `HEAD-META.md` — root vs leaf head split, `color-scheme: dark`, LCP portrait preload.

### Token rename
- Color tokens already use role-based names (`--color-accent`, `--color-text-muted`) per `TOKENS.md`. Audit confirmed in this release: no raw color-name tokens remain.

### Findings closed
- #41 (motion+reduced-motion) · #48 (Three.js fallback — cross-linked) · #54 (font CLS) · #55 (color-scheme) · #62 (carousel ARIA) · #69 (canonical snapshots 4 viewports) · #70 (token semantic rename — audit only) · #81 (Calendly focus restore) · #83 (forced-colors snapshot)
## 1.1.0 — 2026-06-02 (Batch 2, steps 11–20)

### Added
- `COMPONENT-CONTRACT.md` — authoritative HeroProps + invariants.
- `HERO-ANATOMY.md` — numbered diagram + element→prop/token/event map.
- `COPY-BUDGETS.md` — per-field length limits with DE/AR variants.
- `I18N.md` — locale fallback chain + RTL mirroring rules.
- `IMAGE-MANIFEST.md` — portrait provenance, prompts, seeds, licenses.
- `EDGE-CASES.md` — 20 fixtures (EC-01..EC-20).
- `STATES.md` — loading, empty, error, offline, slow with canonical copy.
- `STORYBOOK-FIXTURES.md` — PII-safe rules.
- `GLOSSARY.md` — rim/halo/wash/hot edge disambiguation.

### Findings closed
- #11 component-contract gap · #12–17 data/schema/copy/i18n/image/edge gaps
- #52 glossary · #66 Storybook PII · #68 component anatomy
# Hero Section Spec — Changelog

Format: [semver](https://semver.org). Breaking visual or schema changes → MAJOR. Additive sections → MINOR. Typo/clarification → PATCH.

## 1.0.0 — 2026-06-02

**First AI-shippable cut. Closes Phase 0 audit, batch 1 (steps 1–10).**

### Added
- `00-INDEX.md` — canonical entry point, reading order, source-of-truth table.
- `CONVENTIONS.md` — naming rules for events, flags, files, CSS vars, components.
- `TOKENS.md` — single source for color/space/motion/type tokens, Tailwind v4 + oklch.
- `CHANGELOG.md` — this file.
- `THREEJS-FALLBACK.md` — SSR/no-WebGL/reduced-motion fallback decision and rule.
- `ARCHIVED.md` — explicit list of spec sections that are no longer in scope.

### Changed
- `SPEC.md`: image reference `./01-hero.png` → `./00-hero-canonical.png`.
- `SPEC-PART-2.md`: same image-ref fix.
- `SPEC.md`, `SPEC-PART-3.md`, `SPEC-PART-4.md`: badge copy unified on `Top 5% Vetted Engineer` (matches canonical reference image).
- `SPEC.md` header now points to `00-INDEX.md` as the entry point and declares semver/version.

### Deprecated (still in repo for history)
- GAP-ANALYSIS.md as an override layer. All deltas promoted into SPEC.md.
- SPEC-PART-2.md §17 (flowchart) and §23 (in-hero logo strip) — see `ARCHIVED.md`.
- Tailwind v3 token block in SPEC §2 — superseded by `TOKENS.md`.

### Findings closed (from `spec-issues/`)
- #2 broken image ref · #3 Top 5%/1% reconciliation · #4 conventions undefined
- #5 token drift · #6 archived elements · #7 no TOC · #8 no version/changelog
- #9 time-format normalization (in progress, see Phase 1 below) · #10 Three.js fallback

### Time-format normalization
All spec time estimates now use `Xh Ym` (e.g. `1h 15m`), never `75 min`. Sweep applied across PART-2..6 in this release. Lint rule lives in `CONVENTIONS.md` future revision.