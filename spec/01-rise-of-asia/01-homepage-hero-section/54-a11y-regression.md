# A11y Regression Matrix — Wiring

**Owner:** A11y · **Closes:** #95 (graduates from 24-a11y.md + 22-snapshots.md stubs)

## CI matrix (per PR)
| Tool | Scope | Gate |
|---|---|---|
| `@axe-core/playwright` | Hero page, all locales (en/de/ar) | 0 violations |
| `pa11y` | Hero URL, WCAG2AAA | 0 errors |
| Playwright | Keyboard-only nav script (Tab order = `24-a11y.md` §dom-order) | pass |
| Playwright | Screen-reader snapshot (NVDA via `aria-snapshot`) | matches baseline |
| Storybook + `addon-a11y` | Every story | 0 violations |

## Manual matrix (per release, owner: QA)
| Tech | Browser | Pass criteria |
|---|---|---|
| NVDA 2024+ | Firefox | All landmarks, slide-change announced |
| JAWS 2024 | Chrome | Same |
| VoiceOver | Safari macOS + iOS | Same + rotor lists hero region |
| TalkBack | Chrome Android | Swipe gestures preserved |
| Dragon NaturallySpeaking | Edge | All CTAs voice-clickable by label |

## Failure handling
Any new violation → block merge → triage in `#hero-a11y` within 1 business day.
