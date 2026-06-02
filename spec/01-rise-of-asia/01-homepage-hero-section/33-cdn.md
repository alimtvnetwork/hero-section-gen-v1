# CDN Contract — Hero Assets

**Authority:** Path structure, headers, purge workflow, signed URLs, fallback chain. Aligns with `08-image-manifest.md`.

## Path structure

```
cdn://hero/
  portraits/{engineer_id}-{variant}-{vN}@{density}.{format}
  fallback/portrait-default-v1@2x.jpg
  fonts/{family}-{weight}.woff2
  og/{route}-{vN}.jpg
```

Examples:
- `cdn://hero/portraits/eng-001-amber-v2@2x.avif`
- `cdn://hero/fallback/portrait-default-v1@2x.jpg`

## Headers (set at edge)

| Asset | `Cache-Control` | `Content-Type` | Extras |
|---|---|---|---|
| `portraits/**` | `public, max-age=31536000, immutable` | per format | `Vary: Accept`, `Timing-Allow-Origin: *` |
| `fallback/**`  | `public, max-age=31536000, immutable` | `image/jpeg` | — |
| `fonts/**`     | `public, max-age=31536000, immutable` | `font/woff2` | `Access-Control-Allow-Origin: *` |
| `og/**`        | `public, max-age=2592000` | `image/jpeg` | — |

Immutability is safe because every path is versioned (`-vN`).

## Ownership

- **Owner team:** `@platform-cdn`.
- **Registrar / DNS:** see `DNS.md` (Batch 5 future).
- **Purge workflow:** automated via CI on manifest version bump; manual purge requires PR + `@platform-cdn` approval.

## Signed URLs

Only required for **preview-mode** portrait drafts (not yet on canonical manifest). TTL 15 min, signed with `CDN_SIGN_KEY`. Production portraits are public.

## Fallback chain (mirrors `08-image-manifest.md` §5)

1. Requested AVIF.
2. WebP (browser `Accept` negotiation OR explicit `<source>` swap).
3. JPEG emergency (always reachable).
4. `cdn://hero/fallback/portrait-default-v1@2x.jpg` (generic amber-lit silhouette).
5. Static CSS gradient (no CDN call, baked in CSS).

Each step that fires beyond #1 emits `hero_portrait_fallback` with `step`, `engineer_id`, `format`.

## Outage mode

- CDN 5xx → browser cache continues serving last `immutable` asset.
- New visitor during outage → fallback chain runs to step 4 (also CDN) → step 5 (CSS gradient, always works).
- Status banner via `hero_dep_outage { dep: "cdn" }`.

## Acceptance

- Every portrait path returns 200 with correct headers (CI smoke).
- Fallback chain unit-tested via mocked 404/timeout.
- `Cache-Control: immutable` verified in browser devtools.