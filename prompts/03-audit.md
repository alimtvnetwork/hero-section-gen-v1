# 03 — Audit / Gap Analysis

**Goal:** Compare the spec against the canonical image AND against
production-grade engineering standards, then score it.

## Inputs

- `00-<section>-canonical.<ext>`
- Full spec folder
- `.lovable/coding-guidelines.md` and `.lovable/strictly-avoid.md`

## Process

1. **Visual audit** — for each row in `05-<section>-anatomy.md`, confirm the
   spec text matches the pixel reality. Log each delta in
   `20-gap-analysis.md` with severity.
2. **Token audit** — every value in `02-tokens.md` exists in the prototype
   and the prototype contains no values that aren't tokenized.
3. **Contract audit** — `04-component-contract.md` covers every visible
   element; every prop has a default, validator, and length budget.
4. **A11y audit** — AAA contrast for body, ≥AA for large text; focus order
   matches DOM order; reduced-motion path defined.
5. **Perf audit** — LCP element identified; image manifest has preload
   strategy; budgets file lists JS / CSS / image / font caps.
6. **Ops audit** — CI matrix, lighthouse-ci config, analytics schema,
   secrets inventory, CSP, supply-chain pin all present.
7. **Score** — out of 100, weighted:
   - Visual / layout / copy: 20
   - Tokens / typography / color: 15
   - Component contract / states / edge cases: 20
   - A11y / SEO: 15
   - Perf / runtime / supply chain: 15
   - Governance / CI / analytics / ops: 15
8. Write the score + per-category breakdown into `20-gap-analysis.md` and
   bump `21-changelog.md`.

## P0 / P1 / P2 definitions

- **P0** — blocks ship. Wrong copy, wrong token, missing a11y path, security
  hole, broken contract.
- **P1** — degrades quality measurably. Sub-spec on a secondary element,
  missing edge case, missing analytics event.
- **P2** — polish. Nice-to-have copy, minor motion delight, discovery items.

All findings land in `45-priorities.md` with status `todo` / `in-progress` /
`done` and an estimated batch number.

## Deliverables

- `20-gap-analysis.md` updated with the full scored audit.
- `45-priorities.md` updated with every P0/P1/P2 finding.
- `21-changelog.md` bumped (e.g. `vX.Y.Z` with audit summary).
- Hand off to `04-next-10.md` once the user says "next 10".