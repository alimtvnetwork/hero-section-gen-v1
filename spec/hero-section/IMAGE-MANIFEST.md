# Portrait Image Manifest

**Authority:** Every portrait shipped in the hero is listed here with provenance, prompt, seed, license, and version. No image is added to the CDN without an entry.

## Entry schema

```yaml
- id: eng-001-amber
  engineer_id: eng-001
  version: v2                       # bump on any re-export
  source: imagegen                  # imagegen | photo | stock
  prompt: >                         # only for source=imagegen
    Cinematic portrait of a software engineer, amber rim light from camera-left,
    dark studio background, 85mm, shallow depth, half-body, looking slightly off-camera.
  seed: 482910                      # imagegen only; required for reproducibility
  model: imagegen.fast              # exact model id used
  license: internal-generated       # or stock id, or photographer + release ref
  release_on_file: contracts/eng-001-portrait-release.pdf  # required if source=photo
  alt: "Aisha Rahman, Chief Software Engineer, smiling under amber studio light"
  focal_point: { x: 0.52, y: 0.38 }
  exports:
    - { format: avif, density: 2x, path: cdn://hero/portraits/eng-001-amber-v2@2x.avif }
    - { format: webp, density: 2x, path: cdn://hero/portraits/eng-001-amber-v2@2x.webp }
    - { format: jpeg, density: 1x, path: cdn://hero/portraits/eng-001-amber-v2@1x.jpg } # fallback
  retention: keep-last-3            # CDN policy; older versions purged
  added: 2026-06-02
  added_by: design@example.com
```

## Rules

1. **Versioned filenames only** (`-vN`). Latest is **not** symlinked at the CDN; the manifest decides which version the app loads.
2. **AVIF first**, WebP second, JPEG emergency. AVIF quality 50, WebP 75, JPEG 82. Re-encode = new version.
3. **Real customer names forbidden** in Storybook fixtures (see `STORYBOOK-FIXTURES.md`). Manifest entries use real names only for production-bound assets.
4. **License audit** runs in CI: every entry must resolve `license` to an allowed value or have `release_on_file` pointing to a committed PDF.
5. **404 fallback chain:** AVIF → WebP → JPEG → static gradient placeholder (`--color-bg-elev`). Fire `hero_portrait_error` with `version`, `format`.

## Initial manifest

`manifest.yaml` lives at `spec/hero-section/portraits/manifest.yaml` (created in Batch 3 step 21 alongside canonical snapshots).