# Phase 17 — Discovery Pass 3 (findings 18–27)

**Mode:** discovery only, no fixes. Pulled from the queued passes at end of Phase 16, plus new gaps surfaced while drilling.

---

## 18. `HERO_SAFE_MODE` "toggleable without redeploy" claim is false on Workers
**What:** Part 5 §53 says the kill-switch flag is `env`-driven and "toggleable without redeploy." This repo deploys to Cloudflare Workers via TanStack Start — env vars are bound at deploy time; changing one requires `wrangler deploy` (or a dashboard edit that triggers a new version push). No runtime KV/flag service is wired.
**Why fails:** During an incident, on-call flips the env and nothing happens for ~2 min until redeploy. The runbook's "60s rollback" SLO is unmet.
**Fix path:** Either (a) wire Cloudflare KV / Workers KV-backed flag, (b) use a feature-flag SaaS (PostHog FF, Statsig), or (c) downgrade the claim to "requires redeploy, ~90s on Workers."
**Time:** **25 min** — pick (c) for MVP (1 line), document (a)/(b) as v1.1 upgrade.

## 19. Embed widget CSP + iframe sandbox flags unspecified
**What:** Part 6 §61 promises "CSP tightening" and "sandbox-safe" but lists zero directives and zero sandbox tokens. Real embed needs at minimum: `script-src 'self' https://assets.calendly.com`, `frame-ancestors *` (or allowlist), `img-src https: data:`, and iframe `sandbox="allow-scripts allow-same-origin allow-popups allow-forms"`. Missing any of these either breaks Calendly or leaves XSS surface open.
**Why fails:** AI ships a permissive `sandbox` (or none) and a default CSP — security review blocks launch.
**Time:** **20 min** — enumerate the directive list + sandbox tokens + one allowlist policy template.

## 20. Email signature uses `<picture>` — Outlook doesn't support it
**What:** Part 6 §59 says "inline CSS/tables for Outlook" but also implies the same image pipeline from Part 2 §16 (`<picture>` + WebP). Outlook 2016/2019/365-Desktop strip `<picture>` and `srcset`, and WebP isn't decoded in older Outlook. Real signatures use a single `<img src="…png">` ≤600px wide, ≤200KB, hosted on a stable CDN URL.
**Why fails:** AI generates the signature with `<picture>`; recipients on Outlook see a broken image. This is the highest-visibility bug in the whole spec (one bad render = thousands of replies).
**Time:** **15 min** — replace pipeline in §59 with: single PNG, fixed CDN URL, ≤600px, ≤200KB; add Litmus/EmailOnAcid QA checklist (3 line items).

## 21. A/B `useExperiment` hook has no implementation owner
**What:** Part 4 §host shows `const { variant } = useExperiment('hero_2026q3', {...})` — no library named, no provider chosen. Repo has no `useExperiment` hook. Options: PostHog feature flags (paid tier for experiments), GrowthBook (self-host or cloud), Statsig, hand-rolled cookie + serverFn.
**Why fails:** AI imports from `'@/hooks/useExperiment'` which doesn't exist → build failure on first try; or installs the wrong SDK.
**Time:** **25 min** — ADR: pick provider (recommend PostHog since analytics already uses it per §29), write hook contract signature + 5-line stub, mark live impl as v1.1.

## 22. Spec files lack `version:` frontmatter — GAP overrides have no anchor
**What:** 13-spec.md, all Part files, and 20-gap-analysis.md are plain markdown with no YAML frontmatter. GAP says "overrides SPEC §6.1" but doesn't say *which version* of §6.1. After Phase 1 (promote GAP into SPEC), the override pointers go stale silently.
**Why fails:** Spec drift accumulates with no provenance trail; reviewers can't tell if a contradiction is a real bug or a stale reference.
**Time:** **15 min** — add `version: 1.0.0`, `lastUpdated: 2026-06-02`, `supersedes: []` frontmatter to every spec file; GAP entries cite both file + version.

## 23. i18n key model contradicts the CMS data model
**What:** Part 3 §27 i18n keys are nested-dot (`hero.headline.line1`, `hero.headline.line2`, `hero.headline.line3`) implying 3 fields. Part 3 §host CMS schema uses **one** string `headlineText` + array `headlineAccentWords`. The translator workflow can't fill 3 keys when the CMS has 1 field.
**Why fails:** AI either hardcodes English line splits in i18n JSON (translations break on long German words) or refactors the CMS schema (breaks migration plan).
**Time:** **15 min** — pick one model. Recommend: one `headlineText` + `headlineAccentWords[]`; i18n key is `hero.headlineText` (single), accent words live in CMS not i18n.

## 24. Forced-colors and reduced-transparency only mentioned in Part 6 §55 — not wired anywhere
**What:** Part 6 §55 names both media queries as deliverables. But Part 2 (rim-light layers), Part 4 (motion table), and SPEC §11 a11y checklist don't cross-reference. A11y matrix (Phase 16, finding #8) has the same root cause but for motion only.
**Why fails:** AI implements §55 as a single CSS block at the bottom of the component; rim-light layers still render in forced-colors mode (because they're applied as inline gradients on parent elements).
**Time:** **15 min** — extend the motion matrix from finding #8 to a 3-axis matrix: motion / forced-colors / reduced-transparency, per visual element.

## 25. Edge-case fixtures missing — 5 cases, 0 data files
**What:** Part 5 §51 names 5 content edge cases (name >24 chars, quote >30 words, no portrait, stat >6 chars, empty `subRole`). Storybook (§56) declares stories for them. No `fixtures/` folder, no JSON, no factory functions.
**Why fails:** AI either invents fixtures inline in each story (drift), or stories ship referencing undefined data.
**Time:** **20 min** — one `fixtures/engineers.ts` file with 5 named exports (`longName`, `longQuote`, `noPortrait`, `longStat`, `noSubRole`) + base `default` engineer.

## 26. Cost model vs analytics volume — never multiplied
**What:** Part 5 §49 cites "PostHog free tier" (1M events/month). Spec defines 6 events; §29 reasoning says "100+ events at hero alone" is feasible. If the site does 50k hero views/day × 6 events = 9M events/month — 9× over free tier. The §49 mitigation ("sample `hero_view` at 50% if > $50/mo") only addresses one event.
**Why fails:** Surprise bill in week 2; or AI samples everything aggressively and breaks funnel math.
**Time:** **20 min** — traffic-tier × event-count table (10k/50k/200k daily views) with $/mo and recommended sampling per tier. Pin §49 mitigation to the actual volumes.

## 27. Sunset criteria (Part 5 §50) has triggers but no owner / cadence
**What:** §50 lists 5 triggers (conversion drop >25%, brand refresh, tech debt >16h, A/B ≥10%, stakeholder escalation). No named role owns the review, no cadence (monthly? quarterly?), no decision-doc template.
**Why fails:** Triggers fire silently; nobody acts. Or every PR debates "is this a sunset?" because there's no forum.
**Time:** **15 min** — assign: owner = `product-design lead`, cadence = monthly review, template = `SUNSET-DECISION-{date}.md` with 4 fields (trigger, evidence, recommendation, decision).

---

## Subtotal Phase 17 (if all 10 promoted to fix phases)

~3h 5m of fix work. Add 25% buffer → **~3h 50m**.

## Cumulative project (Phases 1–15 + 16 + 17)

~3h 45m + 3h 25m + 3h 50m = **~11h** to AI-shippable + governance-ready.

## Remaining digging passes (queued — not yet run)

Each likely yields 4–8 more issues:

1. **Native parity (Part 6 §58)** — Swift/Kotlin token-name transforms; no platform mapping table.
2. **In-app WebView (Part 6 §63)** — UA detection helper missing; SSR UA unreliable on Workers cache.
3. **Portrait license / model release** — no folder, no template, no signoff workflow.
4. **Storybook decorators registry** — `Reduced motion`, `High contrast`, `RTL` decorators named in §56 but no `.storybook/decorators.ts` proposed.
5. **TanStack route ownership** — SPEC implies hero lives at `/`, but no route file conventions in spec (loader strategy, `head()`, suspense boundary).
6. **Image alt text rules** — `portraitAlt` required, but no copy guidance (avoid "photo of"); WCAG-compliant alt patterns missing.
7. **Carousel keyboard model** — §24 mentions keyboard support; no exact key map (Arrow, Home/End, PgUp/PgDn?), no focus-trap rules.
8. **SEO structured data** — Part 3 §metadata covers OG/Twitter; no JSON-LD `Organization` / `Person` (for engineer) / `Service`.
9. **Error budgets vs SLOs** — observability §48 alerts defined but no SLO table (LCP P75, error-rate, Calendly success-rate targets).
10. **Auth & gated CTA** — what if Calendly requires a logged-in user? Hero CTA → auth flow undefined; integration with `_authenticated` route gate missing.
11. **Empty/loading states above the fold** — hero loader skeleton not specified; SSR-first vs hydration gap not addressed.
12. **Token export pipeline (§57) circular reference risk** — Style Dictionary exports to CSS vars *and* spec defines CSS vars; which is source-of-truth?
13. **Image dimensions** — portrait 2× (`~1440px tall`) but no width; aspect-ratio `4/5` from GAP — math gives 1152×1440, never stated.
14. **"Pixel-faithful at 1440×900" claim** vs responsive — no tolerance budget (±2px? layout-shift threshold?). Visual regression (§32) needs explicit tolerance.
15. **Currency/locale** of stats (e.g., "$2M+") — no locale-aware formatting; will literal "$" appear in EUR/JPY markets?
16. **Dark mode is the design** but spec talks about light-theme stub (§host). If light theme is excluded, say so loudly and remove the half-baked stub.

---

## Updated Phase Plan additions

Phases 16-A through 17-J (one per finding above) — promote individually as user says "next" after Phases 1–15 ship. Phase 18+ to be authored after pass 4.
