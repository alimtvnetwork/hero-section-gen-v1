# Copy Length Budgets (i18n-safe)

**Authority:** Translators and CMS validators enforce these limits. Strings outside the budget = PR block.

## Per-field budgets

| Field | Min | Max (EN) | Max (DE +35%) | Max (AR) | Lines @ 1440 | Lines @ 375 |
|---|---|---|---|---|---|---|
| `eyebrow` | 6 | 28 | 38 | 28 | 1 | 1 |
| `headline.text` | 18 | 56 | 76 | 56 | 2 | 3 |
| `headline.accentWords[]` | 1 | 3 words | 3 | 3 | — | — |
| `lede` | 60 | 140 | 190 | 140 | 2 | 4 |
| `body` | 0 | 220 | 297 | 220 | 3 | 6 |
| `primaryCta.label` | 4 | 22 | 30 | 22 | 1 | 1 |
| `secondaryCta.label` | 4 | 22 | 30 | 22 | 1 | 1 |
| `engineer.role` | 4 | 36 | 49 | 36 | 1 | 1 |
| `engineer.subRole` | 0 | 40 | 54 | 40 | 1 | 1 |
| `engineer.quote` | 80 | 160 | 216 | 160 | 3 | 5 |
| `engineer.badge` | 8 | 28 | 38 | 28 | 1 | 1 |
| `stat.value` | 1 | 6 | 6 | 6 | 1 | 1 |
| `stat.label` | 4 | 24 | 32 | 24 | 1 | 1 |

## Overflow rules

- **Lines @ viewport** capped via `line-clamp-N`. Truncation must end on word boundary + `…`.
- **Never** truncate `headline.text` or `primaryCta.label`. If they overflow, the spec is wrong — fix the copy or bump the budget.
- **Arabic:** line-height `1.7` (vs `1.4` LTR); reserve +10% vertical space on `lede`/`quote`.
- **German:** allow `hyphens: auto` with `lang="de"` for `lede` and `body`.
- **CJK:** `word-break: keep-all` on `headline.text`.

## CI gate

`bun run check:copy-budgets` validates CMS payloads at build. Failing keys block deploy. Locale fallback (`de-AT → de-DE → en`) runs **before** budget check.

## Snapshots required

Canonical screenshots at 1440 / 768 / 375 for **en, de, ar** — committed to `snapshots/copy-budgets/`. PR diff comments any pixel delta > 2%.