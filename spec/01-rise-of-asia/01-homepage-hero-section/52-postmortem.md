# Postmortem Template — Hero Incidents

**Owner:** SRE · **Closes:** #105 (graduates from 43-slo.md stub)

## When to write
Any incident where: hero LCP > 4s for >5 min, error rate > 1% for >5 min, full outage > 1 min, or customer-reported regression.

## Template (copy into `incidents/YYYY-MM-DD-slug.md`)

```md
# Incident: <title>
**Date:** YYYY-MM-DD · **Duration:** Xm · **Severity:** SEV-{1,2,3}
**Author:** @handle · **Reviewers:** @lead1 @lead2

## Summary
2–3 sentences. What broke, who saw it, how long.

## Impact
- Users affected: N (% of sessions)
- Revenue impact: $X (if measurable)
- SLO burn: X% of monthly budget

## Timeline (UTC)
- HH:MM — event
- HH:MM — detection
- HH:MM — mitigation
- HH:MM — resolved

## Root cause
Five-whys. Code/config/process.

## What went well
## What went poorly
## Action items
| Owner | Action | Due | Ticket |
|---|---|---|---|
```

## Blameless rule
Critique systems and decisions, never people. Names appear only in **author/reviewer** and **action-item owner** fields.

## Publication
Internal within 5 business days. Public summary (sanitized) within 10 business days if customer-visible.
