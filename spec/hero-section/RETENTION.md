# Data Retention Policy

**Authority:** TTLs for every data class the hero produces. Anything not listed = forbidden to persist.

## Table

| Data class | Storage | TTL | Deletion mechanism | Owner |
|---|---|---|---|---|
| Analytics events (`hero_*`) | analytics warehouse | 25 months | automated job (per GDPR Art. 5(1)(e)) | @data |
| Raw server access logs | log store | 90 days | log retention policy | @platform-sre |
| Error reports (Sentry-style) | reporter | 90 days | reporter retention | @platform |
| Source maps | reporter | 90 days | reporter retention | @platform |
| CDN portrait versions | CDN | keep last 3 versions per engineer | nightly purge (per `CDN.md`) | @platform-cdn |
| CMS draft entries | CMS | 90 days after publish | CMS lifecycle job | @content |
| CMS version history | CMS | last 20 versions | CMS lifecycle job | @content |
| Calendly booking events (webhook payloads) | server log | 30 days | log retention | @growth |
| Lighthouse CI reports | LH server | 365 days | LH retention | @perf |
| Preview-mode signed tokens | none | 15 minutes (TTL on token) | not stored | @content |
| Consent records (geo + decision) | consent store | 13 months | rolling delete | @privacy |
| Storybook fixtures (synthetic) | git | forever | n/a | @hero-owner |

## Forbidden

- Storing IP address, full user agent, or referrer alongside analytics events beyond 30 days (PII-adjacent).
- Retaining UTMs in raw access logs past 90 days.
- Keeping deleted-engineer portraits past 30 days.
- Logging Calendly invitee email/name to error reporter (scrubber required).

## Subject-access requests (SAR)

- Endpoint: `mailto:privacy@example.com` (no automated portal in MVP).
- Response SLA: 30 days.
- Hero produces no first-party account data; SARs typically resolve as "no records held."

## Auditing

- Quarterly: data-retention job runs report listing oldest record per class; alarms on anything past TTL.
- Annually: privacy review with @legal.

## Acceptance

- Each row above has a configured retention rule in the source system.
- Privacy review sign-off (`@privacy`) recorded in CHANGELOG on each MAJOR spec bump.