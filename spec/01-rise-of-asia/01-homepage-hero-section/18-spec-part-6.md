# Hero Section — Spec Part 6 (Items 55–64)

Continuation of Parts 1–5. Same **What / Reasoning / Time** format. This is the long-tail batch: optional polish, distribution variants, and accessibility above the legal minimum. None are required to launch, but each is a real lever once you have one.

> Reminder: `20-gap-analysis.md` overrides everything earlier. Items in this batch are written against the **canonical** image (`00-hero-canonical.png`).

---

## 55. Accessibility Beyond WCAG 2.2 AA

**What:** Go from AA (the legal floor) to AAA on the dimensions that actually matter for a hero:

- **7:1 contrast for body text:** swap muted body color from `#B5B5B5` (~5.3:1) to `#D4D4D4` (~9.4:1) on `#0A0A0A`. Amber accent stays AA-large-text only — never use it for small body copy.
- **Plain-language summary:** add a visually-hidden `<p class="sr-only">` at the top of the section paraphrasing the hero in ≤25 plain words: *"Offshore engineering agency. Hire vetted senior engineers for startups. Book a call or view portfolio."* — screen reader users get the gist in 4 seconds instead of parsing decorative copy.
- **Reduced-transparency:** respect `prefers-reduced-transparency: reduce` — disable rim-light layers + particle canvas, increase backdrop contrast (some users have vestibular sensitivity to glow effects).
- **Forced-colors mode (Windows High Contrast):** wrap the hero in `@media (forced-colors: active)` block — drop background imagery, use `CanvasText`/`Canvas`/`LinkText`/`Highlight` system colors, ensure CTAs render with visible borders.
- **Sign-language / video alternative for the engineer quote:** optional, but if the brand promises inclusivity, provide a 15s captioned BSL/ASL video of the quote, behind a `Watch in sign language` link in the profile column.
- **Cognitive a11y:** every interactive element has a clear, predictable label (no icon-only buttons without text); animations cap at one continuous loop per element (rim-light), not many simultaneous loops.

**Reasoning:** AA passes a Lighthouse audit; AAA passes a real screen-reader user. The high-contrast Windows mode is the single most-skipped a11y check and the one that exposes the most "invisible button" bugs in dark themes — testing it takes 5 minutes and prevents enterprise-customer escalations. Plain-language summary is the cheapest accessibility win in this entire spec.

**Time:** **1h** — 15m contrast pass + token swap, 5m sr-only summary, 15m forced-colors block, 10m reduced-transparency wiring, 15m screen-reader walkthrough (VoiceOver + NVDA). Sign-language video is excluded (separate production budget).

---

## 56. Storybook / Playground Stories

**What:** A `stories/Hero.stories.tsx` with these stories (each is one selectable variant in the Storybook sidebar):

- **`Default`** — props from CMS canonical content.
- **`Single engineer`** — `engineers.length === 1` (carousel controls hidden).
- **`Three engineers`** — canonical 3-slide carousel.
- **`Long name + long quote`** — stress test for §51 edge-case rules.
- **`Missing portrait`** — `portraitSrc=null` triggers initials fallback.
- **`Long headline`** — German translation (~40% longer) to verify wrap behavior.
- **`RTL`** — `dir="rtl"`, Arabic content, mirror-verified.
- **`Reduced motion`** — Storybook decorator forces `prefers-reduced-motion: reduce`.
- **`High contrast`** — decorator forces `forced-colors: active`.
- **`Mobile`** — viewport 375×667.
- **`Tablet`** — viewport 768×1024.
- **`Loading`** — skeleton state (portrait blur-up placeholder, text shimmer).

Each story is a **single screenshot** for Chromatic / Percy visual regression (§32). Knobs (Storybook Controls) expose `eyebrow`, `headline.text`, `headline.accentPhrase`, `engineers[0].name`, `engineers[0].quote`, `autoAdvanceMs` for live designer tinkering.

**Reasoning:** Designers will request "what does it look like if the name is really long?" forever. A story per edge case lets them answer it themselves without filing tickets. Chromatic-style visual regression also gets every edge case covered for free — no separate test authoring.

**Time:** **1h 15m** — 15m Storybook scaffold (if not already installed), 5m/story × 11 stories = 55m, 5m Chromatic wiring.

---

## 57. Design Tokens Export (Style Dictionary)

**What:** Single source of truth for tokens, transformed into all consumer formats:

- Source: `tokens/hero.tokens.json` (W3C Design Tokens spec format).
- Build: Style Dictionary or Tokens Studio CLI emits:
  - `tokens/dist/css/hero.vars.css` — CSS custom properties (used by this hero).
  - `tokens/dist/tailwind/hero.config.js` — Tailwind theme extension.
  - `tokens/dist/json/hero.tokens.json` — flat JSON (consumed by Figma plugin, native apps).
  - `tokens/dist/swift/HeroTokens.swift` — iOS struct (for §58 native parity).
  - `tokens/dist/kotlin/HeroTokens.kt` — Android object.
- CI: re-build on every change to source; commit-block if dist is out of sync.
- Tokens stay **semantic** (`color.accent.default`), not raw (`color.amber.500`). The amber hex lives in exactly one place.

**Reasoning:** Without a token-export pipeline, the amber hex drifts across web, iOS, Android, marketing emails, and Figma — each platform settles on slightly different values within months. One source + N transforms means changing the brand amber is a one-line PR that lands everywhere. This is the foundation §58 (native parity) sits on.

**Time:** **1h 15m** — 25m Style Dictionary setup + first config, 20m token authoring + semantic naming pass, 15m platform transforms, 15m CI sync-check.

---

## 58. Native App Parity (iOS + Android)

**What:** If iOS (SwiftUI) and/or Android (Jetpack Compose) apps exist or are planned, mirror the hero as native components:

- **Shared tokens** from §57 — no native re-declaration of colors, spacing, radii.
- **Shared content schema** from Part 3 §34 — same CMS feeds web + native via a single GraphQL/REST endpoint, no native-specific copy fields.
- **Native-appropriate composition:** native heroes adapt to safe areas and notches (`SafeAreaInset` on iOS, `WindowInsets` on Android); carousel becomes a native `TabView` / `HorizontalPager` for momentum scrolling.
- **Lottie for the rim-light pulse** (§39) — same Lottie file plays on web and native, identical motion.
- **Portrait image:** same WebP CDN URL; native picks 2x/3x via Accept headers or query param.
- **Analytics events (§29) use identical event names + property schemas** so PostHog dashboards merge web and native without renames.

**Reasoning:** Cross-platform brand drift is the most expensive design-system bug — "the orange looks different on iOS" is a meeting that happens forever. Sharing tokens + content + Lottie means the brand stays identical, and every CMS edit propagates everywhere with one publish. Skip this entirely if there's no native app — it's worse than nothing if the native team doesn't exist to maintain it.

**Time:** **Native is multi-day work** — out of this spec's scope to estimate. Spec contract (file-level): **1h 30m** to author `HERO-NATIVE.md` with the contract, token mapping, content fields, Lottie usage, and analytics-naming rules so the native team can implement without further coordination.

---

## 59. Email Signature Variant

**What:** A 600px-wide static HTML+inline-CSS block sales reps paste into Gmail/Outlook/Apple Mail signatures:

- Layout: 96×96 circular portrait on the left, name + role + quote stacked on the right, single CTA button below (`Book a Call →`).
- Pure inline CSS (no `<style>` blocks — Outlook strips them); tables for layout (Outlook 2007–2019 still uses Word's renderer).
- Image hosting: portrait + CTA button image both served from CDN with `Cache-Control: immutable`; `<img>` tags have explicit `width`/`height` attrs.
- Generated from the same CMS engineer record → one Edge function `/api/signature/[engineer-id].html` returns the snippet.
- Fallback: pure text version below the HTML for plain-text mail clients.
- Tracking: CTA links wrapped with UTM `utm_source=email_signature&utm_medium=email&utm_content={engineerId}`.

**Reasoning:** Sales reps send 30–100 emails/day each. A signature with a polished engineer photo + book-a-call CTA is a 24/7 lead funnel that costs nothing per send. Auto-generating from CMS means swapping the featured engineer updates every rep's signature next time their client loads the email (CDN cache permitting). Outlook table-layout is non-negotiable — it has 40%+ B2B market share and renders modern CSS as gibberish.

**Time:** **1h 15m** — 30m HTML/table layout + Outlook QA, 15m Edge function, 10m UTM wiring, 10m plain-text fallback, 10m litmus.com cross-client test.

---

## 60. PDF / Sales-Deck Export

**What:** A Playwright (or Puppeteer) script that renders the hero as a single-page A4/Letter landscape PDF for sales decks:

- Headless Chromium loads the hero route with `?print=1` (triggers print stylesheet from §31 + hides hero chrome that doesn't belong in a deck: carousel arrows, particle canvas).
- Layout adapts: shorter section, branded footer (logo + URL + page number), generous margins, embedded fonts.
- Output: `exports/hero-{engineerId}-{date}.pdf` to S3 / R2.
- Triggered manually via `bun run export:hero --engineer alim-ul-karim` OR scheduled monthly for "featured engineer of the month" sales material.
- Resolution: `viewport: 1920×1080`, `deviceScaleFactor: 2` for crisp print.
- File size budget: ≤ 500KB (heavy compression on the portrait, subsetted fonts).

**Reasoning:** Sales decks live in PowerPoint/Keynote and salespeople ask for "a slide of the hero" weekly. A scripted export removes that recurring designer interrupt. Per-engineer PDFs let reps personalize a deck for a prospect in seconds. Subsetting fonts (only chars used in the deck) is the single biggest PDF size win — often 70%+ reduction.

**Time:** **1h 30m** — 30m Playwright script + viewport, 20m print-mode stylesheet additions, 15m branded footer + page-number template, 15m file-size optimization (font subsetting + portrait compression), 10m CLI wrapper.

---

## 61. Embeddable / iframe Widget

**What:** A `/embed/hero` route that renders a self-contained, postMessage-aware version of the hero for partner blogs, PR placements, or YC startup pages:

- Strip: no global site header/footer, no analytics that bleed cross-domain, no Calendly (replace with a deep link to `https://book.example.com/?utm_source=embed&host={referrer}`).
- Self-resizing: emits `postMessage({ type: 'embed-resize', height })` to parent on mount + ResizeObserver — partner page includes a one-line snippet that listens.
- Sandbox-safe: works under `<iframe sandbox="allow-scripts allow-popups">` (no `allow-same-origin` — that's the host's choice).
- CSP for the embed route is **more restrictive**: no third-party scripts at all; tracking happens server-side via a referrer-tagged pixel.
- Snippet generator UI at `/embed/hero/snippet` — partner pastes the engineer ID, gets a copy-pasteable `<script async src="…/embed.js">` snippet that auto-inserts the iframe.
- Versioning: `?v=1` query param so the embed contract stays stable for partners; bump major version for breaking changes (mirrors §45 semver).

**Reasoning:** Embeddable hero = free distribution. A partner who embeds your hero on their blog is a permanent lead funnel. PostMessage resize is the only correct way to handle dynamic iframe heights — without it the embed always shows a scrollbar (looks broken to readers) or huge whitespace (looks wrong to the partner). CSP must be tightened for embed-mode because you've given up control of the host page.

**Time:** **2h** — 30m `/embed/hero` route + stripped UI, 25m postMessage resize protocol, 20m snippet generator UI, 20m CSP tightening + server-side pixel, 15m partner-side `embed.js` loader, 10m cross-origin QA.

---

## 62. AMP / Instant-Page Variant — Decision

**What:** Decide whether to ship an AMP version of the hero.

- **Default decision in 2026: do not ship AMP.** Google deprecated AMP-as-ranking-signal in 2021; AMP traffic share has collapsed to <2% of organic search. The maintenance cost (separate template, restricted JS subset, no Calendly script) outweighs the benefit.
- **Ship AMP only if:** the brand has measurable Google Discover traffic (>10% of organic), OR a partner publication contractually requires AMP (rare, mostly legacy news).
- Document the decision in `DECISIONS.md` with the traffic data justifying it; re-evaluate annually.
- If shipped: AMP variant is feature-locked at hero copy + static portrait + Calendly deep link (no carousel, no particles, no analytics beyond Google's amp-analytics). It is **never** the canonical version.

**Reasoning:** "Should we do AMP?" is a recurring question that wastes engineering time every year. A documented decision short-circuits it. Most teams should skip AMP in 2026 and use Core Web Vitals (already in §24) to satisfy Google's speed signals — same outcome, one codebase.

**Time:** **30m** — 20m decision doc with traffic data, 10m calendar reminder for annual re-eval. If AMP is shipped: add ~8 hours for parallel template + ongoing maintenance.

---

## 63. In-App WebView / Social-Browser Optimization

**What:** Verify and tune the hero in the four most-common in-app browsers, because they all render differently from real Chrome/Safari:

| WebView | Quirks to handle |
|---|---|
| **Instagram / Facebook in-app** | `mix-blend-mode` works but slower; particle canvas FPS drops — auto-disable particles when UA matches `Instagram` / `FBAN` / `FBAV`. Calendly often blocked by FB's tracking-prevention; show deep-link CTA fallback. |
| **TikTok in-app** | Aggressive font-substitution — `font-display: swap` is essential. Outbound link tap can be intercepted by TikTok's confirmation sheet; pre-warn via `target="_blank"` and an explicit message. |
| **X / Twitter in-app** | Generally Chrome-like on Android, Safari-like on iOS — fewer quirks, but verify OG preview is the correct image (uses `twitter:image` over `og:image`). |
| **LinkedIn in-app** | Often opens external links in system browser — minimal in-app testing needed, but verify the LinkedIn share preview specifically (LinkedIn caches OG aggressively; use Post Inspector to bust). |

Add UA detection helper `isInAppBrowser()` that returns the brand string; gate optional features (particles, Calendly modal) on it.

**Reasoning:** A large fraction of inbound traffic from social ads comes through these in-app browsers, not the system browser. The hero looks broken in TikTok-in-app (default fonts, frozen particles) → bounce rate spikes → ad spend wasted. UA-based feature gating costs ~10 LoC and recovers measurable conversion.

**Time:** **1h** — 15m UA helper, 10m particle gating, 10m Calendly fallback for FB/IG, 15m real-device testing across the four apps, 10m documenting findings in `qa/in-app-browsers.md`.

---

## 64. Privacy Mode / Consent-Aware Rendering

**What:** When the user has declined analytics/marketing cookies (via the cookie banner), gracefully degrade:

- **No analytics events fire** — the `track()` wrapper (§29) reads consent state and no-ops every event except a single first-party `hero_view` (legitimate-interest server-side count, no personal data, no third-party).
- **Calendly:** replace the inline modal with a plain `<a href="https://calendly.com/…">` link that opens in a new tab. Calendly's own page handles consent there.
- **PostHog autocapture disabled** entirely until consent granted.
- **Personalization off:** if the hero ever shows "Welcome back, {name}" or similar, drop it under no-consent.
- **Visual parity:** the hero looks identical with or without consent — never punish users with a degraded UI for declining cookies (this is both a GDPR requirement and a trust signal).
- **Re-rendering on consent change:** if the user accepts cookies mid-session, hydrate analytics + Calendly inline modal without a full reload — listen on a `consentChanged` custom event from the banner.

**Reasoning:** GDPR + CCPA + state privacy laws require this in practice, not just in policy. The "no degraded UI" rule is the single behavior that builds trust — sites that hide content or break features when you decline cookies train users to never accept them anywhere. Server-side `hero_view` counting via legitimate interest lets you keep traffic measurement without third-party cookies — the simplest legal path.

**Time:** **1h** — 15m consent state wiring in `track()`, 15m Calendly link-vs-modal switch, 10m server-side first-party view counter, 10m re-hydration on `consentChanged`, 10m legal review pass with the privacy doc.

---

## ⏱ Time Roll-Up (55–64)

| # | Item | Time |
|---|---|---|
| 55 | A11y beyond WCAG AA | 1h |
| 56 | Storybook stories | 1h 15m |
| 57 | Design tokens export | 1h 15m |
| 58 | Native parity contract (file only) | 1h 30m |
| 59 | Email signature variant | 1h 15m |
| 60 | PDF / sales-deck export | 1h 30m |
| 61 | Embeddable iframe widget | 2h |
| 62 | AMP decision (doc only) | 30m |
| 63 | In-app WebView tuning | 1h |
| 64 | Privacy / consent rendering | 1h |
| | **Subtotal** | **≈ 12h 15m** |

Plus 25% buffer → realistic **~15h 20m** for this batch. Note §58 excludes the actual native build (multi-day per platform).

**Cumulative project time (items 1–64):**
- Part 1 implementation: ~6–8h
- Part 2 (15–24): ~9h
- Part 3 (25–34): ~12h
- Part 4 (35–44): ~13h
- Part 5 (45–54): ~9h
- Part 6 (55–64): ~15h
- **Total: ~64–66 hours** to ship a best-in-class hero with full long-tail polish.

For perspective: a typical landing-page hero ships in 4–8 hours. The 64h above is what separates "shipped" from "industry-leading reference implementation that compounds value for years."

---

## 🗒 Remaining Items to Spec (Part 7+)

Honestly: the spec is functionally complete. Anything beyond 64 is either out-of-hero-scope or so situational it should be an RFC, not part of this folder. That said, here are the last credible items if you want to truly exhaust the surface area:

65. **Hero-specific feature flags taxonomy** — naming convention, kill-switch flags vs. experiment flags, cleanup policy (every flag has an expiry date).
66. **Multivariate testing (vs. single-variable A/B from §42)** — full factorial across headline × CTA label × engineer photo; sample size implications.
67. **AI-personalized hero variants** — server-side variant selection based on visitor signals (referrer industry, geo, time-of-day); risk of veering into manipulative personalization is documented.
68. **Hero as a reusable "section primitive"** — extract the rim-light + portrait + profile-card pattern as a standalone composable for use on other pages (engineer detail page, about page, case studies).
69. **Brand book entry** — formal "Hero pattern" page in the company brand book with do/don't examples, when to use vs. when to use a different hero pattern.
70. **Open-sourcing strategy** — if the hero design becomes a brand asset, decision on whether to publish as a public design-system pattern (with attribution requirement) or keep proprietary.

After 70 there's nothing left that genuinely belongs in a hero spec. Further work is governance (org-wide design system), business strategy (open-source vs. proprietary), or content (the actual copy / engineer roster) — all owned outside this folder.

Say the word for the final batch **65–70** and the spec is exhaustively done.
