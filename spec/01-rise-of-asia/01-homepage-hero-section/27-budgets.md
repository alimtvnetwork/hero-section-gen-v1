# Performance Budgets + Lighthouse CI

**Authority:** PR-blocking gates. Budgets enforced by Lighthouse CI on every PR; failing rows = red check.

## Lighthouse targets (mobile, throttled Moto G4 / Slow 4G)

| Metric | Target | Hard fail |
|---|---|---|
| Performance score | ≥ 95 | < 90 |
| LCP | ≤ 2.0 s | > 2.5 s |
| CLS | 0.00 | > 0.05 |
| INP | ≤ 150 ms | > 200 ms |
| TBT | ≤ 200 ms | > 350 ms |
| Accessibility score | 100 | < 100 |
| Best Practices | ≥ 95 | < 90 |
| SEO | 100 | < 100 |

## Bundle budgets (gzipped, route `/`)

| Bucket | Budget | Hard fail |
|---|---|---|
| Initial JS (route) | ≤ 80 KB | > 100 KB |
| Initial CSS | ≤ 20 KB | > 30 KB |
| Above-fold images (LCP portrait, AVIF) | ≤ 90 KB | > 130 KB |
| Fonts (preloaded WOFF2 combined) | ≤ 80 KB | > 110 KB |
| Three.js chunk (lazy) | ≤ 60 KB | > 90 KB |
| Third-party JS (Calendly stub, analytics) | ≤ 20 KB | > 40 KB |
| **Total initial transfer** | ≤ 280 KB | > 350 KB |

## `.lighthouserc.json` (committed)

```json
{
  "ci": {
    "collect": { "url": ["http://localhost:3000/"], "numberOfRuns": 3 },
    "assert": {
      "assertions": {
        "categories:performance":   ["error", { "minScore": 0.95 }],
        "categories:accessibility": ["error", { "minScore": 1.0  }],
        "categories:seo":           ["error", { "minScore": 1.0  }],
        "largest-contentful-paint": ["error", { "maxNumericValue": 2000 }],
        "cumulative-layout-shift":  ["error", { "maxNumericValue": 0.05 }],
        "total-blocking-time":      ["error", { "maxNumericValue": 200  }],
        "resource-summary:script:size":     ["error", { "maxNumericValue": 102400 }],
        "resource-summary:stylesheet:size": ["error", { "maxNumericValue": 30720  }],
        "resource-summary:image:size":      ["error", { "maxNumericValue": 133120 }],
        "resource-summary:font:size":       ["error", { "maxNumericValue": 112640 }]
      }
    },
    "upload": { "target": "temporary-public-storage" }
  }
}
```

## PR comment template

```
### Lighthouse — hero (`/`)
| Metric | Before | After | Δ | Budget | Status |
|---|---|---|---|---|---|
| LCP   | 1.85s | 1.91s | +60ms | ≤2.0s | ✅ |
| CLS   | 0.00  | 0.00  | 0     | 0.00  | ✅ |
| JS gz | 72KB  | 78KB  | +6KB  | ≤80KB | ✅ |

Owner: @hero-perf · Waiver expiry: n/a
```

Auto-posted by `lighthouse-ci-action`. Comment updates in place on push.

## Waivers

Any failing budget requires a `BUDGET-WAIVER.md` entry: reason, owner, expiry (≤14 days). CI passes only while the waiver is active.