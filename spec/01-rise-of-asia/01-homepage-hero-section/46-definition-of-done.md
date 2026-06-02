# Definition of Done — Hero Section

**Authority:** This is the final 100/100 ship-gate. A hero PR or release is **not done** until every row below is `[x]` with linked evidence. Visual match alone is not enough.

## Sign-off matrix (12 gates)

| # | Gate | Owner | Evidence path | Pass criteria |
|---|---|---|---|---|
| 1 | **Spec compliance** | @hero-owner | `00-index.md` reading order followed; PR description lists touched files | All P0s in `45-priorities.md` ✅; no new P0 introduced. |
| 2 | **Visual diff = 0** | @design @qa | `snapshots/hero/diffs/` empty | Every baseline in `22-snapshots.md` within `maxDiffPixelRatio` tolerance. |
| 3 | **Tokens** | @design | `src/styles.css` matches `02-tokens.md` verbatim | No raw hex in JSX; no Tailwind v3 patterns. |
| 4 | **Component contract** | @hero-owner | `Hero.types.ts` matches `04-component-contract.md` | Type test (`tsc --noEmit`) + runtime invariants logged in dev. |
| 5 | **Edge cases EC-01..EC-20** | @qa | Storybook stories + Playwright snapshots all green | 0 failing fixtures. |
| 6 | **States** | @qa | Loading / empty / error / offline / slow snapshots | All present at 1440 + 375. |
| 7 | **Motion + reduced-motion** | @design | `23-motion.md` table covered; S-1440-rm snapshot green | No `transition:` leaks under `prefers-reduced-motion`. |
| 8 | **A11y AAA** | @a11y | `axe-core` 0 violations; manual VoiceOver + NVDA scripts run | Contrast ≥ 7:1 for body text; carousel ARIA per `24-a11y.md`. |
| 9 | **Performance budgets** | @perf | Lighthouse CI report attached | Every assertion in `27-budgets.md` passes; LCP p75 ≤ 2.0s (RUM). |
| 10 | **Security / privacy** | @security @privacy | CSP enforce header live; secrets unchanged or rotated; no PII in fixtures | `curl -I` shows enforce CSP; `git grep` of secret patterns empty. |
| 11 | **Copy + legal** | @copy @legal | All changed strings approved per `38-copy-owners.md` | Legal-review keywords either absent or `@legal` approved. |
| 12 | **Observability + DoD checklist signed** | @platform-sre @hero-owner | SLOs in monitoring; runbook linked; this checklist file linked in PR | All 11 above `[x]`; on-call notified of release. |

## Device matrix (gate 2 / 5 / 6 must run on each)

| Device | Browser | Required |
|---|---|---|
| iOS 16 / 17 / 18 | Safari | yes |
| Android 12+ | Chrome | yes |
| Desktop | Chrome | yes |
| Desktop | Firefox | yes |
| Desktop | Safari | yes |
| Desktop | Edge | yes |
| Windows | High-contrast mode | yes (gate 8) |

Owner: @qa. Failing on any required device = gate 2/5/6 fail.

## PR template snippet (auto-injected)

```md
## Definition of Done — hero
- [ ] 1. Spec compliance — link: __
- [ ] 2. Visual diff = 0 — link: __
- [ ] 3. Tokens — link: __
- [ ] 4. Component contract — link: __
- [ ] 5. Edge cases EC-01..EC-20 — link: __
- [ ] 6. States — link: __
- [ ] 7. Motion + reduced-motion — link: __
- [ ] 8. A11y AAA — link: __
- [ ] 9. Performance budgets — link: __
- [ ] 10. Security / privacy — link: __
- [ ] 11. Copy + legal — link: __
- [ ] 12. Observability + sign-off — link: __
```

## Release rule

- Any unchecked gate = PR cannot merge to `main`.
- Emergency hotfix: gate 1, 2, 9, 10 mandatory; others may be waived with a `WAIVER.md` entry (expiry ≤ 7 d) and `@hero-owner` + `@platform-sre` co-sign.

## Score

When all 12 gates are `[x]` for a release, the spec is **100/100 AI-shippable** for that release. A blind AI handed `00-index.md` builds the canonical hero without further input.