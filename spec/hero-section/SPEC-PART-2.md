# Hero Section — Spec Part 2 (Items 15–24)

Continuation of `SPEC.md`. Each item includes **Reasoning** (why it matters) and **Time** (realistic estimate for a mid-level dev using the Part 1 stack). Times assume Part 1 tokens, fonts, and DOM skeleton already exist.

---

## 15. Exact Pixel Grid & Safe Zones

**What:** Lock the hero to a 12-col grid, `column-gap: 24px`, outer gutter `80px ≥1280 / 48px 1024–1279 / 24px <1024`. Add a debug overlay (toggled by `?grid=1`) showing column lines + an 8px baseline grid. Every text block, button, chip, and the portrait must snap to the 8px baseline (y-positions = multiples of 8).

**Reasoning:** The reference's "premium" feel comes from rhythm, not decoration. Off-by-2px baseline drift is the #1 thing that makes a faithful build feel AI-generated. A debug grid lets QA verify alignment in a single screenshot instead of pixel-peeping.

**Time:** **35 min** — 15 min grid wrapper + tokens, 10 min debug overlay, 10 min snap-pass on existing blocks.

---

## 16. Portrait Asset Pipeline & Fallback

**What:** Source priority: (1) the provided `01-hero.png` cropped to the subject; (2) regenerate via `imagegen` using SPEC §5.1 if missing. Export WebP (primary) + PNG (fallback) at 2× density (~1440px tall). Serve via `<picture>` + `srcset` (1x/2x). Disable lazy-load (above-the-fold), set `fetchpriority="high"`, `decoding="async"`, and preload in TanStack `head()` with `<link rel="preload" as="image" imagesrcset="…">`.

**Reasoning:** The portrait is the LCP candidate. Without preload + priority hints, LCP routinely slips past 2.5s on simulated 4G and tanks the Lighthouse score — the #1 landing-page regression. WebP cuts ~30% bytes vs PNG at identical perceived quality.

**Time:** **40 min** — 10 min export/convert, 10 min `<picture>` markup, 10 min preload wiring, 10 min Lighthouse verification.

---

## 17. Background Flowchart SVG — Authoring Rules

**What:** Inline SVG (no external file), `viewBox="0 0 800 800"`, `opacity: 0.22`, `aria-hidden="true"`. Three rounded rects labeled `Fetch / Process / Render` (12px text, `fill: var(--accent)`), connected top→bottom with 1px arrows. All strokes `var(--accent)`, `stroke-linejoin: round`. Rectangles solid stroke; connector arrows `stroke-dasharray: 4 4`.

**Reasoning:** Inline SVG inherits the design token color and renders consistently across browsers (external SVG-as-img anti-aliases differently in Safari). Dashed connectors read "schematic / blueprint" — solid connectors would read as a literal product diagram and pull focus from the portrait.

**Time:** **30 min** — 20 min authoring at correct coordinates, 10 min position/opacity tuning behind the portrait.

---

## 18. Amber Rim-Light Composite

**What:** Two stacked radial-gradient layers behind the portrait, both `pointer-events-none`, `mix-blend-mode: screen`:

- **A — soft halo:** 900×900, `radial-gradient(circle at 30% 50%, rgba(255,149,0,0.18), transparent 60%)`, `filter: blur(40px)`. Pulses (Part 1 §10.3).
- **B — hot edge:** 400×600, `radial-gradient(ellipse at 25% 50%, rgba(255,170,60,0.35), transparent 55%)`, `filter: blur(20px)`. Static, positioned so its hot center sits ~40px inside the portrait's right silhouette edge.

**Reasoning:** A single gradient looks like a stock Photoshop flare. Two layers (one wide/soft, one narrow/hot) mimic how a physical rim light wraps a subject — the difference between "cinematic" and "CSS demo." `mix-blend-mode: screen` adds light instead of painting opaque orange over the portrait edge.

**Time:** **25 min** — 10 min markup, 15 min iterative position tuning at 1280 and 1440.

---

## 19. Particle Field — Canvas Implementation

**What:** Single `<canvas>` over the right two-thirds, `mix-blend-mode: screen`, `pointer-events-none`. ~80 amber dots:

- Init: `x ∈ [0.33W, W]`, `y ∈ [0, H]`, `r ∈ [0.5, 1.5]`, `vy ∈ [0.05, 0.25] px/frame`, `alpha ∈ [0.15, 0.35]`.
- Loop with `requestAnimationFrame`; wrap y to top on overflow; `fillStyle = rgba(245,165,36,alpha)`.
- Pause when `document.hidden` and when `prefers-reduced-motion: reduce`.
- DPR-aware: `canvas.width = cssW * dpr`, scale context once.

**Reasoning:** Three.js for 80 dots adds ~150KB gz for no visible benefit. A div-per-particle approach janks past ~40 nodes on low-end laptops. Canvas2D hits 60fps trivially in ~2KB JS. Skipping DPR scaling makes the dots fuzzy on retina — non-negotiable.

**Time:** **50 min** — 20 min canvas + DPR setup, 15 min animation loop, 10 min reduced-motion + visibility hooks, 5 min density/speed tuning.

---

## 20. CTA System — Button Variants & States

**What:** Reusable `<Button variant="primary|secondary" size="lg">`. All five states defined:

| State | Primary | Secondary |
|---|---|---|
| Rest | bg `--accent-strong`, text `#0A0A0A`, glow `0 10px 30px -8px rgba(255,149,0,0.45)` | bg `#1A1A1A`, border `rgba(255,255,255,0.10)` |
| Hover | bg `#FFB341`, glow opacity 0.6, arrow `translate-x-1` | border `rgba(245,165,36,0.4)`, arrow `translate-x-1` |
| Active | `scale-[0.98]`, glow opacity 0.3 | `scale-[0.98]`, bg `#222` |
| Focus-visible | 2px `--accent` ring, 2px offset | same |
| Disabled | bg `#3A2A10`, text `#7A6A4A`, no glow | bg `#141414`, text `#5A5A5A` |

Transitions: `transform 150ms, box-shadow 200ms, background-color 150ms`, easing `cubic-bezier(0.22,1,0.36,1)`.

**Reasoning:** Defining all five states up-front prevents "hover looks broken on tablet" follow-ups. A single token-driven variant API stops secondary CTAs from accidentally inheriting the amber glow — the glow is what makes "Book a Call" read as the obvious primary action.

**Time:** **45 min** — 15 min component scaffold, 20 min state styling, 10 min visual review at all states.

---

## 21. Carousel — Behavior & Progress Animation

**What:** 5 slides under the portrait. Auto-advance every 6000ms; pause on hover anywhere in the hero, on focus-within of controls, and when `document.hidden`. Active progress segment animates `scaleX(0) → scaleX(1)` linear over the interval; inactives stay flat. Arrow clicks advance manually AND reset the timer. Keyboard: `ArrowLeft/Right` when focus is inside the carousel, `Home/End` jump to first/last. Slide-change cross-fades portrait + name/role + quote + contacts (`<AnimatePresence mode="wait">` 250ms, or CSS opacity fallback). Wrap the changing region in `aria-live="polite"`.

**Reasoning:** Auto-advancing carousels are a known a11y trap. Pause-on-hover + pause-on-focus + reduced-motion respect makes it WCAG 2.2 compliant. The scaleX progress bar doubles as a visual countdown so users know a change is coming — without it the auto-advance feels random and users miss content.

**Time:** **75 min** — 20 min state machine + timer, 20 min progress fill, 15 min keyboard + pause-on-hover, 10 min cross-fade groups, 10 min reduced-motion fallback.

---

## 22. Stat Chip Component — Data-Driven

**What:** Render chips from a typed array, not hardcoded JSX:

```ts
type Stat = { icon: LucideIcon; value: string; label: string };
const stats: Stat[] = [
  { icon: UsersRound,  value: "15+",   label: "Senior Engineers On Demand" },
  { icon: ShieldCheck, value: "99.9%", label: "System Uptime Focus" },
  { icon: LineChart,   value: "100+",  label: "Startups Scaled Worldwide" },
];
```

Chip: `flex items-center gap-3 px-4 py-3 rounded-xl bg-[--bg-elev] border border-[--border]`. Icon tile is a sibling 32×32 `rounded-md bg-[--accent-soft] grid place-items-center` — never overlap the icon onto the tile with absolute positioning.

**Reasoning:** Data-driving makes localization, CMS sourcing, and A/B tests trivial — these requests land within weeks of launch. `grid place-items-center` avoids the sub-pixel centering bug `flex+items-center+justify-center` produces in Safari with odd-sized SVG glyphs.

**Time:** **20 min** — 10 min component + data, 10 min styling.

---

## 23. Logo Strip — Wordmark SVG Set

**What:** Six inline SVG wordmarks (`frame`, `Workflo`, `kabo`, `lendsqr`, `sanity`, `veed`), ~120×28 viewBox, monochrome `currentColor` paths. Layout:

```html
<ul class="grid grid-cols-6 items-center gap-12 max-w-[1200px] mx-auto text-[#4A4A4A]">
  <li class="flex justify-center transition-colors duration-200 hover:text-[#9A9A9A]"><FrameLogo/></li>
  …
</ul>
```

Use `<ul>/<li>` (semantically a list of brands). Each SVG has a `<title>`. **Do NOT** embed real third-party trademarks — stylized lookalikes only, with a comment noting trademark caveat.

**Reasoning:** `flex justify-between` produces uneven gaps when logo widths differ (which they always do); `grid-cols-6` guarantees pixel-symmetric columns. `currentColor` lets one CSS rule recolor every path on hover instead of per-SVG fills. The trademark caveat avoids the takedown that has hit multiple ProductHunt landing-page templates.

**Time:** **60 min** — 40 min authoring 6 acceptable wordmarks (the bottleneck), 15 min layout + hover, 5 min a11y `<title>`.

---

## 24. Performance & LCP Budget

**What:** Hard budgets, enforced via Lighthouse CI or a documented manual gate:

- **LCP ≤ 2.0s** at simulated 4G / 4× CPU. Portrait is the LCP element.
- **CLS = 0** — explicit `width/height` or `aspect-ratio` on every image/icon/chip; fonts use `font-display: swap` AND `size-adjust` metric overrides to prevent shift on font load.
- **TBT ≤ 150ms** — defer carousel JS via dynamic `import()` post-first-paint; particle canvas waits for `requestIdleCallback` (fallback `setTimeout(…,0)`).
- **JS payload for hero ≤ 30KB gz** — no Three.js (Canvas2D per §19), prefer CSS keyframes over Framer Motion where possible, tree-shake Lucide (`import { Crown } from "lucide-react"`).
- Self-host the display font with `preload` + `crossorigin`; subset to Latin (~70% woff2 size reduction).

**Reasoning:** Heroes are where landing pages win or lose Core Web Vitals — LCP is a Google ranking signal and a 3s+ LCP measurably hurts paid-ad quality scores. Numeric budgets make regressions blocking instead of subjective. CLS=0 specifically catches the most common landing-page bug: a 4px H1 shift when the web font swaps in.

**Time:** **60 min** — 15 min font self-hosting + size-adjust, 15 min dynamic imports, 15 min Lighthouse run + remediation, 15 min CI threshold or manual-gate doc.

---

## ⏱ Time Roll-Up (15–24)

| # | Item | Time |
|---|---|---|
| 15 | Pixel grid & safe zones | 35 min |
| 16 | Portrait pipeline & preload | 40 min |
| 17 | Flowchart SVG | 30 min |
| 18 | Rim-light composite | 25 min |
| 19 | Particle canvas | 50 min |
| 20 | CTA variants & states | 45 min |
| 21 | Carousel behavior | 75 min |
| 22 | Stat chips (data-driven) | 20 min |
| 23 | Logo wordmark set | 60 min |
| 24 | Performance & LCP budget | 60 min |
| | **Subtotal** | **≈ 7h 20m** |

Add ~25% buffer for review/QA → realistic **~9 hours** for this batch alone.

---

## 🗒 Remaining Items to Spec (Part 3+)

Suggested next 10, in priority order:

25. **SEO & metadata** — exact `<title>`, meta description, OG image generation, JSON-LD `Organization` + `Person` (Alim Ul Karim).
26. **Dark/light theme strategy** — even if hero is dark-only, define coexistence with a future light theme (`color-scheme`, token aliases).
27. **Internationalization (i18n)** — string extraction; RTL flip rules (mirror flowchart arrows, swap rim-light side, reverse CTA arrow).
28. **`Book a Call` lead capture** — Calendly modal vs `/contact` route, analytics events, UTM passthrough.
29. **Analytics & event taxonomy** — PostHog/GA4 events: `hero_cta_click`, `hero_slide_view`, `hero_logo_strip_visible` (IntersectionObserver), payload schema.
30. **Error & empty states** — portrait fails to load, empty carousel data, fonts blocked by CSP — what does the hero render?
31. **Print stylesheet** — collapse to clean black-on-white text version (frequently requested by enterprise sales decks).
32. **Testing strategy** — Playwright visual regression at 375/768/1024/1440, axe-core a11y assertions, unit tests for carousel state.
33. **Component API & prop contracts** — if shipped as reusable `<Hero/>`: typed props for headline, accent words, stats[], engineers[], onPrimaryCta, onSecondaryCta.
34. **CMS / content schema** — Sanity/Contentful model for engineers carousel + stats so marketing can edit without a deploy.
35. **Deployment & caching** — CDN cache headers for portrait variants, `Cache-Control: immutable` on fingerprinted assets, edge vs static HTML.

Lower priority later: motion-design spec sheet (Lottie alt for the rim light), security headers (CSP for inline SVG + canvas), legal sign-off on logo strip, copy/voice guide, optional micro-sound, per-browser/OS QA matrix.

Tell me which numbers you want next and I'll write them at this depth.
