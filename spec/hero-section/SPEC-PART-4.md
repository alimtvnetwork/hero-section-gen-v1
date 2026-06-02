# Hero Section — Spec Part 4 (Items 35–44)

Continuation of Parts 1–3. Same **What / Reasoning / Time** format.

---

## 35. Deployment & Caching

**What:** Cache strategy by asset type:

| Asset | `Cache-Control` | Notes |
|---|---|---|
| HTML route (`/`) | `public, max-age=0, s-maxage=300, stale-while-revalidate=86400` | Edge cache 5 min, serve stale up to 24h while revalidating. |
| Fingerprinted JS/CSS (`/_build/*.[hash].js`) | `public, max-age=31536000, immutable` | Never re-fetch within a year. |
| Portrait WebP/PNG (fingerprinted) | `public, max-age=31536000, immutable` | Same. |
| Logo SVGs (inline) | n/a — bundled in HTML | — |
| Font woff2 (self-hosted, fingerprinted) | `public, max-age=31536000, immutable` + `crossorigin` | — |
| OG image (`/og/hero.png`) | `public, max-age=86400, s-maxage=604800` | 1 day browser, 1 week CDN. |

Deploy target: Cloudflare Workers (this template). Use the CDN's automatic Brotli + gzip. Add `Link: </fonts/display.woff2>; rel=preload; as=font; crossorigin` and the portrait preload as HTTP headers (faster than `<link>` in `<head>`).

**Reasoning:** Without `immutable` on fingerprinted assets, browsers still issue revalidation requests (304s) on every reload — wasted RTT that's visible on slow networks. SWR on the HTML means returning visitors get instant paint while the edge fetches a fresh copy in the background. HTTP-header preloads kick off before HTML parsing, shaving ~50–100ms off LCP vs `<link rel=preload>`.

**Time:** **45 min** — 15 min headers config (wrangler.toml or _headers), 10 min HTTP-header preload wiring, 10 min verify with `curl -I` against deployed URL, 10 min Lighthouse re-run.

---

## 36. Security Headers / CSP

**What:** Response headers:

```
Content-Security-Policy:
  default-src 'self';
  script-src 'self' 'unsafe-inline' https://assets.calendly.com https://*.posthog.com;
  style-src 'self' 'unsafe-inline';
  img-src 'self' data: https:;
  font-src 'self';
  frame-src https://calendly.com https://*.calendly.com;
  connect-src 'self' https://*.posthog.com https://api.calendly.com;
  object-src 'none';
  base-uri 'self';
  form-action 'self';
  frame-ancestors 'none';
  upgrade-insecure-requests;
Strict-Transport-Security: max-age=63072000; includeSubDomains; preload
X-Content-Type-Options: nosniff
Referrer-Policy: strict-origin-when-cross-origin
Permissions-Policy: camera=(), microphone=(), geolocation=(), payment=(), usb=()
Cross-Origin-Opener-Policy: same-origin
```

Inline SVGs are fine under `'self'`. Canvas particles are pure JS — no special CSP. If using nonce-based CSP later, drop `'unsafe-inline'` for `script-src` and inject `nonce={cspNonce}` on every `<script>`.

**Reasoning:** A leaked third-party script (analytics, embedded video) is the most common XSS vector on marketing sites — CSP is the only defense that survives a compromise of one of those vendors. `frame-ancestors 'none'` prevents the page being iframed into a phishing wrapper. HSTS preload kills SSL-strip attacks for returning visitors.

**Time:** **45 min** — 20 min CSP authoring + test against actual third parties used, 10 min headers config, 15 min Lighthouse + securityheaders.com verification + fix any blocked resources.

---

## 37. Legal & Trademark Sign-off

**What:** Checklist before production launch:

- **Logo strip:** Either (a) get written permission from each brand to use their trademark as a "trusted by" reference, or (b) replace with stylized lookalikes + label the section "Inspired by industry leaders" instead of "Trusted by." Default to (b) until (a) is signed.
- **Featured engineer portrait:** Signed model release covering web + social use, perpetual term, identified scope (marketing site + paid ads + sales decks). Store the signed PDF in `legal/releases/`.
- **Quote attribution:** Engineer's quote ("How do companies scale offshore teams profitably…") must be either (a) something they actually said + email approval, or (b) clearly framed as a rhetorical hook (no attribution), not put in their mouth.
- **Stats ("15+ Senior Engineers", "99.9% Uptime", "100+ Startups"):** Must be substantiable. Keep a one-page source-of-truth doc; FTC requires advertised claims to be defensible.
- **Privacy disclosure:** Analytics (PostHog/GA4) and Calendly trigger GDPR/CCPA obligations — link to `/privacy` in the footer (out of scope for hero, but block production launch until it exists).

**Reasoning:** "Trusted by [logo]" without permission has triggered cease-and-desist letters within 48 hours of viral launches (multiple documented YC-era cases). Model releases protect from the engineer leaving the company and demanding takedown. Unsubstantiated stats are an FTC violation, not just bad taste.

**Time:** **120 min** non-dev — 30 min email each brand, 30 min draft model release + signature, 30 min compile stats source-of-truth, 30 min privacy/cookie banner coordination. Most of this is calendar time, not focused work.

---

## 38. Copy / Voice & Tone Guide

**What:** A one-page `spec/hero-section/VOICE.md` covering:

- **Voice:** Confident, specific, no hedging. Say "Ship production-ready systems" not "Help you with software."
- **Tone:** Warm-professional. The amber palette is the visual analog — say "vetted" not "elite," "engineers" not "rockstars."
- **Forbidden words:** rockstar, ninja, guru, world-class, cutting-edge, leverage, synergy, ecosystem, solution (as a noun), unlock, empower, seamless, holistic, robust.
- **Sentence rhythm:** Mix short (4–6 words) with medium (12–16). No sentences over 22 words in the hero.
- **Numbers:** Always concrete. "Scaled 100+ startups" not "many startups." Round to credible precision (99.9% is fine; 99.87% feels made up).
- **Quote style for carousel:** First-person, posed as a question or a contrarian claim, ≤ 20 words. Examples + counter-examples included.
- **Headlines accent rule:** Highlight the verb + the outcome ("**Solve** **Real Problems**"), never the subject. Forces the reader's eye onto action.

**Reasoning:** Without a voice guide, every new engineer's quote drifts toward generic LinkedIn-speak ("passionate about delivering value") and the hero loses its distinctive feel within 3 carousel rotations. The forbidden-words list is the single highest-leverage rule — it kills 80% of slop in one pass.

**Time:** **40 min** — 30 min authoring, 10 min review with founder/marketer for buy-in.

---

## 39. Motion-Design Spec Sheet

**What:** Export a designer-readable motion spec covering every animation in the hero:

| Element | Property | From → To | Duration | Easing | Delay | Loop |
|---|---|---|---|---|---|---|
| Eyebrow row | opacity, y | 0,16 → 1,0 | 600ms | `cubic-bezier(0.22,1,0.36,1)` | 0 | once |
| H1 | opacity, y | 0,16 → 1,0 | 600ms | same | 80ms | once |
| Lede / body / CTAs / chips | opacity, y | 0,16 → 1,0 | 600ms | same | +80ms each | once |
| Portrait | opacity, scale | 0, 1.02 → 1, 1 | 900ms | `ease-out` | 200ms | once |
| Rim-light A (halo) | opacity | 0.6 ↔ 1 | 6s | `ease-in-out` | 0 | infinite |
| `Top 5%` pill glow | box-shadow opacity | 0.12 ↔ 0.28 | 4s | `ease-in-out` | 0 | infinite |
| Particle dot | y | base → +H | 8–20s (per dot) | linear | random | infinite |
| Carousel progress bar | scaleX | 0 → 1 | 6000ms | linear | 0 | reset per slide |
| Slide cross-fade | opacity | 1 → 0 outgoing, 0 → 1 incoming | 250ms | `ease-in-out` | 0 | per advance |
| CTA arrow on hover | translateX | 0 → 4px | 200ms | `cubic-bezier(0.22,1,0.36,1)` | 0 | on hover |
| Button press | scale | 1 → 0.98 | 150ms | same | 0 | on press |

Deliver as **(a) this table in markdown**, **(b) a Lottie export** of the rim-light pulse for marketing screen recordings (designers prefer Lottie over screen-grabbing CSS), **(c) After Effects tokens JSON** if the design team uses Bodymovin → AE round-trip.

**Reasoning:** Designers and devs misalign on timing constantly ("the entrance feels too slow" vs. "it's exactly 600ms like the spec says"). A canonical table ends the argument. A Lottie of the rim-light pulse means marketing can drop a polished version into product videos without a screen recording artifact.

**Time:** **60 min** — 20 min table authoring (most values already exist in Parts 1–2), 30 min Lottie export from After Effects (or skip if no AE), 10 min JSON token sync.

---

## 40. Optional Micro-Sound

**What:** A very subtle "tick" on carousel advance. Specs:

- File: 12kHz mono WAV, ~80ms duration, soft transient (think Apple's `Tink.aiff` × 0.5 volume). Export at `-24 dB LUFS` peak.
- Format: `audio/mpeg` (smallest gz) preloaded with `<audio preload="auto">`.
- Default: **muted**. User must opt in via a small speaker toggle in the bottom-right of the hero (`<button aria-pressed>`). Preference persisted to `localStorage('hero.sound')`.
- Suppress if `prefers-reduced-motion: reduce` (proxy for "no surprises") or if `document.hidden`.
- Play only on auto-advance, not on manual arrow clicks (those are user-initiated, no audio confirmation needed).
- Accessibility: never use sound as the only signal of a slide change — visual progress bar (§21) already covers that.

**Reasoning:** Default-on audio is the fastest way to get a hero closed in a noisy office. Default-off + opt-in lets sound-design enthusiasts (the people who'll tweet about it) discover it. Tying suppression to `prefers-reduced-motion` is the only signal the platform gives for "this user doesn't want unexpected stimuli."

**Time:** **50 min** — 15 min asset sourcing/editing, 15 min audio element + opt-in UI, 10 min localStorage persistence, 10 min suppression rules + QA across browsers.

---

## 41. Per-Browser / OS QA Matrix

**What:** Sign-off matrix (every cell must pass before launch):

|  | macOS Safari | iOS Safari | Chrome (Win) | Chrome (Android) | Firefox | Edge |
|---|---|---|---|---|---|---|
| Layout @ 1440 | ✓ | n/a | ✓ | n/a | ✓ | ✓ |
| Layout @ 375 | n/a | ✓ | n/a | ✓ | ✓ | n/a |
| `mix-blend-mode: screen` (rim light, particles) | **verify** | **verify** | ✓ | ✓ | ✓ | ✓ |
| Backdrop blur (if used) | ✓ | ✓ (iOS ≥ 16) | ✓ | ✓ | ✓ (≥103) | ✓ |
| `font-display: swap` + `size-adjust` | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| WebP portrait | ✓ | ✓ (iOS ≥14) | ✓ | ✓ | ✓ | ✓ |
| `aspect-ratio` | ✓ | ✓ (iOS ≥15) | ✓ | ✓ | ✓ | ✓ |
| Canvas particles (DPR) | ✓ | **verify** | ✓ | ✓ | ✓ | ✓ |
| Carousel keyboard | ✓ | n/a | ✓ | n/a | ✓ | ✓ |
| Calendly modal | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| `prefers-reduced-motion` respected | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| VoiceOver / TalkBack reads name + role + quote | ✓ | ✓ | n/a | ✓ | n/a | n/a |

Known watch-list: Safari iOS historically renders `mix-blend-mode: screen` over a canvas inconsistently — keep a screenshot in `qa/ios-rim-light.png` and re-test on every iOS major version bump.

**Reasoning:** "Works on my Chrome" is not shipping. Safari iOS is the single browser where you'll discover ~70% of cross-browser bugs in a hero like this (blend modes, DPR canvas, font metrics). Locking the matrix in writing forces the bug to be reported as a failed cell, not "it looks weird on my phone."

**Time:** **120 min** — 90 min running the matrix on real devices/BrowserStack, 30 min documenting failures + fixes.

---

## 42. Experimentation Framework

**What:** Flag-driven variants for A/B testing:

```ts
type HeroVariant = 'control' | 'b' | 'c';

const { variant } = useExperiment('hero_2026q3', {
  control: 0.5,
  b: 0.25,
  c: 0.25,
});

<Hero
  headline={variants[variant].headline}
  primaryCta={variants[variant].cta}
  experimentId="hero_2026q3"
  variantId={variant}
/>
```

Variants store: headline text + accent words, CTA label, optional featured engineer override. Variant assignment is sticky per visitor (cookie `exp_hero_2026q3=b`, 30-day TTL), survives auth. Every event in §29 auto-includes `experiment_id` + `variant_id`. Stop conditions documented: ≥95% confidence at 2000 conversions per arm, or 14 days, whichever first.

**Reasoning:** Marketing will want to A/B test the headline in week 3. Baking the variant prop and event tagging in from day one is 40 min; retrofitting requires touching every hero file and every analytics event. Sticky cookie prevents the well-known "variant flipped mid-session, conversion attributed wrong" data-integrity bug.

**Time:** **60 min** — 20 min `useExperiment` hook + cookie, 15 min variants config, 15 min event tagging passthrough, 10 min QA across browsers/incognito.

---

## 43. Contributor Onboarding README

**What:** `spec/hero-section/README.md` (≤1 screen) covering:

- Pointer to the four spec files (1, 2, 3, 4) with one-line summaries.
- Pointer to the Figma source (URL + frame name).
- Pointer to the component story / playground (`/storybook/hero` or `/dev/hero`).
- "Where to start" matrix: *I want to change the headline → §27 + §34. The carousel timing → §21. The amber color → §2.*
- A "Definition of Done" checklist matching §14 acceptance criteria + Part 2/3/4 budgets (LCP, CLS, JS budget, a11y zero violations).
- Local commands: `bun dev`, `bun run test:visual`, `bun run test:a11y`.

**Reasoning:** A 28h spec is intimidating; a 1-page README is the on-ramp. New contributors get to a productive first change in <10 minutes. The "where to start" matrix prevents the "I changed the color in 6 places and missed two" PR.

**Time:** **30 min** — 20 min authoring, 10 min review with a fresh contributor (rubber-duck test).

---

## 44. Future-Proofing

**What:** Three forward-looking bets to document but not yet implement:

1. **View Transitions API:** When `Document.startViewTransition` reaches ≥90% global support (likely 2026), replace the carousel cross-fade with a single-line `startViewTransition(() => setIndex(next))` — declarative, GPU-accelerated, free morphing of name/role between slides. Track support at caniuse.com; pull the trigger when Firefox ships it stable.
2. **CSS Container Queries:** Replace the 1440/1024/768/375 viewport breakpoints with `@container` queries on the hero's own grid wrapper. Hero becomes truly reusable in a sidebar/embed without media-query rewrites. Ready today in evergreen browsers; gated on whether the project still supports a long-tail browser.
3. **Speculation Rules API:** Add `<script type="speculationrules">{ prerender: [{ source: 'list', urls: ['/contact', '/about'] }] }</script>` to prerender the most-likely next pages while the user reads the hero. Cheap perf win on Chrome; no-op elsewhere.

Add a `FUTURE.md` tracking each item's adoption trigger and a checklist for retrofitting when triggered.

**Reasoning:** The hero will be in production for 2–3 years. Knowing in advance which platform features will swap out which custom code prevents a costly "rewrite the hero with the new API" project — it becomes a 1-day swap instead.

**Time:** **30 min** docs only — no implementation. 15 min FUTURE.md, 15 min linking the triggers from each affected spec section.

---

## ⏱ Time Roll-Up (35–44)

| # | Item | Time |
|---|---|---|
| 35 | Deployment & caching | 45 min |
| 36 | Security headers / CSP | 45 min |
| 37 | Legal & trademark sign-off | 120 min (mostly non-dev) |
| 38 | Voice & tone guide | 40 min |
| 39 | Motion-design spec sheet | 60 min |
| 40 | Optional micro-sound | 50 min |
| 41 | Per-browser / OS QA matrix | 120 min |
| 42 | Experimentation framework | 60 min |
| 43 | Contributor README | 30 min |
| 44 | Future-proofing docs | 30 min |
| | **Subtotal** | **≈ 10h 20m** |

Plus 25% buffer → realistic **~13h** for this batch (note: §37 + §41 are calendar time / device testing, not focused coding).

**Cumulative project time (items 1–44):**
- Part 1 implementation: ~6–8h
- Part 2 (15–24): ~9h
- Part 3 (25–34): ~12h
- Part 4 (35–44): ~13h
- **Total: ~40–42 hours** of work to ship this hero to a truly best-in-class standard.

---

## 🗒 Remaining Items to Spec (Part 5+)

The spec is now ~95% complete. Items left are governance and edge-case polish:

45. **Changelog & versioning policy** — semver for the `<Hero/>` component (when does a copy tweak vs. a prop signature change bump major?), `CHANGELOG.md` format, release notes template.
46. **Deprecation & migration policy** — what happens when v1 → v2 breaks the prop API across 4 landing pages; codemod expectations; minimum N-1 support window.
47. **URL/redirect policy** — if the route ever moves (e.g., `/` → `/home`), 301 rules, OG image URL stability, sitemap update.
48. **Observability & alerting** — Sentry frontend error grouping for the hero specifically; alert if LCP P75 exceeds 2.5s for 1h; alert on Calendly script load failure rate > 5%.
49. **Cost model** — CDN bandwidth budget (portrait WebP × visitors × month), analytics event volume cost (PostHog/Segment), Calendly seat cost. Important for "do we re-encode the portrait at lower quality?" decisions.
50. **Sunset criteria** — explicit conditions that retire this hero: redesign trigger (e.g., conversion < X%), brand-refresh trigger, technology-debt trigger.

After 50 the document is at the limit of useful spec detail for a single hero section. Anything further belongs in product-level docs (roadmap, design system, brand book), not in this folder.

Say the word for the **final batch 45–50** and that will close out the spec.
