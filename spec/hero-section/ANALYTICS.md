# Analytics Contract — Hero

**Owner:** Data · **Closes:** #78, #90, plus PII review (#18 family)

## Event schema (versioned)
Every event payload includes `v: "1.0.0"` (semver). Bump **minor** for additive fields, **major** for breaking changes. Consumers MUST pin a major.

```ts
type HeroEvent = {
  v: "1.0.0";
  event: "hero_view" | "hero_cta_click" | "hero_slide_change" | "hero_calendly_open";
  ts: string;            // ISO-8601 UTC
  session_id: string;    // opaque, rotated per session
  variant?: string;      // A/B bucket — see FLAGS.md
  slide_index?: number;
  cta_id?: "primary" | "secondary";
  locale: string;        // BCP-47
};
```

## PII forbidden
Never send: email, name, IP, precise geo, user-typed strings, URL query params, `referrer` host beyond eTLD+1. Auto-redacted at SDK layer.

## Consent + replay queue (#78)
- Events enqueue to `sessionStorage` key `hero_evt_q` (cap 50, FIFO drop).
- On consent grant → flush queue → POST `/api/public/analytics`.
- On consent denial → queue cleared, no network.
- Queue TTL: 24h; expired entries dropped on read.

## Transport
- Endpoint: `/api/public/analytics` (see `public-api-endpoints` rules).
- Batch: max 20 events / 2s debounce.
- Failure: retry 3× exponential (1s, 4s, 16s) then drop.

## Sampling
`hero_view` 100% · `hero_slide_change` 25% · rest 100%.
