---
name: Common text-shadow
description: Project-wide default text-shadow applied to headline/body text on dark surfaces.
type: design
---
# Common text-shadow

Canonical token (copy verbatim into every spec's `02-tokens.md`):

```css
--shadow-text-default: rgb(0 0 0) 1px 0.7px 0px;
```

Multi-format:

| token | css | hex | rgb | hsl |
|---|---|---|---|---|
| `--shadow-text-default` | `rgb(0 0 0) 1px 0.7px 0px` | `#000000` | `rgb(0 0 0)` | `hsl(0 0% 0%)` |

**Apply to:** `<h1>..<h3>`, body `<p>`, lede, blockquote, stat numbers, and
any text sitting on `--color-bg`, `--color-bg-elev`, `--color-bg-elev-2`.

**Opt out for:** inputs, code blocks, tooltips, badges with colored fills,
eyebrow text on amber, CTA text on amber, any text on a light surface.

Source of truth: `prompts/06-color-and-typography.md` (Rule 4).