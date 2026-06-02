# Hero Section — Implementation Spec

**Spec version:** 1.0.0 · **Entry point:** [`00-index.md`](./00-index.md) · **Tokens:** [`02-tokens.md`](./02-tokens.md) · **Conventions:** [`01-conventions.md`](./01-conventions.md) · **Changelog:** [`21-changelog.md`](./21-changelog.md)

> **Stop.** If you have not read `00-index.md`, read it first. This file is one of seven; reading it standalone produces the wrong design.

Reference image: `./00-hero-canonical.png` (canonical). Archived variant: `./01-hero-variant-archived.png` — do NOT build.

Target stack: **TanStack Start + React 19 + Tailwind v4** (`@theme` in `src/styles.css`, oklch tokens — see `02-tokens.md`). Three.js particles are **optional progressive enhancement** per `12-threejs-fallback.md`. Motion via `framer-motion`. Output must be pixel-faithful to `00-hero-canonical.png` at 1440×900 desktop, and gracefully responsive at 1024 / 768 / 375 (canonical snapshots in `snapshots/`).

---

## 0. Global Intent

The hero presents an offshore engineering agency. Mood: **premium, dark, cinematic, trustworthy**. Think "Linear × Vercel × Stripe" but warm — black background with amber/orange highlights rim-lighting a portrait of a featured engineer. Left = marketing copy + CTAs + stat chips. Right-center = portrait. Far right = "engineer profile card" overlay. Bottom = carousel dots + trusted-by logo strip.

---

## 1. Canvas & Layout

1. Full-bleed section, `min-h-screen` (≈ 900px tall on desktop), `w-full`, `position: relative`, `overflow: hidden`.
2. Background base color: **pure near-black** `#0A0A0A` (Tailwind `bg-neutral-950` is close; prefer custom token `--bg: #0A0A0A`).
3. Inner content uses a 12-column grid with `max-w-[1440px] mx-auto px-20` (80px side gutters on desktop, 24px on mobile).
4. Vertical rhythm: top padding `pt-24` (96px), bottom padding `pb-10` (40px) before the logo strip.
5. Three primary horizontal zones across the width:
   - **Left text column:** columns 1–5 (≈ 42% width, max 560px).
   - **Center portrait zone:** columns 5–9 (overlaps the text column slightly — portrait can bleed under the right edge of the text).
   - **Right profile card zone:** columns 9–12 (≈ 25% width, max 360px), vertically centered.
6. Logo strip is a separate full-width row at the bottom of the section, separated by ~80px of black space and a hairline divider `border-t border-white/5`.

---

## 2. Color Tokens (define in Tailwind theme / `:root`)

```
--bg:            #0A0A0A   /* page background */
--bg-elev:       #141414   /* chip / card surface */
--bg-elev-2:     #1A1A1A   /* secondary button, pill backgrounds */
--border:        rgba(255,255,255,0.06)
--border-strong: rgba(255,255,255,0.10)
--text:          #FFFFFF
--text-muted:    #B5B5B5   /* body copy */
--text-dim:      #7A7A7A   /* meta, captions, logo-strip label */
--accent:        #F5A524   /* primary amber/orange */
--accent-strong: #FF9500   /* CTA fill, gradient highlight */
--accent-soft:   rgba(245,165,36,0.12) /* glow halos */
```

All amber accents must share the same hue; do NOT introduce red, pink, or yellow-green variants.

---

## 3. Typography

1. Headings: a **modern geometric sans** — use **"General Sans"** or **"Satoshi"** (display weight). Fallback: `Inter` 700.
2. Body: same family, regular/medium weights, OR `Inter` 400/500.
3. Eyebrow / labels: same family, uppercase, tracking `0.18em`, weight 600.
4. Sizes (desktop):
   - Eyebrow: `12px / 16px`, uppercase, color `--accent`.
   - H1: `64px / 72px`, weight 700, color `#FFFFFF`, letter-spacing `-0.02em`.
   - H1 highlighted words ("Solve", "Real Problems"): same size/weight, color `--accent` (`#F5A524`).
   - Lede paragraph (immediately under H1): `18px / 28px`, weight 500, color `#E5E5E5`.
   - Secondary paragraph: `14px / 22px`, weight 400, color `--text-muted`.
   - Stat number: `18px / 22px`, weight 700, color `#FFFFFF`.
   - Stat label: `12px / 16px`, weight 400, color `--text-muted`.
   - Profile name: `28px / 32px`, weight 600, color `#FFFFFF`.
   - Profile role: `14px`, weight 500, color `--accent`.
   - Profile sub-role: `14px`, weight 400, color `--text-muted`.
   - Pull quote: `20px / 30px`, weight 500, italic OFF, color `#EDEDED`.
   - Logo-strip label: `12px`, uppercase, tracking `0.2em`, color `--text-dim`.

---

## 4. Left Column — Marketing Copy

1. **Eyebrow row** at the very top:
   - A short amber horizontal rule, 24px wide × 1.5px tall, color `--accent`, vertically centered with the text.
   - 12px gap, then text: `TRUSTED BY ENGINEERING TEAMS SHIPPING TO US · EU · ASIA`.
   - Color `--accent`. The middle dots are `·` (U+00B7) with 8px horizontal padding.
2. 24px vertical gap to the H1.
3. **H1** (3 lines, hard-wrapped):
   - Line 1: `Scale Your Startup with`
   - Line 2: `Engineers Who ` + `Solve` (amber)
   - Line 3: `Real Problems` (amber)
   - The amber words MUST be wrapped in `<span class="text-accent">` for individual coloring — do NOT recolor entire lines.
4. 24px gap → **Lede paragraph** (max 2 lines): `Offshore engineering teams built for fast-moving startups across the USA, Europe, and Asia.`
5. 16px gap → **Secondary paragraph** (4 lines max-width ~520px): `We help startups and growing companies build, ship, and scale production-ready systems with carefully vetted Asian engineers. From MVP development to performance optimization and long-term scaling, our teams focus on debugging, reliability, and real-world impact — not just code delivery.`
6. 32px gap → **CTA row** (`flex gap-3`):
   - **Primary CTA** "Book a Call":
     - Background: solid `--accent-strong` `#FF9500` (or a subtle gradient `linear-gradient(180deg, #FFB341 0%, #FF9500 100%)`).
     - Text: `#0A0A0A`, weight 600, 15px.
     - Padding: `px-6 py-3.5` (≈ 24px/14px).
     - Radius: `rounded-lg` (8px).
     - Trailing arrow icon (`→` or Lucide `arrow-right`) 16px, same dark color, 8px left margin.
     - Soft amber glow: `box-shadow: 0 10px 30px -8px rgba(255,149,0,0.45)`.
     - Hover: arrow translates +4px X; glow intensifies.
   - **Secondary CTA** "View Our Work":
     - Background: `--bg-elev-2` `#1A1A1A`, border `1px solid var(--border-strong)`.
     - Text: `#FFFFFF`, weight 500, 15px.
     - Same padding, radius, arrow.
     - Hover: border becomes `--accent` at 40% opacity.
7. 24px gap → **Stat chip row** (`flex gap-3`, three chips, equal natural width, do NOT stretch).
   Each chip:
   - Background `--bg-elev` `#141414`, border `1px solid var(--border)`, radius `rounded-xl` (12px), padding `px-4 py-3`.
   - Left: 32×32 square with rounded-md, background `--accent-soft`, centered amber Lucide icon (`users-round`, `shield-check`, `line-chart` respectively), icon stroke `--accent`, stroke-width 1.75.
   - Right (text stack, 4px gap):
     - Top: bold number — `15+`, `99.9%`, `100+`.
     - Bottom: muted label — `Senior Engineers On Demand`, `System Uptime Focus`, `Startups Scaled Worldwide`.

---

## 5. Center — Portrait

1. Portrait: photorealistic headshot of a bearded man wearing glasses and a black button-up shirt, looking slightly camera-right. Use the supplied image as the source if available; otherwise generate via `imagegen` with prompt: *"Cinematic studio portrait of a 30-year-old South Asian software engineer with short black hair, neatly trimmed beard, thin-rim glasses, wearing a dark charcoal button-up shirt, three-quarter pose facing camera-right, dramatic warm amber rim light on the right side of his face and shoulder, pitch-black background, shallow depth of field, photorealistic, 4k, professional headshot, no logos."*
2. The portrait sits **without any frame, mask, or rounded container** — its background is already black so it composites directly onto the section background.
3. Sizing: image height ≈ 720px on desktop; positioned with its **vertical center aligned to the section's vertical center**, horizontally centered between columns 5 and 9.
4. The right edge of the portrait can softly fade into the section using `mask-image: linear-gradient(to right, black 70%, transparent 100%)` so it blends behind the profile card.
5. **Behind the portrait, layered between bg and image:** a faint blueprint/flowchart graphic — thin amber lines (1px, `--accent` @ 25% opacity) drawing boxes labeled `Fetch`, `Process`, `Render`, connected by arrows. Render as an inline SVG, positioned absolute, opacity 0.25, blur `backdrop-blur-[1px]` optional. Purpose: hint at engineering workflow without distracting.
6. **Rim light:** add a radial gradient div behind the portrait — `background: radial-gradient(circle at 30% 50%, rgba(255,149,0,0.18) 0%, transparent 60%)`, sized 800×800, blurred.

---

## 6. Right — Engineer Profile Card (no card chrome)

The right column is a vertical stack, left-aligned text, ~320px wide. There is NO bordered card — items float on the black background, separated by generous spacing.

1. **"Top 5% Vetted Engineer" pill** at the top:
   - Pill: background `--bg-elev`, border `1px solid var(--border-strong)`, radius `rounded-full`, padding `px-4 py-2`.
   - Leading amber crown icon (Lucide `crown`), 16px, color `--accent`.
   - Text: `Top 5% Vetted Engineer`, 13px, weight 500, white.
   - Subtle amber outer glow: `box-shadow: 0 0 24px rgba(245,165,36,0.18)`.
2. ~120px vertical gap (let the portrait breathe) → **Name block**:
   - `Alim Ul Karim` — 28px, weight 600, white.
   - 4px gap → `Chief Software Engineer` — 14px, weight 500, `--accent`.
   - 4px gap → `AI, .NET Developer` — 14px, weight 400, `--text-muted`.
3. 20px gap → **Pull quote** with large amber opening quotation mark:
   - Quotation mark `"` (or `“`) as a separate span, 40px, weight 700, color `--accent`, line-height 1, sitting above-left of the text by ~8px.
   - Quote text: `How do companies scale offshore teams profitably using top 1% talent?` — 20px / 30px, weight 500, white.
   - Closing quote `"` after the text, same amber styling, baseline-aligned.
4. 24px gap → **Contact rows** (two rows, 12px gap between them). Each row:
   - 32×32 rounded-md `--bg-elev` square containing a centered icon (`linkedin` then `map-pin`), icon color white, 16px.
   - 12px gap → text, 14px, color `#E5E5E5`. Values: `linkedin.com/in/alimulkarim` and `Dhaka, Bangladesh`.

---

## 7. Carousel Controls (under portrait, centered between columns 5–9)

1. Row contains: `‹ button` · 5 progress segments · `› button`. Vertical position: ~40px below the portrait's bottom.
2. Arrow buttons: 40×40 `rounded-full`, background `--bg-elev`, border `1px solid var(--border)`, white chevron icon 16px. Hover: border becomes `--accent`.
3. Progress segments: 5 pills, each `w-10 h-1 rounded-full`, gap 8px.
   - Active (first): solid `--accent`.
   - Inactive: `--bg-elev-2` (`#1A1A1A`).
4. 16px horizontal gap between the arrow buttons and the segment group.

---

## 8. Bottom — Trusted-By Logo Strip

1. Centered label above the logos: `TRUSTED BY STARTUPS AND COMPANIES WORLDWIDE` — 12px, uppercase, tracking `0.2em`, color `--text-dim`, centered.
2. 32px gap → row of 6 monochrome logos, evenly distributed via `flex justify-between items-center` inside `max-w-[1200px] mx-auto`.
3. Logo names (left → right): `frame`, `Workflo`, `kabo`, `lendsqr`, `sanity`, `veed`. Each is wordmark + small geometric mark.
4. All logos rendered in `#4A4A4A` (muted gray). On row hover OR individual hover, transition to `#9A9A9A` over 200ms. Do NOT colorize.
5. Height of each logo: ~28px. Use inline SVG; do NOT use raster images.

---

## 9. Background Effects (subtle, do not overpower)

1. **Vignette:** full-section overlay `background: radial-gradient(ellipse at center, transparent 55%, rgba(0,0,0,0.6) 100%)`, `pointer-events-none`.
2. **Particle field (optional, Three.js or Canvas):**
   - ~80 tiny amber dots (1–2px), opacity 0.15–0.35, randomly placed across the right two-thirds of the hero.
   - Slow vertical drift (8–20s loops), independent per particle. No mouse interaction required.
   - Implementation: a single `<canvas>` absolutely positioned, `mix-blend-mode: screen`.
3. **Top edge highlight:** very faint amber line at the very top of the section — `linear-gradient(90deg, transparent, rgba(245,165,36,0.25), transparent)` 1px tall, 60% width centered.

---

## 10. Motion (use Framer Motion or GSAP)

1. On mount, stagger the left column children top-to-bottom with `y: 16 → 0`, `opacity: 0 → 1`, 80ms stagger, 600ms duration, easing `cubic-bezier(0.22, 1, 0.36, 1)`.
2. Portrait fades in with `opacity: 0 → 1` over 900ms and `scale: 1.02 → 1` (subtle settle).
3. Rim-light radial gradient pulses gently: `opacity: 0.6 ↔ 1` over 6s, infinite, ease-in-out.
4. "Top 5% Vetted Engineer" pill: amber glow breathes (`box-shadow` opacity 0.12 ↔ 0.28) over 4s.
5. Carousel auto-advances every 6s, active segment animates its `width` from 0 → full as a progress bar (use `transform: scaleX`), reset on advance.
6. CTA arrow: on hover, translate `x: +4px` over 200ms; on press, scale `0.98`.
7. Logo strip: respect `prefers-reduced-motion` — disable all loops and stagger.

---

## 11. Responsive Behavior

1. ≥ 1280px: layout exactly as described above.
2. 1024–1279px: shrink H1 to 56px, portrait height to 600px, profile card width to 280px, side gutters to 48px.
3. 768–1023px: drop the right profile card under the portrait as a horizontal row (pill + name + quote stacked, contact rows side by side). Carousel dots remain centered.
4. < 768px: single column. Order top→bottom: eyebrow, H1, lede, paragraph, CTAs (stack full-width), stat chips (horizontal scroll OR 1-per-row), portrait (full-width, max-height 420px, masked with bottom fade), profile pill + name + quote + contacts (centered), carousel, logo strip (2 rows of 3, gap 24px).
5. H1 mobile size: 36px / 42px. Body 16px. Maintain accent colors and amber rim.

---

## 12. Accessibility

1. Portrait `<img alt="Alim Ul Karim, Chief Software Engineer, photographed in a studio with warm rim lighting.">`.
2. All decorative SVGs (background flowchart, particles, vignette) marked `aria-hidden="true"`.
3. CTA buttons are real `<a>` or `<button>` with discernible text; arrow icon is `aria-hidden`.
4. Carousel: arrow buttons have `aria-label="Previous slide"` / `"Next slide"`. Active segment has `aria-current="true"`.
5. Color contrast: white on `#0A0A0A` = AAA; muted `#B5B5B5` on `#0A0A0A` ≥ AA for body text. Amber `#F5A524` is used ONLY for ≥18px text or non-text accents, never small body copy.
6. Focus rings: 2px `--accent` outline with 2px offset, visible on keyboard nav only (`focus-visible`).

---

## 13. DOM Skeleton (reference)

```html
<section class="relative min-h-screen w-full overflow-hidden bg-[#0A0A0A] text-white">
  <div class="pointer-events-none absolute inset-0">[vignette + particles + top edge]</div>

  <div class="relative mx-auto grid max-w-[1440px] grid-cols-12 gap-6 px-20 pt-24 pb-10">
    <!-- LEFT: copy -->
    <div class="col-span-5 flex flex-col">
      <div class="eyebrow">[rule] TRUSTED BY ENGINEERING TEAMS …</div>
      <h1>Scale Your Startup with Engineers Who <span class="text-accent">Solve</span> <span class="text-accent">Real Problems</span></h1>
      <p class="lede">…</p>
      <p class="muted">…</p>
      <div class="ctas">[Book a Call] [View Our Work]</div>
      <div class="stats">[chip][chip][chip]</div>
    </div>

    <!-- CENTER: portrait + flowchart bg + rim light -->
    <div class="col-span-4 relative">[svg flowchart][rim glow][img portrait]</div>

    <!-- RIGHT: profile -->
    <div class="col-span-3 flex flex-col">
      <div class="pill">[crown] Top 5% Vetted Engineer</div>
      <div class="spacer"></div>
      <div class="name-block">[name][role][sub-role]</div>
      <blockquote>“…”</blockquote>
      <div class="contacts">[linkedin][location]</div>
    </div>

    <!-- carousel under portrait, spans center -->
    <div class="col-start-5 col-span-4 flex justify-center">[‹][▬▬▬▬▬][›]</div>
  </div>

  <div class="relative border-t border-white/5 pt-12 pb-10">
    <p class="logo-strip-label">TRUSTED BY STARTUPS AND COMPANIES WORLDWIDE</p>
    <div class="logo-row">[frame][Workflo][kabo][lendsqr][sanity][veed]</div>
  </div>
</section>
```

---

## 14. Acceptance Checklist (the implementing AI must self-verify)

- [ ] Background is true near-black, never blue-tinted.
- [ ] Only ONE accent hue (`#F5A524` / `#FF9500`) appears; no secondary brand colors.
- [ ] H1 amber spans are *only* on the words "Solve" and "Real Problems".
- [ ] Eyebrow has the short amber rule on its left and uses middle-dot separators.
- [ ] Primary CTA glows; secondary CTA does not.
- [ ] Stat chips: amber icon tile + bold white number + muted gray label, exactly three.
- [ ] Portrait composites directly on the black background — no border, no rounded mask, no card.
- [ ] Faint amber flowchart SVG sits behind the portrait at ≤ 25% opacity.
- [ ] Profile column is unframed; uses generous whitespace.
- [ ] Big amber opening + closing quote marks wrap the pull quote.
- [ ] Carousel: 5 segments, first one active in amber; round chevron buttons flanking.
- [ ] Logo strip is monochrome gray, centered, with uppercase tracked label above.
- [ ] Layout collapses gracefully at 1024, 768, and 375 widths per §11.
- [ ] All motion respects `prefers-reduced-motion`.
- [ ] Lighthouse a11y ≥ 95; no contrast violations on body text.