# CMS Preview Mode + Rollback

**Authority:** How draft hero content is previewed, who can see it, how it gets reverted on production.

## Preview mode

- Entry: signed URL `https://preview.example.com/?preview=<token>` where `<token>` = HMAC of `(user_id, ttl, draft_id)` using `CMS_PREVIEW_TOKEN`.
- TTL: **15 minutes**.
- Auth: requires logged-in CMS editor (cookie + bearer to CMS).
- Behaviour:
  - Reads draft (unpublished) entries from CMS.
  - Bypasses CDN cache (`Cache-Control: no-store`).
  - Adds `X-Robots-Tag: noindex, nofollow` header.
  - Renders a fixed amber banner: `Preview — draft content, not visible to users.`
  - Suppresses analytics (`HERO_ANALYTICS_SAMPLE=0`).
- Flag: `HERO_PREVIEW_MODE` per `FLAGS.md`.

## Publishing

- Editor clicks "Publish" in CMS → CMS calls webhook `/api/public/webhook/cms-publish` (signature-verified per `Public API Endpoints` template).
- Webhook triggers a cache-tag purge at CDN (`hero:*`).
- Cache TTL for live content: `s-maxage=60` (stale-while-revalidate 300s).

## Version history

- CMS retains **last 20 versions** of every hero entry.
- Each version: id, author, timestamp, diff, approvals.
- Editors cannot delete history; only platform admins (CODEOWNERS `@platform`) can prune.

## Rollback procedure

1. Editor opens version history for the affected entry.
2. Clicks "Revert to version N" → CMS creates new version M = N + 1 with identical payload.
3. Same webhook fires → cache purge → live content reverts.
4. Audit log entry written: `hero.cms.rollback {from: M-1, to: N, by: <user>}`.
5. SLO event `cms_rollback` increments; if >2 rollbacks per week → trigger retro.

## Emergency kill-switch

- Set `HERO_SAFE_MODE=true` (see `FLAGS.md`) — serves SSR baseline only, ignores CMS payload entirely.
- Used when CMS is poisoned (legal-claim issue, defacement, etc.) and rollback is too slow.

## Acceptance

- Preview URL works for editors; expires after 15 min.
- `curl https://preview.example.com/` without token returns 401.
- Publish webhook → CDN purge → fresh content visible within 90 s.
- Rollback E2E test runs in CI weekly.