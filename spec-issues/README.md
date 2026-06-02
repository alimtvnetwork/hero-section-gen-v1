# Spec Issues — Audit Log

**Scope audited:** `spec/hero-section/` (SPEC.md, SPEC-PART-2..6.md, GAP-ANALYSIS.md).
**Mode:** discovery only — NO fixes applied. Fixes happen one phase at a time when user says "next".
**Last updated:** 2026-06-02 (Phase 25 — discovery pass 11)

## How this folder works

- `README.md` (this file) — running summary + phase index + remaining-passes list.
- `PHASE-XX-<slug>.md` — one file per fix-phase. Contains: scope, files touched, acceptance, est. time.
- We keep phases **small and atomic** (≤ 4 edits each) so one "next" = one green checkmark.
- After each phase ships, mark it ✅ here and append a one-line changelog.

## Headline verdict

**If handed to a blind AI today, this spec would FAIL.**
Confidence the AI ships something close to canonical: **~45%**.
Primary failure drivers (ranked):

1. **Canonical vs SPEC contradiction (P0).** GAP-ANALYSIS.md overrides SPEC.md on at least 7 visual rules (Top 5% vs 1%, italic single-line accent, no flowchart, no logo strip in hero, unframed stat chips, 3-segment carousel bottom-right, `min-h-[760px]`). SPEC.md was never patched. An AI reading top-down builds the wrong design.
2. **Broken image reference (P0).** SPEC.md §preamble and SPEC-PART-2 §16 point to `./01-hero.png`, which has been renamed to `01-hero-variant-archived.png`. Canonical is `00-hero-canonical.png`. AI loads the wrong source of truth.
3. **Stack drift (P1).** SPEC.md uses Tailwind v3 config patterns + raw hex tokens; repo is Tailwind v4 with `@theme` in `src/styles.css` + `oklch()` — explicitly mandated by AGENTS rules. AI either ignores repo conventions or rewrites the design system.
4. **Naming conventions undefined (P1).** Events use `snake_case` (`hero_cta_click`), flags use `SCREAMING_SNAKE` (`HERO_SAFE_MODE`), files use `kebab-case` + ALL-CAPS mixed (`SPEC-PART-2.md`, `GAP-ANALYSIS.md`, `SPEC.md`). No convention doc says which to use where → bikeshedding + inconsistency at PR time.
5. **No single source of truth.** Tokens defined in SPEC §2 *and* Part 5 §57 (Style Dictionary) *and* implied in `src/styles.css`. Three drift surfaces.
6. **No TOC, no version, no acceptance map.** Sections jump from 0–11 (SPEC.md) to 12–24 (Part 2) to 45–64 (Parts 5/6). A reader can't tell what's MVP vs aspirational, what's shipped vs proposed.
7. **Coding-guideline violations inside the spec itself.** Raw `text-white` / `bg-neutral-950` / `#FFFFFF` are recommended in SPEC despite the repo rule "Never write custom color classes in components — always use semantic design tokens."

## Phase plan (atomic, do-as-I-say-next)

Each phase = one focused PR. Estimated wall time in parentheses. Do **not** combine.

| # | Phase | Files touched | Est. |
|---|-------|---------------|------|
| 1 | Promote GAP-ANALYSIS deltas into SPEC.md (single source of truth) | SPEC.md | 25 min |
| 2 | Fix broken image refs (`01-hero.png` → `00-hero-canonical.png`) | SPEC.md, SPEC-PART-2.md | 5 min |
| 3 | Reconcile "Top 1%" vs "Top 5%" everywhere | SPEC.md, SPEC-PART-3.md, SPEC-PART-4.md | 10 min |
| 4 | Add `CONVENTIONS.md` (naming: events, flags, files, CSS vars, components) | spec/hero-section/CONVENTIONS.md | 20 min |
| 5 | Rewrite SPEC §2 tokens to Tailwind v4 + `oklch()` in `src/styles.css` style | SPEC.md | 20 min |
| 6 | Drop archived elements still referenced (Part 2 §17 flowchart, §23 logo strip) | SPEC-PART-2.md | 10 min |
| 7 | Add `00-INDEX.md` (TOC across all 7 spec files, MVP vs aspirational tags) | spec/hero-section/00-INDEX.md | 25 min |
| 8 | Add version + changelog header to SPEC.md (semver from Part 5 §45) | SPEC.md | 10 min |
| 9 | Normalize time format (always `Xh Ym`, no mixed `75 min` / `1h 15m`) | SPEC-PART-2..6 | 15 min |
| 10 | Three.js fallback decision: kill, or add SSR/no-WebGL fallback rule | SPEC.md §0, §11 | 15 min |
| 11 | Cross-ref Calendly (§3) ↔ Consent rendering (Part 6 §64) | SPEC-PART-3.md, SPEC-PART-6.md | 10 min |
| 12 | Replace raw HTML examples with React/TanStack equivalents | SPEC.md examples block | 25 min |
| 13 | De-duplicate token definitions (SPEC §2 = source, Part 5 §57 = export only) | SPEC-PART-5.md | 10 min |
| 14 | Tag every Part 5/6 item as `[MVP]` / `[v1.1]` / `[aspirational]` | SPEC-PART-5.md, SPEC-PART-6.md | 15 min |
| 15 | File-name standardization (`SPEC.md` → `01-SPEC.md`, etc., or all-lowercase) | rename pass | 10 min |
| 16+ | Continue discovery — more passes as we dig | — | — |

**Phase 0 subtotal so far:** ~3h 45m to bring spec to AI-shippable. Remaining digs likely add 2–3 more phases.

## Remaining digging passes (queued — not started)

These are areas I've **not yet audited deeply**; each likely produces 3–8 more issues:

- A11y claims (Part 6 §55 AAA) vs SPEC §3 contrast math — never cross-checked.
- Analytics event payloads vs the GDPR/consent rules in §64 — likely PII leakage in `referrer`/`utm_*`.
- Motion spec (Part 4 §31–33) vs `prefers-reduced-motion` rule — coverage gaps.
- Image-generation reproducibility (Part 5 §52) vs actual prompt+seed stored — currently neither in repo.
- Storybook stories (Part 6 §56) reference props that don't exist in the data schema (Part 3).
- Native parity (Part 6 §58) — token names assume CSS, no platform mapping.
- Edge cases (Part 5 §51) defined but no test fixture file exists.
- Cost model (Part 5 §49) cites PostHog tier but analytics events spec implies volume that exceeds it.
- Disaster recovery (Part 5 §53) references `RUNBOOK.md` — file does not exist.
- Changelog (Part 5 §45) references `CHANGELOG.md` — file does not exist.
- License/legal (Part 5 §52) refers to portrait license snapshots — no folder for them.
- Component contract: there is no single "props shape" doc; schema in Part 3 §host doesn't enumerate all props the visual sections rely on (badge string, hotspot, slide-segment count, etc.).

## Changelog

- 2026-06-02 — Phase 0: created spec-issues folder, headline verdict, 15-phase plan, queued discovery passes.
- 2026-06-02 — Phase 16 (discovery pass 2): logged findings 8–17 in `PHASE-16-discovery-pass-2.md`; queued 14 more digging passes.
- 2026-06-02 — Phase 17 (discovery pass 3): logged findings 18–27 in `PHASE-17-discovery-pass-3.md`; queued 16 more digging passes.
- 2026-06-02 — Phase 18 (discovery pass 4): logged findings 28–37 in `PHASE-18-discovery-pass-4.md`; queued 20 more digging passes.
- 2026-06-02 — Phase 19 (discovery pass 5): logged findings 38–47 in `PHASE-19-discovery-pass-5.md`; queued 20 more digging passes.
- 2026-06-02 — Phase 20 (discovery pass 6): logged findings 48–57 in `PHASE-20-discovery-pass-6.md`; queued 20 more digging passes.
- 2026-06-02 — Phase 21 (discovery pass 7): logged findings 58–67 in `PHASE-21-discovery-pass-7.md`; queued 20 more digging passes.
- 2026-06-02 — Phase 22 (discovery pass 8): logged findings 68–77 in `PHASE-22-discovery-pass-8.md`; queued 20 more digging passes.
- 2026-06-02 — Phase 23 (discovery pass 9): logged findings 78–87 in `PHASE-23-discovery-pass-9.md`; 10 passes remaining in queue.
- 2026-06-02 — Phase 24 (discovery pass 10, final): logged findings 88–97 in `PHASE-24-discovery-pass-10.md`; **discovery queue empty**. 97 findings total.
- 2026-06-02 — Phase 25 (discovery pass 11): opened new dig surface (CI/supply-chain/observability/process); logged findings 98–107 in `PHASE-25-discovery-pass-11.md`; queued 10 new passes.
