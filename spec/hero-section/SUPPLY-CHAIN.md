# Supply Chain — npm Dependency Rules

**Authority:** Lockfile-required, audit-gated, Dependabot-maintained. Floating ranges forbidden in CI.

## Rules

1. **Lockfile committed** (`bun.lockb` or `package-lock.json`). PRs without an updated lockfile when `package.json` changed = fail.
2. CI runs `bun install --frozen-lockfile` (or `npm ci`). Any drift = fail.
3. **Audit gate:** `npm audit --omit=dev --audit-level=high` exits non-zero → CI red.
4. **Dependabot** weekly PRs for `patch` + `minor`. `major` requires human review + entry in `CHANGELOG.md`.
5. **Provenance** (npm `--provenance`) required for any package published from this repo.
6. **Pinning policy:** runtime deps pinned to exact versions (`"react": "19.0.1"`, not `"^19.0.1"`). Dev deps may use `^`.

## Allowlist (runtime, hero scope)

| Package | Version | Purpose |
|---|---|---|
| `react`, `react-dom` | 19.x | framework |
| `@tanstack/react-router` | 1.x | routing |
| `@tanstack/react-start` | 1.x | SSR |
| `@tanstack/react-query` | 5.x | data |
| `framer-motion` | 11.x | motion |
| `three` | 0.16x | particle field (lazy chunk only) |
| `lucide-react` | 0.4x | icons |
| `zod` | 3.x | validation |
| `seedrandom` | 3.x | deterministic variant pick |
| `@fontaine/vite-plugin` | latest | metric matching |

## Denylist (do not add without RFC)

| Package | Reason |
|---|---|
| `gsap` | license (paid for some uses); framer-motion covers needs |
| `swiper`, `embla-carousel-react`, `keen-slider` | carousel is hand-rolled — adding a lib bloats bundle and conflicts with ARIA pattern |
| `axios` | use native `fetch` |
| `moment` | use native `Intl` + `Temporal` |
| `lodash` (full) | tree-shake individual `lodash-es/*` or use platform APIs |
| `styled-components`, `@emotion/*` | repo is Tailwind v4; no CSS-in-JS |

New additions: open RFC in `spec-issues/`, get `@platform` + `@hero-owner` approval.

## Provenance check

Weekly job verifies every runtime dep has a signed provenance attestation. Missing → file an issue; downgrade to last good version.

## Acceptance

- `bun install --frozen-lockfile` succeeds in CI.
- `npm audit` clean at high+.
- No package in denylist appears in `package.json` (`grep -E '"(gsap|swiper|axios|moment)"' package.json` = no match).