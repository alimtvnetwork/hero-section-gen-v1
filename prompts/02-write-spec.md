# 02 — Write the Canonical Spec

**Goal:** Build the full `spec/<site>/<section>/` folder so a blind AI can
implement the section without re-reading the image.

## Folder template (mirror `spec/01-rise-of-asia/01-homepage-hero-section/`)

```
spec/<NN-site>/<NN-section>/
├── 00-index.md                  ← entry point + reading order
├── 01-conventions.md            ← naming rules
├── 02-tokens.md                 ← single source of truth for styles.css
├── 03-glossary.md
├── 04-component-contract.md     ← public Props API
├── 05-<section>-anatomy.md
├── 06-copy-budgets.md
├── 07-i18n.md
├── 08-image-manifest.md
├── 09-edge-cases.md
├── 10-states.md                 ← loading/empty/error/offline/slow
├── 11-storybook-fixtures.md
├── 12-threejs-fallback.md       ← only if 3D used
├── 13-spec.md                   ← visual baseline
├── 14..18-spec-part-N.md        ← portrait / data / motion / ops / a11y
├── 19-archived.md
├── 20-gap-analysis.md
├── 21-changelog.md              ← semver
├── 22..26  visual/a11y/perf supplements
├── 27..36  perf / runtime / supply chain
├── 37..46  governance + final gate
└── 47..56  operational closure (CI, analytics, a11y regression, lighthouse-ci)
```

## Rules

1. **One concern per file.** Files exceeding ~300 lines get split.
2. **00-index.md is the only entry point.** Every other file is reached
   from there.
3. **Lowercase-hyphen filenames** with a sortable numeric prefix matching
   `00-index.md` reading order.
4. **Source of truth table** in `00-index.md` resolves all contradictions.
5. **Tokens are immutable** once a section ships v1.0.0; bump semver in
   `21-changelog.md` for any change.
6. **Status tags** on every section: `[MVP]`, `[v1.1]`, `[aspirational]`.

## Step-by-step

1. Copy the file skeleton above. Leave placeholders only where the user
   cannot have a real value yet — placeholders MUST contain `TODO(<owner>)`.
2. Fill `02-tokens.md` from the prototype's CSS variables verbatim.
   **MANDATORY:** every color row in 4 formats (oklch + hex + rgb + hsl),
   every shade/hover/glow as its own row, every header level in the
   typography table with explicit font-family. Declare
   `--shadow-text-default: rgb(0 0 0) 1px 0.7px 0px`. See
   `prompts/06-color-and-typography.md` for the full contract — this is
   non-negotiable; spec audits fail without it.
3. Fill `04-component-contract.md` with a TypeScript-style Props interface,
   each field's units, defaults, and validation rule.
4. Fill `05-<section>-anatomy.md` from the prototype's numbered table.
5. Fill `13-spec.md` as the visual baseline that a designer would read.
6. Fill `45-priorities.md` with P0 / P1 / P2 from `20-gap-analysis.md`.
7. Fill `46-definition-of-done.md` — the binary ship gate.

## Anti-patterns

- Paraphrasing the user's words. Capture verbatim quotes in
  `.lovable/memory/specs/`.
- Deleting outdated content. Move it to `19-archived.md`.
- Letting two files disagree silently — log to `20-gap-analysis.md` and
  pick a winner in the source-of-truth table.

## Deliverables

- All `00..21` files exist and pass cross-reference check
  (`grep -rIEn '[A-Z][A-Z-]+\.md' spec/<site>/<section>/` returns empty).
- `00-index.md` reading order matches actual file numbering.
- Hand off to `03-audit.md`.