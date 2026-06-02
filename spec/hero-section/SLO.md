# SLOs + Alerting — Hero

**Authority:** Owns runtime targets and pager routing. SLO breach = page; SLO trend = ticket.

## Service-level objectives (28-day rolling window)

| SLO | Target | Hard breach | Page? |
|---|---|---|---|
| Hero `/` availability | 99.95% | <99.9% over 1h | yes |
| Hero TTFB p75 | ≤ 600 ms | >900 ms over 30m | yes |
| Hero LCP p75 (RUM) | ≤ 2.0 s | >2.5 s over 1h | yes |
| Hero CLS p75 | 0.00 | >0.05 over 1h | yes |
| Hero INP p75 | ≤ 150 ms | >250 ms over 1h | no (ticket) |
| `hero_cta_click` → Calendly load | ≤ 1.5 s p75 | >3 s | no (ticket) |
| 5xx rate (route `/`) | ≤ 0.10% | >1% over 5m | yes |
| Hydration mismatch rate | ≤ 1 per 10k views | >5 per 1k views | yes |
| `hero_portrait_error` rate | ≤ 0.20% | >1% over 15m | yes (CDN) |
| Carousel auto-advance failure rate | ≤ 0.05% | >0.5% | no (ticket) |

## Error budget

- Availability: 99.95% → 21m 36s per 30 days.
- LCP/CLS/INP budgets fold into the **Performance score** SLO (Lighthouse RUM aggregate ≥ 95).
- Burning >50% of monthly budget in <14 days → freeze new hero features until recovery plan.

## Alert routing

| Signal | Channel | Pager (after-hours) |
|---|---|---|
| Availability / 5xx | `#hero-incidents` | @platform-sre |
| TTFB / LCP / CLS | `#hero-perf` | @perf |
| Hydration / a11y | `#hero-incidents` | @hero-owner |
| CDN / portrait | `#platform-cdn` | @platform-cdn |
| CMS rollback >2/week | `#hero` | (no page) |
| Calendly outage | `#hero` | @growth |

## Synthetic monitoring

- Playwright check from 3 regions every 60 s: load `/`, assert H1 text, assert LCP element, assert no console error.
- Failure 2 consecutive runs → page.

## Postmortem trigger

- Any "page" SLO breach > 15 minutes → blameless postmortem within 5 business days using `POSTMORTEM-TEMPLATE.md`.

## Acceptance

- All SLOs defined in monitoring tool (Datadog/Grafana) and linked from this file.
- On-call rotation exists with at least 2 people per role.
- Runbook (`RUNBOOK.md` — future) referenced from each alert.