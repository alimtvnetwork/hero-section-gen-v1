# Hero Section — Spec Part 5 (Items 45–54)

Continuation of Parts 1–4. Same **What / Reasoning / Time** format. This batch covers governance, observability, cost, and the long-tail polish that separates a "shipped" hero from a "maintained" hero.

---

## 45. Changelog & Versioning Policy

**What:** The `<Hero/>` component follows strict **semver**:

- **MAJOR (x.0.0):** Breaking prop signature change, removed prop, renamed CSS token consumed by external pages, removed DOM node a consumer might query.
- **MINOR (1.x.0):** New optional prop, new variant, new slot, additive token, new event in §29 taxonomy.
- **PATCH (1.0.x):** Copy tweak via CMS doesn't count; code-only changes — bug fix, perf fix, refactor with no API change, default-value tuning that stays within documented range.

`CHANGELOG.md` format = "Keep a Changelog" convention: sections `Added / Changed / Deprecated / Removed / Fixed / Security`, newest on top, dated, version-tagged. Every PR touching the hero must add an entry; CI lints for a non-empty `Unreleased` block diff.

CMS-only content changes (new engineer slide, new headline copy) bump a separate `content-version` field and do NOT alter the component semver — they're data, not code.

**Reasoning:** Without a written semver rule, every consumer guesses "is this a major?" differently and updates become risky. Separating content-version from code-version stops marketing edits from triggering false major bumps that block downstream consumers from auto-updating.

**Time:** **30m** — 15m `CHANGELOG.md` scaffold + first entry, 10m PR template + CI lint rule, 5m docs in README.

---

## 46. Deprecation & Migration Policy

**What:** When a prop is removed:

1. **Mark deprecated** in MINOR release: TypeScript `@deprecated` JSDoc + console.warn (once per session, dev-only).
2. **Document migration** in `MIGRATION.md` with before/after code snippets. Provide a codemod (`bunx hero-migrate v1-to-v2`) when the change is mechanical.
3. **N-1 support window:** Deprecated props keep working for at least one full MAJOR (so v2 still accepts v1 props, behind the warn). v3 may remove them.
4. **Communication:** Deprecation announced in CHANGELOG, README "Migration Notes" section, and (if internal) #design-system Slack channel with a 30-day notice before MAJOR.
5. **Telemetry:** If runtime allows, count usage of deprecated props in production for one week before removal — empirically confirm zero consumers before the MAJOR bump.

**Reasoning:** The single most expensive design-system mistake is removing a prop someone still uses. A documented N-1 window + telemetry-confirmed zero usage turns a coin-flip into a safe operation. Codemods turn 4-hour grep-and-replace chores into 30-second commands and dramatically increase upgrade adoption.

**Time:** **45m** docs + tooling — 20m `MIGRATION.md` template + first entry, 15m `@deprecated` + console.warn helper, 10m codemod scaffold (real codemod authored per-change later).

---

## 47. URL / Redirect Policy

**What:** If the hero's route URL ever moves (e.g., `/` becomes `/home` after a site IA refactor):

- **301 (permanent) redirect** from old → new at the CDN/Workers layer. Never use 302 for SEO-relevant route moves.
- **Canonical tag** updated on the new page; old URL must NOT issue 200 + canonical-to-new (that's the "soft 404" pattern Google penalizes).
- **OG image URL stability:** OG images keep a content-hash URL (`/og/hero-{contenthash}.png`), so already-cached LinkedIn/Slack previews keep working even after the route moves.
- **Sitemap update** within the same deploy: remove old URL, add new URL with `<lastmod>` set to deploy date.
- **Internal links audit:** grep the codebase for the old path; replace before deploy, not after. Run a Playwright link-checker in CI.
- **External backlinks:** If the hero has accrued backlinks (e.g., from a Product Hunt launch), keep the 301 in place **forever**, not just for 30 days. Removing it loses the SEO equity.

**Reasoning:** A 302 instead of 301 silently loses ranking authority — discovered weeks later as a traffic drop. "Forever 301s" is the cheapest insurance against an SEO mistake you can't easily undo. OG image stability via content-hash URL prevents the "old Slack previews show a broken image" embarrassment after a redesign.

**Time:** **45m** — 20m Workers redirect rule + test, 10m sitemap update logic, 10m link-checker CI step, 5m docs entry.

---

## 48. Observability & Alerting

**What:** Three layers wired to PagerDuty (or Slack for low-severity):

| Signal | Source | Threshold | Severity |
|---|---|---|---|
| Frontend JS error | Sentry, filtered by `tags.component:hero` | >50 events/hr OR new error type | P2 (Slack) |
| LCP P75 regression | Web Vitals → analytics → daily rollup | P75 > 2.5s sustained 1h | P2 (Slack) |
| CLS P75 regression | same | P75 > 0.1 sustained 1h | P2 (Slack) |
| Calendly script load failure | `error` event on `<script>` | failure rate > 5% over 15m | P1 (PagerDuty) |
| Portrait image 404 | CDN logs | any non-zero rate over 5m | P1 (PagerDuty) |
| Carousel auto-advance stuck (no `hero_slide_change` events for 10m while `hero_view` events present) | analytics anomaly detection | continuous 10m | P3 (Slack) |
| CSP violation reports | `report-uri` endpoint | >20/hr OR new directive | P2 (Slack) |

Sentry release tagging: every deploy tags `hero@<semver>` so regressions are bisectable to a specific release within seconds.

**Reasoning:** Hero failures hurt conversion immediately and are invisible without alerts — the team often finds out via a sales rep noticing fewer demos booked. Tying alerts to specific symptoms (Calendly failure → P1) instead of generic "errors up" routes the page to the right responder. CSP reports are the canary for an XSS attempt in progress.

**Time:** **1h 15m** — 20m Sentry tagging + release wiring, 20m Web Vitals → analytics rollup, 15m Calendly + image-404 alerts, 10m CSP report-uri endpoint, 10m PagerDuty/Slack routing config.

---

## 49. Cost Model

**What:** Per-month cost projection at three traffic tiers (10k, 100k, 1M hero views/mo). Build a small `costs.md` table:

| Item | Unit Cost (2026) | @10k | @100k | @1M |
|---|---|---|---|---|
| CDN bandwidth (portrait WebP, ~120KB) + HTML + JS + CSS (~150KB total per visit, ~80KB returning) | $0.04/GB egress (Cloudflare Workers free tier covers most) | ~$0 | ~$0.50 | ~$5 |
| Portrait image storage (Cloudflare R2) | $0.015/GB/mo | $0 | $0 | $0 |
| Analytics events (~6 events × visits, PostHog Cloud Scale tier) | $0.00031/event after 1M free | $0 | $0 | ~$1.50 |
| Calendly Teams plan (per booker seat, not per visitor) | $16/seat/mo | flat | flat | flat |
| Sentry errors (10k events/mo free, then $26/100k) | tiered | $0 | $0 | ~$0–26 |
| OG image regen (only on content change, cached) | negligible | $0 | $0 | $0 |
| Cloudflare Workers requests (10M free, $0.30/M after) | tiered | $0 | $0 | ~$0 |
| **Total infra (excl. Calendly seats)** | | **~$0** | **~$0.50** | **~$10–35** |

Decision triggers documented:
- If portrait > $20/mo, re-encode at 80% quality (saves ~30% bytes).
- If analytics > $50/mo, sample `hero_view` at 50%.

**Reasoning:** Marketing teams worry that "best-in-class hero" implies "expensive to serve" — the actual numbers show even 1M views/mo costs <$35 infra. Documenting this kills the recurring "should we strip the particles for cost" conversation. Decision triggers convert "we should monitor cost" hand-waving into specific actionable thresholds.

**Time:** **30m** — 20m spreadsheet authoring, 10m triggers + docs.

---

## 50. Sunset Criteria

**What:** Written conditions that retire this hero design:

1. **Conversion trigger:** `hero_cta_click → hero_cta_booked` conversion drops > 25% from 30-day baseline for 14 consecutive days, AND root cause isn't a tracked bug or seasonality.
2. **Brand-refresh trigger:** A company-wide brand or design-system update lands; hero must be re-themed in the same sprint or formally sunset.
3. **Tech-debt trigger:** Cumulative tech debt (deprecated APIs, framework EoL, unmaintained deps) exceeds 16h of work to address — at that point a rewrite is cheaper than incremental fixes.
4. **A/B experimentation trigger:** A challenger variant (§42) beats control by ≥10% at 95% confidence over 30 days — promote challenger, sunset control.
5. **Stakeholder trigger:** Founder/CEO/Head of Design escalates a directional change (e.g., "we're not a dev agency anymore"). Document the decision in `DECISIONS.md`.

When sunset triggers fire, run the **retirement playbook**: archive design assets (Figma → `archive/2026-hero/`), tag the final code version (`hero@FINAL`), keep redirects (§47), update README to point to the successor, freeze content edits in CMS.

**Reasoning:** Designs without sunset criteria linger past their useful life because no one wants to be the person who says "kill it." Numeric triggers depersonalize the decision and make it routine. The retirement playbook prevents the common "we redesigned but kept four orphan files referencing the old hero" cleanup project.

**Time:** **30m** — 20m criteria doc, 10m retirement playbook template.

---

## 51. Edge-Case Content Rules

**What:** Defensive rendering for unusual content:

- **Engineer name > 24 chars** ("Maria Concepción Vázquez del Rosario"): allow wrap to two lines, max 2; tighten line-height to 1.05; if still overflows, ellipsis with `<title>` for full name on hover.
- **Quote > 30 words:** cap at 25 visible words with `…`, expand-on-click into a modal showing full quote (this is also a `hero_quote_expand` analytics event).
- **No portrait available:** fall back to engineer initials in an amber circle (96px, `--accent-soft` bg, white initials, font weight 600).
- **Headline only 1 line:** balance whitespace with `text-wrap: balance` to avoid orphans like "with" on its own line.
- **Stat value > 6 chars** ("$120M+"): reduce font-size step from 18px to 16px to keep chip width predictable.
- **Empty `subRole`:** collapse the line entirely (no empty 14px gap).

**Reasoning:** Content always exceeds initial assumptions within the first quarter of CMS use. Defining defensive rules now avoids the recurring "the new engineer's name broke the hero on mobile" hotfix. `text-wrap: balance` is a one-line CSS property that single-handedly kills 80% of headline orphan complaints.

**Time:** **40m** — 15m wrap + ellipsis rules, 10m initials fallback component, 5m `text-wrap: balance`, 10m stat width handling.

---

## 52. Image Generation Reproducibility

**What:** When portraits or background imagery is AI-generated:

- Store the **prompt, model, seed, and date** alongside the image: `portraits/alim-ul-karim.json` with `{ prompt, model: "imagen-3", seed: 482991, generatedAt }`.
- Re-generation rule: minor variants (re-light, re-crop) should reuse the same seed when the model supports it, to keep facial features consistent.
- License documentation: capture the AI provider's commercial-use terms in `legal/ai-license-snapshot.md` with the date — terms change.
- Human-in-the-loop: every AI-generated face shown in production must be approved by a designer (sign-off recorded in PR description) — avoids the "uncanny valley" hero that quietly tanks conversion.
- Watermark/provenance: store optional C2PA manifest for the image if the provider supports it.

**Reasoning:** Six months later when someone asks "regenerate this portrait but with glasses removed," nobody remembers which model or prompt produced it. Versioned prompts make the image reproducible and the variants consistent. License snapshots protect from the now-routine "the AI vendor changed their commercial terms retroactively" surprise.

**Time:** **30m** — 10m metadata schema, 10m PR-template sign-off field, 10m license snapshot doc.

---

## 53. Disaster Recovery & Rollback

**What:** Pre-staged rollback for hero changes:

- Every deploy tags the previous hero version as `hero@last-known-good`. Cloudflare Workers' instant-rollback can revert to it in < 60s without rebuilding.
- A **kill-switch flag** (`HERO_SAFE_MODE=true`) renders a minimal static fallback hero (no carousel, no particles, no Calendly — just headline + body + a `/contact` link). Toggleable via env without redeploy.
- **Health check** at `/api/health/hero` returns 200 if portrait image, Calendly script, and font assets all resolve. Pingdom/Better Stack monitors at 1-min intervals.
- **Runbook** in `RUNBOOK.md`: numbered steps for the 3 most likely on-call incidents (Calendly outage → set `HERO_SAFE_MODE`; portrait CDN down → swap to cached fallback; LCP regression → identify offending deploy via Sentry release tag → rollback).

**Reasoning:** "We can fix it forward" sounds disciplined and fails in practice during a real incident at 2 AM. A pre-staged kill switch + 60s rollback + numbered runbook reduces mean-time-to-recovery from hours to minutes. The static safe-mode hero is ugly but functional — preserves Calendly bookings even when the dynamic version is down.

**Time:** **1h** — 20m safe-mode component + flag plumbing, 15m health check endpoint, 15m runbook authoring, 10m on-call dry run.

---

## 54. Final Acceptance & Launch Gate

**What:** A single launch-day checklist that must be 100% green before flipping DNS / publishing:

- [ ] All Part 1 acceptance criteria (§14) pass.
- [ ] Lighthouse mobile: Performance ≥ 90, Accessibility ≥ 95, Best Practices ≥ 95, SEO ≥ 95.
- [ ] LCP P75 ≤ 2.0s in field data from staging.
- [ ] CLS = 0 verified in three browsers.
- [ ] Per-browser/OS matrix (§41) 100% green.
- [ ] CSP report-uri receiving 0 violations in 24h staging soak.
- [ ] Analytics taxonomy (§29) verified in PostHog Live View (every event fires once).
- [ ] Calendly booking flow end-to-end tested with a real booking.
- [ ] OG preview verified in Slack, LinkedIn, Twitter, iMessage debuggers.
- [ ] Legal sign-off recorded (§37).
- [ ] On-call paged and acknowledged for next 48h.
- [ ] Rollback dry-run executed in staging.
- [ ] `hero@1.0.0` git tag pushed; CHANGELOG entry merged.
- [ ] Sales + Marketing + Support teams informed in #launches.

Owner per checkbox is named (not "team"). Sign-off by Eng Lead + Design Lead + Marketing Lead before launch.

**Reasoning:** The launch is the highest-risk moment. A single checklist with named owners turns "did we test that?" into a paper trail and forces accountability. Acknowledged on-call within 48h covers the most common post-launch incident window.

**Time:** **30m** prep — 20m checklist authoring + owner assignment, 10m stakeholder review. Actual sign-off day adds 2–4h of running through the list.

---

## ⏱ Time Roll-Up (45–54)

| # | Item | Time |
|---|---|---|
| 45 | Changelog & versioning | 30m |
| 46 | Deprecation & migration | 45m |
| 47 | URL / redirect policy | 45m |
| 48 | Observability & alerting | 1h 15m |
| 49 | Cost model | 30m |
| 50 | Sunset criteria | 30m |
| 51 | Edge-case content rules | 40m |
| 52 | Image-gen reproducibility | 30m |
| 53 | Disaster recovery & rollback | 1h |
| 54 | Final acceptance & launch gate | 30m (+ 2–4h on launch day) |
| | **Subtotal** | **≈ 6h 55m** |

Plus 25% buffer → realistic **~8h 40m** for this batch (not counting launch day itself).

**Cumulative project time (items 1–54):**
- Part 1 implementation: ~6–8h
- Part 2 (15–24): ~9h
- Part 3 (25–34): ~12h
- Part 4 (35–44): ~13h
- Part 5 (45–54): ~9h
- **Total: ~49–51 hours** to ship + governance-ready.

---

## 🗒 Remaining Items to Spec (Part 6+)

This is now the long tail. Each item below is real but increasingly optional — decide based on team size, regulatory context, and how reusable the hero needs to be:

55. **Accessibility audit beyond WCAG AA** — WCAG 2.2 AAA pass: 7:1 contrast for body, sign-language video alternative for the engineer quote, plain-language summary.
56. **Storybook / playground stories** — every variant, every state, every viewport as a separate story with controls (knobs) for live tinkering by designers.
57. **Design tokens export** — Style Dictionary or similar: emit JSON, CSS vars, Swift, Kotlin, Tailwind config from one source — for native apps or partner sites that need the same amber.
58. **Native app parity** — if iOS/Android apps exist or are planned, mirror the hero in SwiftUI/Compose with shared tokens and a shared content schema.
59. **Email signature variant** — a 600px-wide static PNG/HTML hero block for use in sales-team email signatures, generated from the same CMS content.
60. **PDF / sales-deck export** — Puppeteer/Playwright script that renders the hero to a polished single-page PDF for sales decks (related to §31 print, but standalone export).
61. **Embeddable / iframe widget** — `<iframe src="…/embed/hero" />` for partner blogs or PR placements, with postMessage API for resize.
62. **AMP / instant-page variant** — if Google Discover or news distribution matters; usually skip in 2026 but document the decision.
63. **WebView optimization** — many in-app browsers (Instagram, TikTok) have different font + JS quirks; document tested behavior in those WebViews.
64. **Privacy mode / consent-aware rendering** — if user has denied cookies, drop analytics events + Calendly to a no-cookie fallback, render hero unchanged otherwise.

Past 64, anything new is either project governance (not hero-specific) or so niche it should live in its own RFC, not a hero spec.

Say the word for **55–64** and that effectively closes the long tail.
