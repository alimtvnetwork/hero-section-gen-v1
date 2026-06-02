# QA Device Matrix — Hero (Pre-Release)

**Owner:** QA · **Closes:** #84

## Tier 1 — blocking (every release)
| Device | OS | Browser | Viewport | Notes |
|---|---|---|---|---|
| iPhone 15 Pro | iOS 17 | Safari | 393×852 | svh/dvh, notch |
| iPhone SE (3rd) | iOS 17 | Safari | 375×667 | smallest supported |
| Pixel 8 | Android 14 | Chrome | 412×915 | |
| Samsung S23 | Android 14 | Samsung Internet | 360×780 | |
| iPad Air | iPadOS 17 | Safari | 820×1180 | tablet breakpoint |
| MacBook 14" | macOS 14 | Safari, Chrome, Firefox | 1440×900 | baseline |
| Windows desktop | Win 11 | Edge, Chrome, Firefox | 1920×1080 | |

## Tier 2 — best-effort (quarterly sweep)
Galaxy Fold (open/closed), Surface Duo, iPad Mini, low-end Android (Moto G), 4K desktop.

## Acceptance per device
- LCP ≤ 2s (Tier 1 only)
- No layout shift on font load (CLS = 0)
- All CTAs tappable (≥44×44pt)
- Carousel swipe works (`48-carousel-interaction.md`)
- No console errors

## Tooling
BrowserStack live + auto-run on Tier 1; manual exploratory on Tier 2.
