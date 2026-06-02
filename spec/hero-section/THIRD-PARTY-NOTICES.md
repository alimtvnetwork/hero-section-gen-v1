# Third-Party Notices — Hero

**Owner:** Legal · **Closes:** #104

## Generation
Run `bun licenses --prod --json > THIRD-PARTY-NOTICES.json` in CI; render Markdown at `/legal/notices`.

## Allowed licenses (auto-pass)
MIT, ISC, BSD-2-Clause, BSD-3-Clause, Apache-2.0, 0BSD, CC0-1.0, Unlicense.

## Review-required
MPL-2.0, LGPL-* (must dynamically link), CC-BY-* (attribution required in `/legal/notices`).

## Forbidden
GPL-*, AGPL-*, SSPL, Commons Clause, "BUSL", "no commercial" terms. CI fails build.

## Asset attribution
- Hero portrait (`portrait-v3.jpg`): owned, no attribution required.
- Fonts (General Sans, Inter): self-hosted under their respective OFL/SIL — attribution in `/legal/notices`.

## Update cadence
Regenerated on every `bun install` via lifecycle hook.
