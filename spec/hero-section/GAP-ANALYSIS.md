# Gap Analysis — Canonical vs. Archived Reference

> **Canonical (build to this):** `./00-hero-canonical.png` — the image the user re-supplied on 2026-06-02. This is the single source of truth.
> **Archived (do NOT build this):** `./01-hero-variant-archived.png` — an earlier variant of the same design. Kept only for context.
>
> Wherever `SPEC.md` and `SPEC-PART-2..5.md` disagree with this document, **this document wins.**

---

## TL;DR of the gap

The original `SPEC.md` was written against the archived variant. It mis-specified at least **15** items. The corrections are listed below, grouped by section. Composition is largely the same (left copy column, center portrait, right profile, dark/amber palette), but the canonical reference is **simpler, warmer, and more cinematic** — fewer decorative elements, more reliance on lighting and typography.

---

## 1. Differences in the portrait subject

| Aspect | Canonical (build) | Archived (ignore) |
|---|---|---|
| Subject | Older man, light goatee, **no glasses**, **light dress shirt + dark blazer** | Younger man, full beard, **thin-rim glasses**, **dark button-up** |
| Expression | Soft confident half-smile, looking just left of camera | Neutral, looking up-right |
| Lighting | Strong warm amber wash from the right side that wraps the entire right half of the face and jacket | Subtle amber rim only on shirt edge |

**User intent (their words):** *"the picture for this person is all right, and that person could go into that hero section image or in future, we can change the image for that person as well. Remember that the changing of the person image should be very smooth."*

→ **Treat the portrait as a swappable slot, not a baked-in asset.** See §10 of this doc for the contract.

---

## 2. Headline — accent treatment is **different**

- Canonical line break: `Scale Your Startup` / `with Engineers Who` / **`Solve Real Problems`** (line 3, full phrase amber + **italic**).
- Archived: `Scale Your Startup with` / `Engineers Who ` **`Solve`** / **`Real Problems`** (amber on individual words across two lines, **not italic**).

**Correction (overrides SPEC §3.4 and §4.3):**
- Accent phrase is the **single contiguous phrase `Solve Real Problems`**.
- Style: `color: var(--accent)` **AND** `font-style: italic`.
- Place it on its own line (line 3), no per-word splits.
- For i18n (Part 3 §27), the `accentWords` array becomes `accentPhrase: string` instead.

---

## 3. Subtitle line on the right card — case + tracking changed

- Canonical: `AI, .NET DEVELOPER` — **uppercase**, tracking ~`0.15em`, weight 500, muted gray.
- Archived: `AI, .NET Developer` — sentence-case, default tracking.

**Correction (overrides SPEC §6.2):** uppercase + tracked. Treat it as a meta/eyebrow line, not body text.

---

## 4. Pull quote treatment — bar, not quote marks

- Canonical: quote is **italic**, prefixed by a **vertical amber bar** (≈3px wide, full height of the quote, `var(--accent)`, ~16px gap to the text).
- Archived: large amber opening/closing `"..."` characters wrapping non-italic text.

**Correction (overrides SPEC §6.3):**
- Remove the giant quote-mark glyphs.
- Render: `<blockquote class="border-l-[3px] border-[--accent] pl-4 italic text-[20px] leading-[30px] text-white/95">…</blockquote>`.
- Drop the closing quotation mark entirely.

---

## 5. Badge text — "5%" not "1%"

- Canonical: `Top 5% Vetted Engineer`.
- Archived: `Top 1% Vetted Engineer`.

**Correction (overrides SPEC §6.1):** copy is `Top 5% Vetted Engineer`. Keep this as a CMS-editable string (`engineer.badge`) — both values are legitimate marketing claims at different positioning levels.

---

## 6. Background flowchart SVG — **remove entirely**

- Canonical: **no** behind-portrait flowchart. The background is a clean black with a warm amber radial glow on the right.
- Archived: faint dashed flowchart with `Fetch / Process / Render` boxes behind the portrait.

**Correction (overrides SPEC §5.5 and Part 2 §17):** delete §17 of Part 2. Do not author the flowchart SVG. The amber rim-light composite from Part 2 §18 stays — it does the visual work alone.

---

## 7. Logo strip at the bottom — **remove from the hero**

- Canonical: hero ends at the carousel controls. **No** "Trusted by startups…" label, **no** 6-logo row.
- Archived: full logo strip with label.

**Correction (overrides SPEC §8 and Part 2 §23):**
- Remove the logo strip from this section.
- If a "trusted by" strip is still wanted on the page, **move it to a separate `<TrustedBy/>` section below the hero** — it is not part of the hero contract.
- Skip Part 2 §23 implementation. Re-budget that 60 min away.

---

## 8. Stat chips — no card background

- Canonical: stat chips are **icon + text on the section background**, no bordered card, no `--bg-elev` fill. They read as a 3-up row of icon-text pairs separated by ~48px of horizontal space.
- Archived: each chip is a bordered card with `bg-[--bg-elev]` and `border`.

**Correction (overrides SPEC §4.7 and Part 2 §22):**
- Drop the chip background, border, and padding.
- Layout: `flex items-start gap-12`, each item is `flex items-center gap-3` (icon tile 32×32 amber-tinted bg + text stack).
- Icon tile keeps its rounded amber-tinted square — only the outer chip card is removed.

---

## 9. Carousel controls — relocated and simpler

- Canonical: small `‹` and `›` round buttons + **3 progress segments** (looks like the design supports 3 slides, not 5), positioned in the **bottom-right** of the hero, just under the right profile column.
- Archived: `‹` + **5 segments** + `›`, centered under the portrait.

**Correction (overrides SPEC §7 and Part 2 §21):**
- Default slide count: **3** (still data-driven; the count adapts to `engineers.length`).
- Position: anchored bottom-right of the hero, ~32px right padding from the section edge, aligned under the right profile column — not centered under the portrait.
- Buttons are smaller: 32×32 (was 40×40). Border kept.
- Auto-advance timing unchanged (6s).

---

## 10. Hairline divider on the right column

- Canonical: a thin horizontal hairline (`1px`, `border-white/10`) **between the pull quote and the contact rows**.
- Archived: no divider.

**Correction (additive to SPEC §6):** add `<hr class="border-t border-white/10 my-5">` between blockquote and contact rows.

---

## 11. Background lighting — stronger amber radial on the right

- Canonical: the right ~40% of the section has a **noticeably warmer ambient glow**, wrapping behind the portrait and the profile column. The left column sits in deeper near-black.
- Archived: subtler glow tied only to the portrait silhouette.

**Correction (additive to Part 2 §18):** add a third gradient layer covering the right 40% of the section:
- `radial-gradient(ellipse 90% 80% at 80% 50%, rgba(255,149,0,0.10), transparent 70%)`, no blur, behind everything. Sits below Layer A (halo) and Layer B (hot edge) from Part 2 §18.

---

## 12. Body paragraph wrap width

- Canonical: paragraph wraps at ~`max-w-[440px]` — narrower; wraps at "production-/ready" hyphenation.
- Archived: wraps at ~`max-w-[560px]`.

**Correction (overrides SPEC §4.5):** body paragraph max-width is **440px**, not the column's full 560px. The lede paragraph above it stays narrower still (~`max-w-[460px]`). Both should sit comfortably above the CTAs without filling the column edge-to-edge.

---

## 13. Eyebrow row hairline

- Canonical: the amber rule before "TRUSTED BY…" is **shorter (16px)** and **lower-contrast** (`var(--accent)` at ~70% opacity). Almost a hint, not a banner.
- Archived: 24px solid amber rule.

**Correction (overrides SPEC §4.1):** 16px wide, 1px tall, `var(--accent)/70`. 12px gap to text unchanged.

---

## 14. Section height feels shorter

The canonical reference is composed at a wider aspect ratio (no logo strip at the bottom, no large gap below the carousel). Practical effect:

**Correction (overrides SPEC §1.1 and §1.4):**
- Section height: `min-h-[760px]` on desktop (was `min-h-screen`). On viewports ≥ 900px tall, do **not** stretch — let the section breathe at its natural ~760px height with the dark page background continuing below.
- Bottom padding `pb-16` (was `pb-10`) — replaces the visual "weight" the logo strip used to provide.

---

## 15. Stat numbers — slightly larger and bolder

- Canonical: stat values look ~20px (heavier weight 700, same amber-tile icon).
- Archived: 18px weight 700.

**Correction (overrides SPEC §3.4 stat number sizing):** `20px / 24px`, weight 700, white. Stat label stays `12px` muted.

---

## Smooth portrait-swap contract (per user request)

The user explicitly wants the portrait to be swappable smoothly. Codify this:

1. **Portrait is a discrete swappable slot.** The portrait `<img>` lives inside a `relative` container; nothing in the layout depends on its specific silhouette. Layout reserves a fixed-aspect box (`aspect-[4/5]`, `max-h-[640px]`).
2. **Background lighting is independent of the portrait.** All three rim-light gradient layers (§11 above + Part 2 §18) are siblings of the portrait, not children — swapping the photo does not require re-positioning lights.
3. **Swap transition:** when the portrait changes (carousel advance OR CMS publish), cross-fade the old → new image over **250ms** with `mode="wait"` (no scale, no slide — pure opacity to feel "smooth", not "flashy"). Pre-load the next slide's portrait on hover of the right `›` button and 1s before auto-advance to eliminate flash-of-no-image.
4. **Portrait CMS field is image + amber-side hotspot** (Part 3 §34 already specifies hotspot). The hotspot tells the rim-light system whether the warm side is the portrait's left or right — flips Layer B's `at X%` value accordingly. This is the *only* portrait-dependent piece.
5. **Aspect-box fallback:** if a new portrait has a different intrinsic aspect, `object-fit: cover` with `object-position` driven by the hotspot. Layout never shifts.
6. **Acceptance test:** swapping `engineer.portraitSrc` in dev tools to a totally different photo (different person, different pose) should result in a clean cross-fade with rim light still anchored correctly, no layout shift, no z-index glitch.

This is what "very smooth portrait change" requires in practice — not a fancy animation, but **zero layout coupling** between the image and everything around it.

---

## Updated acceptance-checklist deltas (apply to SPEC §14)

Replace these items in the original checklist:
- ~~Faint amber flowchart SVG behind portrait at ≤25% opacity~~ → **removed**.
- ~~Logo strip is monochrome gray, centered, with uppercase tracked label~~ → **moved out of hero**.
- ~~Carousel: 5 segments, first one active in amber; round chevron buttons flanking~~ → **3 segments, anchored bottom-right of the hero**.
- ~~Big amber opening + closing quote marks wrap the pull quote~~ → **replaced with vertical amber bar; quote text is italic**.
- ~~H1 amber spans are only on the words "Solve" and "Real Problems"~~ → **single italic amber phrase "Solve Real Problems" on line 3**.

Add:
- Right column shows a hairline divider between quote and contacts.
- "AI, .NET DEVELOPER" rendered uppercase + tracked.
- Stat chips are unframed (icon + text only, no card bg).
- Portrait swap test: cross-fade between two visually distinct portraits produces no layout shift and lighting remains correct.

---

## Re-budget impact

Net effect of these corrections on previously estimated time:

| Change | Time delta |
|---|---|
| Remove flowchart SVG authoring (Part 2 §17) | **−30 min** |
| Remove logo strip wordmark set (Part 2 §23) | **−60 min** (if logo strip not re-built elsewhere) |
| Adjust carousel from 5→3 segments + reposition | **+10 min** |
| Add hairline divider + italic quote + bar | **+10 min** |
| Add third radial gradient layer (right ambient) | **+10 min** |
| Portrait smooth-swap contract (cross-fade + preload + hotspot wiring) | **+45 min** |
| **Net delta** | **≈ −15 min** (slightly faster) |

The canonical design is actually a touch simpler to ship than the archived one — fewer decorative elements, more lighting.
