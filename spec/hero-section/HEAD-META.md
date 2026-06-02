# `__root.tsx` Head Meta — Hero Defaults

**Authority:** What goes in `__root.tsx` head vs leaf routes for hero rendering. Aligns with TanStack head-meta rules (see project knowledge).

## Root-level (sitewide)

```ts
head: () => ({
  meta: [
    { charSet: "utf-8" },
    { name: "viewport", content: "width=device-width, initial-scale=1, viewport-fit=cover" },
    { name: "color-scheme", content: "dark" },                  // closes finding #55
    { name: "theme-color",  content: "#0A0A0A" },
    { property: "og:type",      content: "website" },
    { property: "og:site_name", content: "Acme" },              // replace
    { name: "twitter:card",     content: "summary_large_image" },
  ],
  links: [
    // Fonts (per FONTS.md)
    { rel: "preload", as: "font", href: "/fonts/inter-var.woff2",       type: "font/woff2", crossOrigin: "anonymous" },
    { rel: "preload", as: "font", href: "/fonts/general-sans-700.woff2", type: "font/woff2", crossOrigin: "anonymous" },
    // Hero LCP portrait preload (first engineer only)
    { rel: "preload", as: "image", href: defaultPortraitWebp, fetchPriority: "high",
      imageSrcSet: defaultPortraitSrcSet, imageSizes: "(min-width:1024px) 640px, 100vw" },
  ],
  scripts: [{
    type: "application/ld+json",
    children: JSON.stringify({
      "@context": "https://schema.org",
      "@type": "Organization",
      name: "Acme",
      url: "https://example.com/",
    }),
  }],
})
```

**Never** in root: `title`, `description`, `canonical` (see TanStack canonical-dedupe caveat).

## Leaf route (`/`)

```ts
head: () => ({
  meta: [
    { title: "Scale Your Startup with Top 5% Vetted Engineers — Acme" }, // ≤60 chars
    { name: "description", content: "We pair you with vetted offshore engineers …" }, // ≤160
    { property: "og:title",       content: "Scale Your Startup with Top 5% Vetted Engineers" },
    { property: "og:description", content: "We pair you with vetted offshore engineers …" },
    { property: "og:url",         content: "/" },
    { property: "og:image",       content: defaultPortraitJpeg }, // LCP image doubles as OG
  ],
  links: [
    { rel: "canonical", href: "/" }, // leaf-only (dedup safe)
  ],
  scripts: [{
    type: "application/ld+json",
    children: JSON.stringify({
      "@context": "https://schema.org",
      "@type": "WebPage",
      name: "Hero",
      primaryImageOfPage: defaultPortraitJpeg,
    }),
  }],
})
```

## Forbidden

- `title` as top-level head field (silently ignored — must be a meta entry).
- Canonical in root (TanStack `links` concatenates; would emit two).
- Absolute URLs baked into `og:url` / `canonical` — use relative; browser resolves.
- Inline JSON-LD without nonce in production CSP (see Batch 4 CSP step).