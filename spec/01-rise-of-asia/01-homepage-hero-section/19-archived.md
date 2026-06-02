# Archived Spec Sections — Do Not Build

These sections appear in the spec history but are explicitly **out of scope** for the canonical hero. A blind AI must skip them.

| Section | File | Reason archived | Replacement |
|---|---|---|---|
| §17 "How it works" flowchart | `14-spec-part-2.md` | Not present in `00-hero-canonical.png`; clutters above-the-fold. | None — content moves to a dedicated `/process` route post-MVP. |
| §23 In-hero logo strip | `14-spec-part-2.md` | Canonical shows logos as a separate section below the hero, not inside it. | Render logo strip in its own component, not as a child of `<Hero>`. |
| SPEC §2 raw-hex token block | `13-spec.md` | Tailwind v3 pattern; this repo is v4. | `02-tokens.md` + `src/styles.css` `@theme` block. |
| GAP-ANALYSIS overrides | `20-gap-analysis.md` | All deltas promoted into `13-spec.md` v1.0.0. | `13-spec.md` is now canonical for visuals. |
| `Top 1% Vetted Engineer` copy | various | Replaced with `Top 5% Vetted Engineer` per canonical reference. | `engineer.badge` CMS key, default `"Top 5% Vetted Engineer"`. |
| `./01-hero.png` reference | `13-spec.md`, `14-spec-part-2.md` | File renamed during cleanup. | `./00-hero-canonical.png`. |

## Rule for the future

When a section is archived, add a row here with a replacement (or "None"). Do **not** delete the original prose — keep it in place so historical PRs remain readable, but add an `> **ARCHIVED — see 19-archived.md**` blockquote at the top of the section.