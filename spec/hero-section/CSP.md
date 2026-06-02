# Content Security Policy — Hero (Production)

**Authority:** No `unsafe-inline`. JSON-LD scripts use a per-request **nonce**. Any inline JS or style violates the policy.

## Policy header (production)

```
Content-Security-Policy:
  default-src 'self';
  script-src  'self' 'nonce-{NONCE}' https://assets.calendly.com;
  style-src   'self' 'nonce-{NONCE}';
  img-src     'self' data: https://{cdn-host} https://{calendly-img-host};
  font-src    'self';
  connect-src 'self' https://{cms-host} https://api.calendly.com https://{analytics-host};
  frame-src   https://calendly.com https://*.calendly.com;
  base-uri    'self';
  form-action 'self';
  object-src  'none';
  upgrade-insecure-requests
```

`{NONCE}` is a 128-bit base64 value generated per request in TanStack `__root.tsx`. Reuse across the document; never log it.

## Inline JSON-LD with nonce

```tsx
// src/routes/__root.tsx (excerpt)
const nonce = useNonce()  // server-generated per request, passed via context

head: () => ({
  scripts: [{
    type: "application/ld+json",
    nonce,
    children: JSON.stringify(orgSchema),
  }],
})
```

## Forbidden

- `<script>{`window.x = …`}</script>` without a nonce.
- `dangerouslySetInnerHTML` for anything other than the nonced JSON-LD block.
- Inline `style="…"` props (use Tailwind / `src/styles.css`).
- Third-party JS pulled by Calendly: pinned `script-src` allows only the documented host.

## CSP report-only first

Roll out as `Content-Security-Policy-Report-Only` with a report-to endpoint for **7 days**. Promote to enforce only when zero violation reports for 72 h.

## Acceptance

- Enforce header set in `_headers` / TanStack server response.
- Production smoke test: 0 console CSP violations on `/`.
- Lighthouse Best-Practices ≥ 95 (CSP audit passes).