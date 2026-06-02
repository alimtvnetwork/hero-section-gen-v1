# Storybook Fixture Rules (PII-Safe)

**Authority:** All Storybook stories and test fixtures use **synthetic data only**. Real customer/engineer names, photos, or quotes are forbidden in `src/components/hero/__fixtures__/`.

## Synthetic name pool

Use one of these (or generate new via `faker.person.fullName({ sex: 'female' })`):

- Aisha Rahman, Priya Sharma, Mei Chen, Sofia García, Yara Haddad,
- Kenji Tanaka, Lucas Müller, Diego Alvarez, Idris Okafor, Noa Cohen.

Roles must be generic engineering titles (`Senior Backend Engineer`, `Mobile Lead`, `Staff ML Engineer`). No company-specific titles.

## Synthetic portraits

Generated via `imagegen` per `08-image-manifest.md`. Each fixture entry stores `seed` for reproducibility. **Never** use scraped social-media photos, stock-photo previews with watermarks, or real engineer headshots from `production-manifest.yaml`.

## Synthetic quotes

Drafted from a fixed pool (`__fixtures__/quotes.json`). Reviewed by copy owner (`38-copy-owners.md`) once; pool size: 12; rotation in stories deterministic via `fixtureSeed`.

## Lint rules

- `eslint-plugin-no-restricted-syntax`: forbid string literals matching real customer-domain emails (`@acme.com`, etc.) in `__fixtures__/`.
- CI grep: fail PR if `__fixtures__/` contains any URL pointing to `production-manifest.yaml` portrait paths.
- Checklist item in PR template: "Fixtures contain no real names/photos/quotes."

## Privacy review

New fixture additions require a 30-second eyeball check by `@privacy-reviewer` (37-codeowners.md rule). Synthetic names from the pool above are auto-approved.

## Why

Storybook is published with internal docs. A leaked story containing a real customer name = a notifiable disclosure event. Synthetic data eliminates the class.