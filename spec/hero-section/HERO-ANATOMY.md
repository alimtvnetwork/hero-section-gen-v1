# `<Hero />` Component Anatomy

**Authority:** Numbered map from on-screen elements → props → tokens → analytics → acceptance rules. Pair with `00-hero-canonical.png`.

## Overlay (text reference of the canonical numbered diagram)

```
 ┌──────────────────────────────────────── HERO SECTION ──────────────────────────────────────────┐
 │                                                                                                │
 │  [1 eyebrow]                                              ┌────── [9 portrait zone ──────┐    │
 │  [2 H1 headline (3 accent words)]                         │                              │    │
 │  [3 lede]                                                 │   [10 rim-light]             │    │
 │  [4 body]                                                 │                              │    │
 │  [5 primary CTA] [6 secondary CTA]                        │                              │    │
 │                                                           │                              │    │
 │  [7 stat chip × 3 ───────────────────]                    │                  [11 profile │    │
 │                                                           │                     card]   │    │
 │                                                           └──────────────────────────────┘    │
 │                                                                                                │
 │                                                    [12 carousel dots] [13 segment counter 3]   │
 └────────────────────────────────────────────────────────────────────────────────────────────────┘
    [14 logo strip — RENDERED OUTSIDE <Hero /> per ARCHIVED.md §23]
```

## Element → contract table

| # | Element | Prop | Token(s) | Analytics | Acceptance |
|---|---|---|---|---|---|
| 1 | Eyebrow | `eyebrow` | `--color-accent`, `font-display` 12/16 600 uppercase | — | Single line @ all viewports per COPY-BUDGETS. |
| 2 | Headline | `headline.text` + `accentWords[]` | `--color-text`, accents `--color-accent`, 64/72 700 | — | 2 lines @ 1440; 3 lines max @ 375; no clip. |
| 3 | Lede | `lede` | `--color-text` 90%, 18/28 500 | — | ≤2 lines @ 1440; ≤4 @ 375. |
| 4 | Body | `body` | `--color-text-muted`, 14/22 400 | — | Optional; clamp 6 lines @ 375. |
| 5 | Primary CTA | `primaryCta` | `--color-accent-strong` fill, `--color-bg` text | `hero_cta_click {cta_id, position:"primary"}` | Always visible above fold; ≥44px tap target. |
| 6 | Secondary CTA | `secondaryCta` | `--color-bg-elev-2` fill, `--color-text` text, `--color-border-strong` | `hero_cta_click {cta_id, position:"secondary"}` | Same row as primary @ ≥768; stacked below @ 375. |
| 7 | Stat chip | `stats[]` | `--color-bg-elev`, icon `--color-accent` | `hero_stat_view` (IntersectionObserver, once) | Exactly 3; unframed chips per canonical. |
| 9 | Portrait | `engineers[i].portraitSrc` + manifest | gradient base from `--color-bg` → `--color-accent-soft` | `hero_portrait_error` on fallback | LCP element; `fetchpriority="high"`. |
| 10 | Rim light | composed in image (manifest), NOT CSS | — | — | One canonical rim-light direction: camera-left, amber, 70° rotation. See GLOSSARY. |
| 11 | Profile card | derived from `engineers[i]` | `--color-bg-elev-2`, `--color-border-strong` | `hero_profile_linkedin_click` | Vertically centered with portrait. |
| 12 | Carousel dots | derived from `engineers.length` | `--color-accent` active, `--color-text-dim` inactive | `hero_slide_change {from, to, method}` | Hidden when `length === 1`. |
| 13 | Segment counter | `segments` prop (default 3) | `--color-text-dim` | — | Format `01 / 03`. |

## Z-order

`bg base → particles (optional) → portrait → rim light → profile card → text column → CTAs → carousel UI`. Profile card sits **above** portrait's right edge with 8px overlap.

## Don'ts

- Do NOT render the logo strip (#14) inside `<Hero />`.
- Do NOT add a hover effect to #2 (headline is not interactive).
- Do NOT animate #7 stat numbers on mount unless `prefers-reduced-motion` is unset AND the value is numeric.