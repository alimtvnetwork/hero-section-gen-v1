# Lighthouse CI Configuration — Hero

**Owner:** Performance · **Closes:** #57 (graduates 27-budgets.md from 🟡 → ✅)

## Config file: `lighthouserc.json`
```json
{
  "ci": {
    "collect": {
      "url": ["https://preview/hero", "https://preview/"],
      "numberOfRuns": 5,
      "settings": { "preset": "desktop", "throttlingMethod": "simulate" }
    },
    "assert": {
      "assertions": {
        "categories:performance": ["error", {"minScore": 0.95}],
        "categories:accessibility": ["error", {"minScore": 1.0}],
        "categories:best-practices": ["error", {"minScore": 0.95}],
        "categories:seo": ["error", {"minScore": 1.0}],
        "largest-contentful-paint": ["error", {"maxNumericValue": 2000}],
        "cumulative-layout-shift": ["error", {"maxNumericValue": 0}],
        "total-blocking-time": ["error", {"maxNumericValue": 150}],
        "first-contentful-paint": ["error", {"maxNumericValue": 1200}]
      }
    },
    "upload": { "target": "temporary-public-storage" }
  }
}
```

## Mobile profile
Second run with `"preset": "perf"` and `"formFactor": "mobile"`; LCP gate relaxed to ≤ 2500ms (Moto G4 baseline).

## CI job
`lhci autorun` in workflow (see `47-ci.md`). Median of 5 runs used for assertions. Reports retained 30d.

## Failure → action
Block merge. PR comment posts diff vs `main` baseline. Owner triages within 1 business day; regression budget in `27-budgets.md`.
