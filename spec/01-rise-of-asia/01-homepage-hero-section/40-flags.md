# Feature Flag Matrix

**Authority:** Every hero flag listed here with per-environment default. Untracked flags are forbidden.

## Matrix

| Flag | Type | Dev | Preview | Staging | Prod | Owner | Retire by |
|---|---|---|---|---|---|---|---|
| `HERO_3D_ENABLED` | boolean | true | true | true | true | @hero-owner | n/a (permanent capability) |
| `HERO_SAFE_MODE` | boolean | false | false | false | false | @hero-owner | n/a (kill-switch) |
| `HERO_AUTO_ADVANCE_MS` | number | 6000 | 6000 | 6000 | 6000 | @hero-owner | n/a |
| `HERO_VARIANT` | enum: `control` \| `tighter-copy` \| `bigger-cta` | `control` | `control` | `control` | `control` | @growth | 2026-09-01 |
| `HERO_PARTICLE_DENSITY` | number 0..1 | 0.6 | 0.6 | 0.5 | 0.4 | @perf | n/a |
| `HERO_CALENDLY_FALLBACK_MAILTO` | string | `mailto:hello@example.com` | same | same | same | @growth | n/a |
| `HERO_DEBUG_GRID` | boolean | env-flag only | false | false | false | @hero-owner | n/a (dev only) |
| `HERO_DEBUG_SAFE_AREAS` | boolean | env-flag only | false | false | false | @hero-owner | n/a |
| `HERO_PREVIEW_MODE` | boolean | true | true | false | false | @content | n/a |
| `HERO_ANALYTICS_SAMPLE` | number 0..1 | 0 | 0.1 | 1.0 | 1.0 | @data | n/a |

## Override mechanism

- Server flags: env vars (`HERO_*`) read at request time via TanStack server context.
- Client overrides (dev only): URL query (`?HERO_DEBUG_GRID=1`) — parsed via `useDebugFlag()` helper, ignored when `NODE_ENV === 'production'`.
- Persistent overrides: forbidden in prod. Dev/preview only via `localStorage.heroFlags`.

## Retirement

- Each non-permanent flag carries a `Retire by` date.
- 14 days before due, CI opens a removal PR scaffolded from the flag-removal template.
- Owner accepts (removes flag + branches) or requests an extension with reason.
- Flags overdue by 30+ days fail CI on every PR (forces resolution).

## Acceptance

- Every used flag in source (`grep -rE "HERO_[A-Z_]+"` in `src/`) appears in this matrix.
- Snapshot tests run with `HERO_VARIANT=control` and the active variant.
- Kill-switch test: setting `HERO_SAFE_MODE=true` renders the SSR baseline (per `12-threejs-fallback.md`) with no JS-driven features.