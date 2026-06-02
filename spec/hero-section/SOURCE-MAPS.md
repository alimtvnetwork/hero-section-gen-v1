# Source-Map Upload Policy

**Authority:** Production source maps are uploaded to the error reporter and **never** served to browsers. This makes errors readable without leaking source.

## Rules

1. Vite build emits source maps (`build.sourcemap: 'hidden'`).
2. `hidden` mode generates `.map` files but **omits** `//# sourceMappingURL=` comments → browsers can't fetch them.
3. CI step uploads `.map` files to the error reporter (Sentry-compatible API) with the release version.
4. CI step then **deletes** `.map` files from the deploy artifact before publish.
5. Reporter API token: `ERROR_REPORTER_DSN` + `SOURCEMAP_UPLOAD_TOKEN` (Batch 4 §SECRETS).

## `vite.config.ts` excerpt

```ts
export default defineConfig({
  build: {
    sourcemap: "hidden",
  },
})
```

## CI step

```yaml
- name: Upload source maps
  run: |
    npx @sentry/cli releases files "$RELEASE" upload-sourcemaps dist/ --no-rewrite
    find dist -name "*.map" -delete
  env:
    SENTRY_AUTH_TOKEN: ${{ secrets.SOURCEMAP_UPLOAD_TOKEN }}
```

## Retention

- Maps retained 90 d in reporter (matches `RETENTION.md`, Batch 5).
- After 90 d, errors still reported but stack frames are minified.

## Forbidden

- `sourcemap: true` in production build (would publish maps).
- Public bucket hosting of `.map` files.
- Inline source maps in production bundles.

## Acceptance

- `curl -I https://prod/assets/index-*.js.map` returns 404.
- New deploy reflects in reporter UI within 5 min with readable stack frames.