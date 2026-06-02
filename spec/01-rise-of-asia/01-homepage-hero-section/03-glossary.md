# Glossary — Visual & Technical Terms

**Authority:** Disambiguates rim/halo/wash terminology. Use only these terms in the spec, PRs, and Storybook.

## Lighting

| Term | Definition | Implementation layer |
|---|---|---|
| **Rim light** | Hard amber edge along one side of the subject (camera-left in canonical). Part of the **portrait image itself**, baked at generation time. | Image — never CSS. |
| **Hot edge** | The brightest pixel cluster of the rim light, ~5% of subject silhouette. Used for `accent` color sampling and Three.js particle anchor. | Image. |
| **Halo** | Soft amber glow extending 60–120px **outside** the subject silhouette. Achieved via a separate radial gradient layer behind the portrait. | CSS `radial-gradient` layer. |
| **Wash** | Low-opacity (8–12%) amber fill covering the entire portrait zone. Sits between bg and portrait. | CSS layer. |
| **Warm side** | The image half receiving the rim light (camera-left). Used to bias text/profile-card placement on the **cool side** (camera-right). | Conceptual; no CSS. |
| **Cool side** | Opposite of warm side. Where the profile card sits in canonical. | Conceptual. |

## Motion

| Term | Definition |
|---|---|
| **Entrance** | First-paint animation, ≤ 640ms, fires once. |
| **Auto-advance** | Carousel timed slide change, 6000ms default. |
| **Cross-fade** | Replacement transition used in `prefers-reduced-motion`. |
| **Slide** | Default carousel transition (horizontal translate + fade). |
| **Breathe** | 4s ease-in-out opacity loop on the badge pill glow. |

## Data

| Term | Definition |
|---|---|
| **Engineer** | One person record. |
| **Slide** | One engineer rendered in the carousel. |
| **Segment** | The bottom-right `01 / 03` counter. Independent of slide count; always shows totals. |
| **Hotspot** | Optional rim-light highlight marker over the portrait, max 3 per engineer. |
| **Variant** | A/B experiment branch. Tagged on every analytics event via `variant_id`. |

## Forbidden synonyms

| Don't write | Write instead |
|---|---|
| "glow" (alone) | halo, wash, or hot edge — pick one |
| "highlight" | rim light (image) or hot edge (pixel cluster) |
| "card" (for slide) | slide |
| "dot" (for segment counter) | segment |
| "amber" + a hex | `--color-accent` |