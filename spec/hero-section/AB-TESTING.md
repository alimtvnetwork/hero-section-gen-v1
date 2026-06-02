# A/B Testing Guardrails — Hero

**Owner:** Growth · **Closes:** #88 (graduates from FLAGS.md stub)

## Allowed scope
Copy variants, CTA label, hero portrait, slide order. **Forbidden:** changes that violate `TOKENS.md`, `A11Y.md`, `BUDGETS.md`, or `COPY-OWNERS.md` (legal-reviewed claims).

## Sample size & duration
- Min sample: **5000 sessions per arm** before reading.
- Min duration: **7 days** (covers weekly cycle).
- Stop early only on: SRM detected, p<0.001 harm on guardrail metric, or legal/PR escalation.

## Guardrail metrics (auto-monitored)
| Metric | Threshold | Action |
|---|---|---|
| LCP p75 | regression > 200ms | auto-pause |
| JS error rate | +0.5% absolute | auto-pause |
| Bounce rate | +5% relative | manual review |
| Calendly conversion | -10% relative | manual review |

## Concurrency
Max **1 hero experiment** in flight. Stacking requires Growth-lead approval (see `CODEOWNERS`).

## Bucketing
Deterministic hash of `session_id` (see `ANALYTICS.md`). Stable across sessions for opted-in users.

## Documentation
Each experiment: spec doc in `experiments/YYYY-MM-DD-slug.md`, linked from `FLAGS.md`. Postmortem on ship/kill.
