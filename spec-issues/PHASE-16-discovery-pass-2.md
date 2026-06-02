# Phase 16 — Discovery Pass 2 (findings 8–17)

**Mode:** discovery only, no fixes. Each item lists *What*, *Why it breaks blind-AI delivery*, and *Estimated fix time* (when promoted to its own phase).

---

## 8. `prefers-reduced-motion` coverage is partial
**What:** Particle canvas (Part 2 §17), carousel (§24), CTA arrow hover (SPEC §11.6), logo-strip stagger (§11.7) all reference reduced-motion. But Part 4 §31 entrance animations, §33 hover/press micro-interactions, the "Top 1% pill" 4s glow loop, and the rim-light breathing in Part 2 §18 do **not** mention `prefers-reduced-motion`. Part 4 §32 audio uses it as a proxy — confusingly conflates two settings.
**Why fails:** AI implements one global wrapper or none — both wrong. Reduced-motion users either see no motion (kills polish) or full motion (a11y bug).
**Time:** **20 min** — one matrix table: every animation × (motion / reduce / forced-colors / reduced-transparency) → keep / fade / kill.

## 9. Analytics payloads leak PII against the consent rule
**What:** Part 3 §29 events ship `referrer`, `utm_source`, `utm_campaign`, `engineer_slot`. Part 6 §64 says under "consent declined" only one server-side `hero_view` fires with **no personal data, no third-party**. `referrer` and full UTM strings are personal data under GDPR ePrivacy guidance, and `engineer_slot` is third-party (Calendly).
**Why fails:** AI ships the event taxonomy verbatim and the consent guard becomes a lie. Legal risk + Sentry/PostHog ingestion of PII.
**Time:** **25 min** — per-event consent matrix (allowed pre-consent / post-consent / server-only) + redaction rules for referrer & UTM.

## 10. Schema is missing fields the visual sections require
**What:** `Engineer` type (Part 3 §host) has `id, name, role, portraitSrc, portraitAlt, quote, badge?, linkedin?, location?` but the canonical visual needs: `subRole` (Part 1 typography §3), `portraitHotspot` ({x,y} for rim-light side — GAP §4 portrait-swap contract), `quoteShort` (≤25 words per Part 5 §51), and `stats` is on `HeroProps` but the spec implies each engineer can override stats during carousel rotation — undefined.
**Why fails:** AI builds the type, then realizes 4 visual rules can't be expressed; either fakes data or silently drops features.
**Time:** **20 min** — extend type + add JSDoc + acceptance test fixture.

## 11. CTA label drift
**What:** SPEC §5 primary CTA is `"Book a Call"`; Part 3 §28 section heading is `"Book a Call"`; Part 3 §27 i18n example uses `hero.cta.primary`; canonical reference image was never re-checked for the exact label, and GAP-ANALYSIS doesn't lock the string. Secondary CTA `"View Our Work"` similarly unverified against canonical.
**Why fails:** AI ships whatever string it reads first; copy team can't override without a source-of-truth string ID.
**Time:** **10 min** — verify against canonical image; lock copy in CMS schema with `default` values.

## 12. Responsive breakpoints disagree with Tailwind defaults
**What:** SPEC §11 defines 1280 / 1024 / 768 cutoffs. Repo Tailwind v4 default breakpoints are `sm:640, md:768, lg:1024, xl:1280, 2xl:1536`. No mapping table; spec uses arbitrary pixel values in prose. Part 3 §32 Playwright tests `375/768/1024/1440` — 1440 isn't a breakpoint, it's a viewport.
**Why fails:** AI invents custom breakpoints in `src/styles.css`, drifts from convention, or hardcodes `[1280px]` arbitrary classes that break refactors.
**Time:** **15 min** — table mapping SPEC breakpoints → Tailwind tokens (`lg:`, `xl:`); rewrite §11 in token language.

## 13. Referenced files do not exist
**What:** SPEC parts reference `analytics/events.md` (Part 3 §29 twice), `CHANGELOG.md` (Part 5 §45, §32), `RUNBOOK.md` (Part 5 §53), `messages.en.json` (Part 3 §27), `engineer.functions.ts` (implied by Part 3 §host). None exist; no folder structure proposed for them either.
**Why fails:** AI either creates them with hallucinated content or skips entire sections silently.
**Time:** **20 min** — file-manifest table: path → owner phase → empty scaffold OK / required content. Don't create files, just declare the manifest.

## 14. "Print stylesheet" and "letter/A4" conflict
**What:** Part 3 §33 says cap portrait `height 4cm so it fits on letter/A4`. Letter is 8.5×11in (≈21.6×27.9cm), A4 is 21.0×29.7cm — different. A 4cm cap works for both but the spec rounds to one number without saying why. Also `@media print` block isn't budgeted in MVP — should be tagged aspirational.
**Why fails:** Low priority, but the conflict triggers the same kind of "Which page size do we test on?" PR-cycle question for every contributor.
**Time:** **5 min** — one sentence: "Cap = 4cm for both Letter and A4; test on A4 (smaller)."

## 15. Image generation pipeline contradicts canonical
**What:** Part 2 §16 still says source = `01-hero.png` regenerated via SPEC §5.1 — but §5.1 describes the old design (glasses, dark button-up). GAP §1 swapped person. If AI regenerates from §5.1 prompt, it gets the archived person again. Part 5 §52 ("Image-Gen Reproducibility") says store `prompt, model, seed` — none stored for the canonical image.
**Why fails:** Any portrait re-gen produces the wrong person. Reproducibility claim is unfounded.
**Time:** **15 min** — overwrite §5.1 prompt to match canonical; save prompt/model/seed sidecar JSON for `00-hero-canonical.png` (file-level note, not generation).

## 16. `<picture>` + `srcset` markup is HTML-first, not React-first
**What:** Part 2 §16 specifies `<picture>` + `srcset` + `<link rel="preload" imagesrcset>` injection via TanStack `head()`. The repo's TanStack pattern uses route `head()` returning `{ meta, links, scripts }` — `imagesrcset` is a `link` attribute, but spec gives no example object shape. SPEC §13 raw HTML example uses `class=` (not `className=`) and a `[Lucide arrow]` placeholder.
**Why fails:** AI writes plain `<picture>` JSX with `class=` and React warns; or fights the `head()` shape and ships a non-typed object.
**Time:** **20 min** — one canonical React snippet for the portrait + one TanStack `head()` example with preload link.

## 17. Acceptance checklist in SPEC §"final" mixes MVP and aspirational
**What:** SPEC.md final checklist (`[ ]` items L262–265) blends `Lighthouse a11y ≥ 95` (MVP-gate) with `Layout collapses gracefully at 1024, 768, 375` (MVP), but is missing all of Parts 5/6 items even though some (e.g. kill-switch flag, CHANGELOG entry) are clearly launch-blockers per Part 5 §54.
**Why fails:** "Launch gate" is in two places (SPEC.md final + Part 5 §54) with different items. AI launches by checking the smaller list and misses the bigger one.
**Time:** **15 min** — collapse into one launch-gate doc; SPEC.md checklist links to it.

---

## Subtotal Phase 16 (if all 10 promoted to fix phases)

~2h 45m of fix work. Add 25% buffer → **3h 25m**.

## Cumulative project (Phases 1–15 + 16's 10 items)

~3h 45m + 3h 25m = **~7h 10m** to a truly AI-shippable spec.

## Remaining digging passes (queued, not yet run)

Each likely yields 4–8 more issues:

1. **Motion spec (Part 4 §31–33) vs forced-colors / reduced-transparency** — only motion handled, not the other two media queries.
2. **Storybook stories (Part 6 §56)** reference variants for "long name", "missing portrait", "RTL" — but no fixture file proposed (overlaps with #10 but goes wider into stories registry).
3. **Native parity (Part 6 §58)** — token names assume CSS custom properties; no Swift/Kotlin name transform table.
4. **Cost model (Part 5 §49) vs analytics volume** — 6 events × estimated traffic might exceed the PostHog free tier; never cross-multiplied.
5. **Edge cases (Part 5 §51) lack test fixtures** — 5 named cases, 0 fixture data files declared.
6. **Sunset criteria (Part 5 §50) lack ownership** — "conversion drop >25%" but no owner, no review cadence.
7. **Disaster recovery (Part 5 §53)** — kill-switch flag `HERO_SAFE_MODE` is env-driven, but repo runs on Cloudflare Workers; env updates need redeploy. Claim "toggleable without redeploy" is false on this stack.
8. **Embed widget (Part 6 §61) CSP** — claims tightened CSP but never enumerates the directives; iframe sandbox flags unspecified.
9. **Email-signature variant (Part 6 §59)** — Outlook table layout vs the same `<picture>` Part 2 mandates: Outlook doesn't support `<picture>`. Conflict.
10. **In-app WebView (Part 6 §63)** — recommends UA detection; repo has no UA-detection helper and SSR-only UA reads are unreliable on Workers' cache layer.
11. **Legal & portrait release (Part 5 §52)** — references a "license snapshot" folder; no folder, no template form, no signoff workflow.
12. **A/B test (Part 4)** — `useExperiment('hero_2026q3')` is a hook that doesn't exist; no library chosen (PostHog feature flags? GrowthBook? Statsig?).
13. **Versioning of the spec itself** — spec files have no `version:` frontmatter; users can't tell which spec version GAP-ANALYSIS overrides.
14. **i18n string keys (Part 3 §27)** are nested-dot-keyed (`hero.headline.line1`) but headline is described as a single string in CMS schema (Part 3 §host) → key model contradicts data model.

---

## Updated Phase Plan additions

Phases 16–25 (one per finding above) — promote individually as user says "next" after Phases 1–15 ship. Or batch related ones into super-phases when no contradictions overlap.
