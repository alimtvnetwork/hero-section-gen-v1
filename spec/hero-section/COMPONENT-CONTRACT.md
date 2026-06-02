# Component Contract — `<Hero />`

**Authority:** Single props-shape source. SPEC-PART-3 §33 (HeroProps) is **derived from** this file; if they disagree, this file wins.

## Public API

```ts
// src/components/hero/Hero.types.ts
import type { LucideIcon } from "lucide-react";

export type Engineer = {
  id: string;                 // CMS uuid; stable across renames
  name: string;
  role: string;               // "Chief Software Engineer"
  subRole?: string;           // "AI, .NET Developer"
  portraitSrc: string;        // 2x URL, .webp; .avif via <picture>
  portraitSrcAvif?: string;
  portraitSrcJpeg: string;    // emergency fallback
  portraitAlt: string;        // required; non-empty
  portraitFocalPoint?: { x: number; y: number }; // 0..1, default {0.5, 0.4}
  quote: string;              // 80–160 chars (see COPY-BUDGETS.md)
  linkedinUrl?: string;
  location?: string;
  badge?: string;             // default "Top 5% Vetted Engineer"
  hotspots?: HotspotMark[];   // optional rim-light highlights, max 3
};

export type HotspotMark = { x: number; y: number; label: string };

export type Stat = { icon: LucideIcon; value: string; label: string };

export type HeroProps = {
  eyebrow: string;
  headline: { text: string; accentWords: string[] };
  lede: string;
  body: string;
  primaryCta: CtaSpec;
  secondaryCta: CtaSpec;
  stats: Stat[];              // length === 3, runtime-warned
  engineers: Engineer[];      // length >= 1; if 1, carousel controls hidden
  autoAdvanceMs?: number;     // default 6000; 0 disables
  segments?: 1 | 2 | 3;       // bottom-right carousel segments, default 3
  onSlideChange?: (i: number) => void;
  variantId?: string;         // analytics variant tag, default "control"
};

export type CtaSpec = {
  label: string;
  href?: string;
  onClick?: () => void;
  analyticsId: string;        // REQUIRED — fires hero_cta_click {cta_id}
};
```

## Invariants (runtime-enforced via `assert` in dev)

1. `stats.length === 3` else `console.error` + render first 3.
2. `engineers[].id` unique.
3. `portraitAlt.length > 0`.
4. No `className` on root — style via tokens only.
5. `accentWords` must each appear in `headline.text` as whole words; missing words = warn, not throw.

## Owned vs delegated

- **Owned by `<Hero />`:** layout, motion, carousel, portrait composition, analytics fire.
- **Delegated:** Calendly modal (`<CtaCalendly />`), particle background (`<HeroParticles />`), logo strip (rendered **outside** `<Hero />` per `ARCHIVED.md`).

## Forbidden

- Passing children. Hero is closed; configure via props.
- Mutating `engineers` after mount (use key change to re-init).
- Adding new props without bumping `MINOR` in `CHANGELOG.md`.