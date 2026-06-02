# Copy Source Ownership

**Authority:** Every user-facing string in the hero has a named owner and a change-approval path. Edits without the listed approval = PR block.

## String map

| CMS key | Default (en) | Owner | Approval needed | Legal-review trigger |
|---|---|---|---|---|
| `hero.eyebrow` | `For ambitious startups` | @copy | @copy | — |
| `hero.headline.text` | `Build with the Top 5% of engineering talent — without the overhead.` | @copy + @brand | @copy + @brand | keywords: "guaranteed", "best", "fastest", "#1" |
| `hero.headline.accentWords` | `["Top 5%","engineering talent"]` | @copy + @brand | @copy + @brand | — |
| `hero.lede` | `We embed senior, vetted engineers into your team in days, not months.` | @copy | @copy | — |
| `hero.body` | (optional supporting paragraph) | @copy | @copy | — |
| `hero.primaryCta.label` | `Book an intro call` | @growth | @growth | — |
| `hero.secondaryCta.label` | `See how it works` | @growth | @growth | — |
| `hero.engineer.badge` | `Top 5% Vetted Engineer` | @brand + @legal | @brand + @legal | always (positioning claim) |
| `hero.stat[*].label` | `Avg. ramp time`, `Vetted engineers`, `Time-zone overlap` | @growth | @growth | numbers/claims |
| `hero.stat[*].value` | `7 days`, `1,200+`, `4–6h` | @growth + @data | @data | numbers must be source-linkable |
| `hero.empty.cta` | `Meet our team →` | @copy | @copy | — |
| `hero.error.title` | `We're recalibrating.` | @copy | @copy | — |
| `hero.error.body` | (see 10-states.md canonical) | @copy | @copy | — |
| `hero.offline.banner` | `You're offline — showing the last version we loaded.` | @copy | @copy | — |

## Change-approval flow

1. Open PR editing CMS JSON or fixture string.
2. 37-codeowners.md auto-requests the listed owner(s).
3. If the **legal-review trigger** matches (keywords or always-rules), CI auto-adds `@legal` as reviewer and applies the `needs-legal` label.
4. Merge blocked until all required approvals + legal (if triggered).
5. After merge, copy lives in CMS — production rollout follows `42-cms-preview.md` rollback rules.

## Legal-review keywords (case-insensitive substring match)

`guaranteed`, `guarantee`, `best`, `fastest`, `#1`, `cheapest`, `award-winning`, `certified`, `top 1%`, `top 5%`, `100%`, `risk-free`, `proven`, `unmatched`, `industry-leading`.

## Forbidden

- AI-rewriting copy without an owner PR.
- Editing strings directly in component source (`<h1>Build...</h1>` literal) — all copy lives in CMS or `__fixtures__/`.
- Removing accent-words array entries without `@brand` sign-off (changes the visual emphasis).

## Audit

- Monthly: copy owners walk through CMS diff; close-out report to `#hero` channel.
- Quarterly: legal re-reviews any string carrying a comparative claim.