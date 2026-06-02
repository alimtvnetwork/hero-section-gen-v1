# Secrets Inventory + Rotation Policy

**Authority:** Every secret used by the hero stack listed here, with owner, rotation cadence, and break-glass procedure.

## Inventory

| Secret | Scope | Used by | Owner | Rotation | Last rotated |
|---|---|---|---|---|---|
| `CMS_READ_TOKEN` | server | TanStack loader / serverFn | `@content` | 90 d | TBD |
| `CMS_PREVIEW_TOKEN` | server | preview-mode route | `@content` | 90 d | TBD |
| `CALENDLY_API_KEY` | server | webhook signature verify | `@growth` | 90 d | TBD |
| `CALENDLY_WEBHOOK_SECRET` | server | `/api/public/webhook/calendly` | `@growth` | 180 d | TBD |
| `CDN_SIGN_KEY` | server | preview portrait signed URLs | `@platform-cdn` | 90 d | TBD |
| `ANALYTICS_WRITE_KEY` | server | server-side event forwarding | `@data` | 180 d | TBD |
| `ERROR_REPORTER_DSN` | server | Sentry-style reporter | `@platform` | 365 d | TBD |
| `LIGHTHOUSE_CI_TOKEN` | CI | LH server uploads | `@perf` | 365 d | TBD |
| `VITE_CMS_URL` | client (public) | runtime CMS endpoint | `@content` | n/a (not secret) | n/a |
| `VITE_CALENDLY_USER` | client (public) | embed config | `@growth` | n/a | n/a |

## Storage

Production secrets live in the platform secret store (per `secrets--add_secret` tool). **Never** committed. `.env.example` lists every secret name + comment, no values.

## Rotation procedure

1. Open issue from `ROTATION-TEMPLATE.md` 14 d before due.
2. Generate new value via vendor console.
3. `secrets--update_secret` with new value; old value retained as `*_PREV` for 24 h.
4. Deploy; verify health checks.
5. Revoke old value at vendor.
6. Close issue; update `Last rotated`.

## Break-glass (compromise suspected)

1. Revoke at vendor immediately.
2. Rotate per above, skipping 14-day window.
3. Audit logs for the secret's usage in last 30 d.
4. Postmortem (`POSTMORTEM-TEMPLATE.md`) within 48 h.
5. If user data exposure suspected → privacy lead + legal.

## Acceptance

- Inventory matches platform secret store (CI check).
- No secret older than 365 d active (CI warns at 30 d before due; fails at due).
- `git grep -nE '(sk_|whsec_|pk_live_|AKIA)'` returns nothing.