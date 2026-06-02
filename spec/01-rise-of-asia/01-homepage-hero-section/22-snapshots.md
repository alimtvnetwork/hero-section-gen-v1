# Canonical Snapshots — Visual Regression Source of Truth

**Authority:** Every PR touching `<Hero />` runs Playwright visual diffs against this set. Pixel delta > 0.5% on any baseline = PR block.

## Baseline matrix (required to merge any visual PR)

| ID | Viewport | DPR | Color scheme | Locale | Motion | State |
|---|---|---|---|---|---|---|
| S-1440-en  | 1440×900 | 2 | dark   | en | full   | default |
| S-1024-en  | 1024×768 | 2 | dark   | en | full   | default |
| S-768-en   | 768×1024 | 2 | dark   | en | full   | default |
| S-375-en   | 375×812  | 3 | dark   | en | full   | default |
| S-1440-de  | 1440×900 | 2 | dark   | de | full   | default (+35% copy) |
| S-1440-ar  | 1440×900 | 2 | dark   | ar | full   | RTL mirror |
| S-1440-rm  | 1440×900 | 2 | dark   | en | reduce | no-particles, cross-fade |
| S-1440-hc  | 1440×900 | 2 | forced | en | full   | `forced-colors: active` |
| S-1440-nojs| 1440×900 | 2 | dark   | en | n/a    | `<noscript>` baseline |
| S-1440-print| 1440×900| 2 | n/a    | en | n/a    | `@media print` A4 + Letter |

Plus state snapshots per `10-states.md`: `HeroLoading`, `HeroEmpty`, `HeroError`, `HeroOffline` at 1440 + 375.

Plus fixture snapshots per `09-edge-cases.md`: EC-01..EC-20 at 1440 + 375 minimum.

## File layout

```
snapshots/hero/
  baseline/     # canonical PNGs, committed
  diffs/        # CI-generated; never committed
  metadata.json # per-image: hash, generator, generated_at, owner
```

## Generation procedure

1. `bun run snap:baseline` boots a sandboxed Playwright runner, deterministic time (`2026-01-01T00:00:00Z`), `prefers-reduced-motion: no-preference` unless ID says otherwise.
2. Disables `animation-duration` shimmer where state is non-animated.
3. Output written to `snapshots/hero/baseline/<id>.png`.
4. Each baseline regen requires a `@design` 37-codeowners.md approval (per `37-codeowners.md`, Batch 5).

## Tolerance

- Default: `maxDiffPixelRatio: 0.005`.
- Text-heavy snapshots (DE, AR): `0.01` (font metrics).
- Print snapshots: `0.02` (browser print rasterizer variance).

## When baselines must regen

- New canonical reference image (`00-hero-canonical-vN.png`).
- Token change that affects visible color/spacing.
- Copy change beyond ±5% of length budget.

Stale baselines are P0 — block all PRs until refreshed.