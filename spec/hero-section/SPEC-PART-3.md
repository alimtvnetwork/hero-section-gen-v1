# Hero Section — Spec Part 3 (Items 25–34)

Continuation of `SPEC.md` and `SPEC-PART-2.md`. Same **What / Reasoning / Time** format. Times assume Parts 1–2 are implemented.

---

## 25. SEO & Metadata

**What:** In the route's `head()`:

- `<title>`: `Scale Your Startup with Top 5% Vetted Engineers — [Brand]` (≤60 chars).
- `<meta name="description">`: `Offshore engineering teams built for fast-moving startups across the USA, Europe, and Asia. Book a call with vetted senior engineers.` (≤160 chars).
- Open Graph: `og:title`, `og:description`, `og:type=website`, `og:image` (1200×630 generated screenshot of this hero), `og:url`, `og:site_name`.
- Twitter: `twitter:card=summary_large_image`, `twitter:title`, `twitter:description`, `twitter:image`.
- Canonical: `<link rel="canonical" href="https://…/">`.
- JSON-LD blocks (inline `<script type="application/ld+json">`):
  - `Organization` (name, url, logo, sameAs[]).
  - `Person` for the featured engineer (name, jobTitle, worksFor, sameAs LinkedIn URL).
  - `WebSite` with `potentialAction` SearchAction if site search exists.

**Reasoning:** Heroes drive the OG preview people actually see when the link is pasted into Slack/LinkedIn/Twitter — that preview converts at 2–3× a plain link. Structured `Person` data is what lets Google show the engineer's name as a rich result; without it the page indexes as generic agency boilerplate.

**Time:** **45m** — 15m metadata, 15m OG image generation + upload, 15m JSON-LD authoring + validation in Google's Rich Results Test.

---

## 26. Theme Strategy (Dark-Default, Light-Ready)

**What:** Even though this hero is dark-only by design, structure tokens so a light theme can ship later without a rewrite:

- All colors flow through semantic tokens (`--bg`, `--surface`, `--text`, `--text-muted`, `--accent`) — components never reference raw hex.
- Add `color-scheme: dark` on `:root` so native UI (scrollbars, form controls) matches.
- Document that the amber accent stays identical across themes; only neutrals invert.
- Light-theme stub: provide a `data-theme="light"` override block with placeholder values + a `TODO` comment — do not ship it visible, but keep the token surface symmetric.
- The portrait + rim light remain a **dark composition** even under a light theme (treat as imagery, like a movie poster), so wrap it in a `bg-[--bg-portrait]` container that stays near-black regardless of theme.

**Reasoning:** Retrofitting a theme later means hunting every hex literal across components — a multi-day chore. Defining the token contract now is ~30m; doing it later is days. Keeping the portrait container dark in both themes is the same trick Apple uses on product hero shots: the imagery is intentional dark art, not a "theme bug."

**Time:** **30m** — 15m token audit + extraction, 10m light-theme stub, 5m `color-scheme` + portrait container.

---

## 27. Internationalization (i18n) & RTL

**What:**

- Extract every string in the hero into a single `messages.en.json` keyed by component path (e.g. `hero.headline.line1`, `hero.cta.primary`).
- Wrap the route in an i18n provider (`react-intl`, `next-intl`, or a project's existing system); fall back to inline strings only if no system exists yet, but still keep them in one `copy.ts` file.
- Accent words ("Solve", "Real Problems") are marked via ICU rich-text formatting, not hardcoded `<span>` splits — translators wrap the right words for their language.
- RTL (`dir="rtl"`): mirror flowchart arrows (transform `scaleX(-1)`); swap rim-light to portrait's left side; reverse CTA trailing arrow icon (`scaleX(-1)`); flip carousel arrow buttons (prev/next semantics stay; visual rotation flips); reverse `flex` direction on rows where reading order matters (CTA row, contact rows).
- Test with German (longest copy — confirm no overflow) and Arabic (RTL — confirm mirror correctness).

**Reasoning:** Hardcoded `<span class="text-accent">Solve</span>` looks fine in English but breaks in any language where the highlighted concept lands on different words. ICU rich-text formatting is the only way translators can move the accent. German tests for the inevitable button-overflow bug; Arabic tests every mirror rule at once.

**Time:** **1h 30m** — 25m string extraction + provider wiring, 20m ICU rich-text for accents, 25m RTL mirror rules, 20m DE/AR review pass.

---

## 28. `Book a Call` Lead Capture

**What:** Decision matrix:

| Option | When |
|---|---|
| Calendly inline modal (preferred) | Sales team is comfortable with Calendly and wants instant booking. |
| Cal.com embed | Open-source preference, self-hosted booking. |
| Route to `/contact` form | Lead-qualification step required before showing calendar. |

Default: **Calendly modal** opens on click with the Calendly inline widget. Preconnect to `assets.calendly.com` on hero mount. Pass UTM params from the URL into Calendly's `?utm_source=…&utm_campaign=…` so attribution lands in CRM. On successful booking, fire analytics `hero_cta_booked` and (optionally) redirect to `/thank-you`. Failure state: if the script fails to load within 3s, gracefully fall back to a `mailto:` link with prefilled subject.

**Reasoning:** A modal keeps the user in context (no page transition = ~20% higher booking conversion in published case studies). UTM passthrough is the single feature marketing will ask for in week 2; baking it in now avoids a refactor. The 3s fallback covers ad-blocked Calendly, which silently kills bookings otherwise.

**Time:** **1h** — 15m modal component, 15m Calendly integration + preconnect, 15m UTM passthrough + analytics, 15m fallback + QA with ad-blocker enabled.

---

## 29. Analytics & Event Taxonomy

**What:** Standardized event names (snake_case, `hero_` prefix), schema documented in `analytics/events.md`:

| Event | Trigger | Properties |
|---|---|---|
| `hero_view` | Section ≥50% in viewport for 1s | `viewport_w`, `viewport_h`, `referrer` |
| `hero_cta_click` | Primary or secondary CTA click | `cta: 'primary' \| 'secondary'`, `label`, `position_y` |
| `hero_slide_view` | Slide visible ≥2s | `slide_index`, `engineer_name`, `manual: boolean` |
| `hero_slide_change` | Carousel advance | `from_index`, `to_index`, `trigger: 'auto' \| 'arrow' \| 'keyboard'` |
| `hero_logo_strip_visible` | Logo strip ≥50% in viewport (once per session) | — |
| `hero_cta_booked` | Calendly success postMessage | `engineer_slot`, `utm_source`, `utm_campaign` |

Use IntersectionObserver for view-based events (no scroll listeners). Provider-agnostic: route through a thin `track(event, props)` wrapper so PostHog/GA4/Segment can be swapped.

**Reasoning:** Naming events ad-hoc creates a junk-data taxonomy that requires a Q2 cleanup project. Standardizing now (with the `hero_` prefix and snake_case) keeps dashboards usable at 100+ events. IntersectionObserver-based view events are 10× cheaper than scroll listeners and don't jank the carousel.

**Time:** **45m** — 10m `track()` wrapper, 20m wiring all 6 events, 10m IntersectionObserver setup, 5m docs in `analytics/events.md`.

---

## 30. Error & Empty States

**What:** Define visible behavior for each failure mode:

| Failure | Behavior |
|---|---|
| Portrait fails to load | Show a CSS-only abstract amber-gradient silhouette (`radial-gradient` + `mask-image: …person.svg`) sized identically — preserves layout, no broken-image icon. |
| Carousel data empty (0 engineers) | Hide the right profile column + carousel controls entirely. Keep the portrait area as a static decorative abstract gradient. Left column unchanged. |
| Single engineer (1 item) | Render the profile card but hide arrow buttons and progress segments; disable auto-advance. |
| Fonts blocked by CSP | `font-display: swap` + `size-adjust` overrides keep layout stable in system-font fallback; visual diff is acceptable. |
| JavaScript disabled | Render the first slide as static HTML; carousel controls hidden via `<noscript>` CSS. CTAs still work (anchor tags). |
| Particle canvas crashes (very old browser) | `try/catch` around init; on error, remove the canvas node — visual loss is negligible, no console spam. |

**Reasoning:** Hero failure modes are the most-screenshotted bug class because they're above-the-fold. A broken-image icon next to "Top 5% Vetted Engineer" is a credibility-destroying screenshot. Defining every failure as a layout-preserving graceful degrade is cheap insurance.

**Time:** **50m** — 15m portrait fallback gradient + mask, 15m carousel empty/single states, 10m font-swap stability test, 10m `<noscript>` + try/catch guards.

---

## 31. Print Stylesheet

**What:** `@media print` block scoped to the hero:

- Force light: `background: #FFF !important; color: #000 !important;`.
- Hide: particle canvas, rim-light layers, flowchart SVG, carousel arrows + progress, secondary CTA.
- Convert portrait to grayscale (`filter: grayscale(1) contrast(1.1)`); cap height 4cm so it fits on letter/A4.
- Replace amber accents with bold black (`color: #000; font-weight: 700`).
- Show primary CTA as text + URL: `Book a Call (https://example.com/book)` via `a[href]::after { content: " (" attr(href) ")"; }`.
- Page-break-inside: avoid on the stat row.

**Reasoning:** Enterprise sales decks frequently print the landing page as a one-pager handout. Without a print sheet you get a totally black A4 sheet (literally just ink-soaked dark mode) — embarrassing in a boardroom. Print is 1h 30m you pay once, forever.

**Time:** **40m** — 20m `@media print` block, 10m portrait grayscale + sizing, 10m print preview QA in Chrome/Safari.

---

## 32. Testing Strategy

**What:** Three layers:

1. **Visual regression (Playwright + `toHaveScreenshot`):** snapshots at 375 / 768 / 1024 / 1440 widths, both `prefers-reduced-motion: no-preference` and `reduce`. Mask the carousel + particle canvas regions (timestamped/random content).
2. **A11y (axe-core via `@axe-core/playwright`):** assert zero `serious`/`critical` violations on the hero route. Specific checks: color contrast (body text), button labels, image alt, ARIA live region on carousel.
3. **Unit (Vitest):** carousel state machine — auto-advance interval, pause-on-hover, keyboard nav, reset-on-manual, empty-data guard. Pure state machine = no DOM mocks needed.

CI gates: visual diffs require explicit approval; axe failures block merge; unit coverage on the carousel module ≥ 90%.

**Reasoning:** Visual regression is the only test that catches the off-by-2px baseline drift from §15. Axe catches the regression where someone removes `aria-live` from the carousel during a refactor. Unit tests on the state machine catch the auto-advance-doesn't-reset bug, which is impossible to spot in code review.

**Time:** **1h 30m** — 30m Playwright setup + 4 viewport snapshots, 20m axe integration + assertions, 30m carousel unit tests, 10m CI wiring.

---

## 33. Component API & Prop Contracts

**What:** If the hero is shipped as a reusable component, the public API is:

```ts
type Engineer = {
  id: string;
  name: string;
  role: string;          // "Chief Software Engineer"
  subRole?: string;      // "AI, .NET Developer"
  portraitSrc: string;   // 2x URL
  portraitAlt: string;
  quote: string;
  linkedinUrl?: string;
  location?: string;     // "Dhaka, Bangladesh"
  badge?: string;        // "Top 5% Vetted Engineer"
};

type Stat = { icon: LucideIcon; value: string; label: string };

type HeroProps = {
  eyebrow: string;
  headline: { text: string; accentWords: string[] };
  lede: string;
  body: string;
  primaryCta: { label: string; onClick?: () => void; href?: string };
  secondaryCta: { label: string; onClick?: () => void; href?: string };
  stats: Stat[];                  // exactly 3 enforced via runtime warn
  engineers: Engineer[];          // 1+; if 1, carousel controls hidden
  autoAdvanceMs?: number;         // default 6000
  onSlideChange?: (i: number) => void;
  onPrimaryCtaClick?: () => void;
  onSecondaryCtaClick?: () => void;
};
```

Rules: no `className` prop on the root (style via tokens only); accent words are matched case-sensitively against `headline.text` and wrapped — translators control which words highlight by editing the array, not the markup.

**Reasoning:** A typed contract is the difference between a component that gets reused across 4 landing pages and one that gets copy-pasted then drifts. Forbidding `className` on the root prevents the "every consumer adds `!important` overrides" anti-pattern. Accent-words-as-array decouples highlighting from string-splitting for i18n (§27).

**Time:** **40m** — 20m type design + JSDoc, 10m runtime guards (stat count, engineer count), 10m consumer-side smoke test.

---

## 34. CMS Content Schema

**What:** Sanity (or Contentful) schema mirroring `HeroProps`:

```ts
// sanity/schemas/hero.ts
{
  name: 'hero',
  type: 'document',
  fields: [
    { name: 'eyebrow', type: 'string', validation: r => r.required().max(80) },
    { name: 'headlineText', type: 'string', validation: r => r.required().max(120) },
    { name: 'headlineAccentWords', type: 'array', of: [{ type: 'string' }] },
    { name: 'lede', type: 'text', rows: 2, validation: r => r.max(180) },
    { name: 'body', type: 'text', rows: 4, validation: r => r.max(500) },
    { name: 'primaryCta', type: 'object', fields: [
      { name: 'label', type: 'string' },
      { name: 'url', type: 'url' },
    ]},
    { name: 'secondaryCta', type: 'object', /* same shape */ },
    { name: 'stats', type: 'array', of: [{ type: 'statChip' }],
      validation: r => r.length(3) },
    { name: 'engineers', type: 'array', of: [{ type: 'reference', to: [{ type: 'engineer' }] }],
      validation: r => r.min(1).max(8) },
  ],
}
```

Separate `engineer` document type owns name, role, portrait (Sanity image with hotspot for the rim-light side), quote, LinkedIn, location, badge. Preview pane shows the live hero render. Editors can reorder the carousel by drag-and-drop.

**Reasoning:** Marketing will absolutely want to swap the engineer of the month, A/B test headlines, and rotate the trusted-by stats without filing a ticket. Field-length validators stop the "headline is 200 chars and now wraps to 6 lines" bug at editor time, not at QA time. Hotspot on the portrait lets editors pick a new photo and have the rim light still land on the face.

**Time:** **1h 15m** — 20m hero schema, 20m engineer schema + hotspot, 15m stat-chip schema + icon picker, 10m preview pane wiring, 10m editor smoke test.

---

## ⏱ Time Roll-Up (25–34)

| # | Item | Time |
|---|---|---|
| 25 | SEO & metadata | 45m |
| 26 | Theme strategy | 30m |
| 27 | i18n & RTL | 1h 30m |
| 28 | `Book a Call` lead capture | 1h |
| 29 | Analytics & event taxonomy | 45m |
| 30 | Error & empty states | 50m |
| 31 | Print stylesheet | 40m |
| 32 | Testing strategy | 1h 30m |
| 33 | Component API & prop contracts | 40m |
| 34 | CMS content schema | 1h 15m |
| | **Subtotal** | **≈ 9h 25m** |

Plus 25% buffer → realistic **~11h 45m** for this batch.

**Cumulative project time (Parts 1–3, items 1–34):**
- Part 1 implementation (items 1–14): not estimated in-card, ≈ 6–8h.
- Part 2 (15–24): ~9h.
- Part 3 (25–34): ~12h.
- **Total so far: ~27–29h** of focused dev time for a single best-in-class hero.

---

## 🗒 Remaining Items to Spec (Part 4+)

35. **Deployment & caching** — CDN cache headers for portrait variants, `Cache-Control: immutable` on fingerprinted assets, edge vs static HTML decision.
36. **Security headers / CSP** — content-security-policy for inline SVG + canvas + Calendly + analytics; `Permissions-Policy` denylist; `Referrer-Policy: strict-origin-when-cross-origin`.
37. **Legal & trademark sign-off** — process for the logo strip (real vs lookalike), portrait release form for the featured engineer, privacy disclosure for analytics.
38. **Copy / voice & tone guide** — so future engineers' quotes and slide content match the headline's voice (confident, specific, no jargon).
39. **Motion-design spec sheet** — Lottie or video alternative to the rim-light pulse for marketing screen recordings; export tokens for After Effects.
40. **Optional micro-sound** — subtle "tick" on carousel advance for muted-by-default users who enable sound; respect `prefers-reduced-motion` as a proxy for "no surprises."
41. **Per-browser/OS QA matrix** — Safari (macOS + iOS), Chrome (Win + Android), Firefox, Edge; specifically test `mix-blend-mode: screen` on Safari iOS (historically buggy).
42. **Experimentation framework** — flag-driven swap of headline copy, CTA label, or featured engineer; bake into the component API (`<Hero variant="b" />`).
43. **Onboarding for new contributors** — `spec/hero-section/README.md` that points to all three SPEC files, the Figma source, and the component story.
44. **Future-proofing** — view-transitions API for slide changes when browser support reaches 90%; CSS `@container` queries to replace the 1024/768/375 breakpoints.

After 44 the long tail is mostly project-governance (changelog policy, deprecation policy, redirect rules if the route URL ever changes) and is better captured at the project level, not in this spec.

Tell me which numbers next and I'll write them at this depth.
