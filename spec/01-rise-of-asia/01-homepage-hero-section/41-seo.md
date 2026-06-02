# SEO — robots / sitemap / canonical / hreflang

**Authority:** Aligns with `26-head-meta.md`. Owns environment-conditional rules.

## robots.txt (per environment)

| Env | Content |
|---|---|
| dev | `User-agent: *` / `Disallow: /` |
| preview | `User-agent: *` / `Disallow: /` + header `X-Robots-Tag: noindex, nofollow` on all responses |
| staging | `User-agent: *` / `Disallow: /` + header `X-Robots-Tag: noindex, nofollow` |
| prod | `User-agent: *` / `Allow: /` / `Sitemap: https://example.com/sitemap.xml` |

Template lives at `public/robots.${ENV}.txt`; build copies the matching one to `public/robots.txt`.

## sitemap.xml

- Generated at build via TanStack route enumeration.
- One entry per public route (`/`, `/about`, `/services`, etc.). Locales emitted as `xhtml:link rel="alternate" hreflang="…"`.
- `lastmod` = git commit date of the route file.
- Served from `/sitemap.xml` (server route).
- Prod only; other envs return 404 to avoid leaking unreleased routes.

## Canonical

- Per leaf route only (see `26-head-meta.md` and TanStack canonical-dedupe caveat).
- **No trailing slash.** `/` is the home and is the sole exception (root must be `/`).
- **Apex domain** (`example.com`), not `www.`. `www.` 301-redirects to apex at the edge.
- **HTTPS only.** Plain HTTP 301-redirects.
- Hash fragments and tracking query params (`utm_*`, `gclid`, `fbclid`) stripped from canonical.

## hreflang

```html
<link rel="alternate" hreflang="en"    href="/" />
<link rel="alternate" hreflang="de"    href="/de/" />
<link rel="alternate" hreflang="ar"    href="/ar/" />
<link rel="alternate" hreflang="x-default" href="/" />
```

Emitted at every locale-aware leaf route. `x-default` = English.

## Open Graph image

- LCP portrait doubles as OG image (see `26-head-meta.md`).
- Dimensions 1200×630 minimum; JPEG; ≤ 200 KB.
- Cached at CDN `og/<route>-vN.jpg` per `33-cdn.md`.

## Acceptance

- `curl https://prod/robots.txt` returns the prod allowlist; preview returns disallow.
- `curl -I https://preview/...` returns `X-Robots-Tag: noindex`.
- Google Rich Results Test passes Organization + WebPage schema.
- `https://www.example.com/` 301s to `https://example.com/`.
- `https://example.com/?utm_source=x` canonicalizes to `https://example.com/`.