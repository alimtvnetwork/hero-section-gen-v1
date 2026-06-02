# Naming Conventions

**Authority:** This file overrides every other naming reference in the spec.

| Surface | Convention | Example | Anti-example |
|---|---|---|---|
| Analytics events | `snake_case`, prefix with feature | `hero_cta_click`, `hero_view` | `heroCtaClick`, `HeroCtaClick` |
| Event properties | `snake_case` | `cta_label`, `variant_id` | `ctaLabel` |
| Feature flags | `SCREAMING_SNAKE`, prefix with feature | `HERO_SAFE_MODE`, `HERO_3D_ENABLED` | `heroSafeMode` |
| Env vars (server) | `SCREAMING_SNAKE`, no prefix needed | `CALENDLY_API_KEY` | `calendlyApiKey` |
| Env vars (client) | `VITE_` + `SCREAMING_SNAKE` | `VITE_CMS_URL` | `VITE_cmsUrl` |
| CSS custom properties | `--kebab-case`, role-prefixed | `--color-accent`, `--space-4` | `--accent`, `--Accent` |
| Tailwind utility tokens | Tailwind v4 `@theme` keys | `--color-bg-elev`, `--font-display` | inline hex |
| React components | `PascalCase`, file matches export | `HeroPortraitCard.tsx` | `hero-portrait-card.tsx` |
| React hooks | `camelCase`, `use*` prefix | `useHeroCarousel` | `UseHeroCarousel` |
| TanStack route files | `kebab-case`, dot-nested | `hero.about.tsx` | `Hero/About.tsx` |
| Spec files | `NN-kebab-case.md` (numeric prefix sortable) | `02-spec-canvas.md` | `SPEC-PART-2.md` *(grandfathered)* |
| Test fixtures | `kebab-case.fixture.ts` | `engineer-profile.fixture.ts` | `engineerProfile.test.ts` |
| Image assets | `kebab-case`, versioned with `-vN` | `hero-portrait-amber-v2.webp` | `HeroPortrait_v2.webp` |
| CMS keys | `dot.case`, feature-prefixed | `hero.headline`, `hero.cta_primary` | `heroHeadline` |
| JSON-LD `@id` | absolute URL of the canonical page | `https://example.com/#hero` | random uuid |

## Rules

1. **Never mix** conventions inside one surface. A single inconsistency is a PR-block.
2. **Renames are breaking** — bump analytics event `v` (see CONVENTIONS for event schema versioning).
3. **Grandfathered files** (`SPEC.md`, `SPEC-PART-N.md`) keep current names until the spec-file rename pass (post-MVP) to avoid breaking inbound links.
4. **No abbreviations** that aren't industry-standard. `cta`, `seo`, `ui`, `id` ok. `eng`, `prtl`, `cfg` not ok.