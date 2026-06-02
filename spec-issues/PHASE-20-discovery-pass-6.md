# Phase 20 — Discovery Pass 6 (findings 48–57)

**Mode:** discovery only, no fixes. Drawn from the queued passes at end of Phase 19.

---

## 48. Three.js SSR fallback missing
**What:** SPEC §0 lists "optional Three.js for subtle background particle field." Three.js requires `window` + `WebGLRenderingContext`; both absent in SSR. No `pendingComponent`, no `<noscript>` fallback, no graceful CSS-gradient stand-in. First paint shows nothing where particles should be, then hydration injects canvas → visible pop.
**Why fails:** Lighthouse CLS budget blown; users on bandwidth-saver / no-JS see a featureless dark area.
**Time:** **15 min** — rule: particle canvas mounts client-only post-hydration; underlay is a static CSS radial-gradient that visually approximates particle density; no layout shift on mount; gate behind `prefers-reduced-motion: no-preference` + viewport ≥ 1024px.

## 49. Touch / swipe support on carousel
**What:** Part 2 §24 covers click + keyboard + auto-advance. Mobile users expect horizontal swipe; not in spec. Carousel ARIA pattern (finding #56-pending) implies tabs/feed which don't have native swipe.
**Why fails:** Mobile UX feels broken; or AI installs a swipe lib (`embla-carousel`?) that conflicts with the spec's hand-rolled state machine.
**Time:** **15 min** — rule: pointer-events drag with 24px threshold + velocity-based snap; no library dep; falls back to button navigation if `PointerEvent` unsupported.

## 50. `<noscript>` content not specified
**What:** Part 3 §32 mentions `<noscript>` for JS-disabled fallback. Spec gives no actual block content. Hero must remain trustworthy without JS: H1, lede, body, CTAs as plain `<a>`, first engineer card as static HTML.
**Why fails:** AI either omits `<noscript>` entirely or fills it with a "Please enable JavaScript" banner — both hurt SEO + a11y.
**Time:** **15 min** — spec exact `<noscript>` markup: replicate above-the-fold text + first engineer's portrait + both CTAs (anchor tags). Hide controls (carousel, particles) via `<noscript><style>...</style></noscript>`.

## 51. CODEOWNERS for `spec/hero-section/` missing
**What:** No `.github/CODEOWNERS` entry. Anyone can merge spec changes; design, copy, legal, and engineering reviews aren't enforced. Part 5 §50 sunset triggers and Part 6 §55 a11y assertions have no review gate.
**Why fails:** Drift accumulates between phases; copy ships without legal review; tokens edited by a backend dev breaks design.
**Time:** **15 min** — `.github/CODEOWNERS`: `spec/hero-section/` → `@design-lead @copy-lead @a11y-lead`; `tokens/` → `@design-lead`; `src/components/Hero/**` → `@frontend-lead @design-lead`.

## 52. Glossary missing — visual-effect terms used interchangeably
**What:** "Rim light" (SPEC §0, Part 2 §18), "hot edge" (Part 2 §18 sub-layer B), "halo" (sub-layer A), "amber wash" (GAP §11 Layer C), "warm side" (GAP §portrait-swap-contract), "glow", "breathe" — referring to overlapping but non-identical concepts. No glossary; readers conflate.
**Why fails:** AI implements one layer for all five terms → washed-out look; or implements five layers that double up → muddy gradient.
**Time:** **15 min** — `GLOSSARY.md`: each term → 1-sentence definition + which CSS layer/token it maps to + a thumbnail showing it isolated.

## 53. Canonical image versioning policy
**What:** Marketing will swap the featured engineer eventually. `00-hero-canonical.png` will be overwritten? Versioned `00-hero-canonical-v2.png`? Git history is technically a version log but spec consumers don't read git. GAP-ANALYSIS overrides reference "the canonical" — after swap, do they still apply?
**Why fails:** GAP overrides go stale silently; reviewers can't tell which version a reference points to.
**Time:** **10 min** — rule: canonical images are versioned (`-v1`, `-v2`); `00-hero-canonical.png` is a symlink/copy of latest; GAP entries cite version (`vs canonical-v1`).

## 54. Font loading strategy missing — blocks CLS=0 target
**What:** SPEC §3 names General Sans / Satoshi (display) + Inter (body). Part 5 §54 launch gate requires CLS=0. Font swap from system fallback → custom = layout shift unless **fallback metric-matching** (`size-adjust`, `ascent-override`, etc.) tuned per font. Spec has no `@font-face` block, no `font-display`, no preload, no metric overrides.
**Why fails:** Custom font loads → text reflows → CLS ~0.1+ → launch gate fails.
**Time:** **25 min** — `@font-face` block for each face: `font-display: swap`, `<link rel="preload" as="font">` for the two weights used above-the-fold, `size-adjust`/`ascent-override`/`descent-override` calculated against Inter/Arial fallback (use `@capsizecss/metrics` or Fontaine).

## 55. `<meta name="color-scheme" content="dark">` missing
**What:** Hero is dark-only. Without `color-scheme: dark`, browsers render their UI chrome (scrollbar, form-control defaults, autofill) in light mode → flash + mismatch. Repo `__root.tsx` doesn't set it.
**Why fails:** First paint shows a light scrollbar on top of `#0A0A0A` background — looks broken.
**Time:** **5 min** — `<meta name="color-scheme" content="dark">` in `__root.tsx` `head()` + matching `:root { color-scheme: dark; }` in `src/styles.css`.

## 56. JS/CSS bundle budget for hero — not stated
**What:** Part 5 §54 LCP ≤ 2.0s + Part 5 §49 cost model both depend on shipping a small bundle. No JS budget, no CSS budget, no third-party budget. Particle canvas + Motion + Calendly preconnect + analytics already adds ~80KB; nothing prevents drift.
**Why fails:** Each PR adds "just a little"; in 6 months LCP regresses; nobody noticed because no CI gate.
**Time:** **20 min** — budget table: JS ≤ 80KB (gz), CSS ≤ 12KB (gz), images per-format ≤ 200KB, third-party blocking ≤ 0, third-party deferred ≤ 50KB. Wire as Lighthouse CI `assertions` (finding #57 below).

## 57. Lighthouse CI integration missing
**What:** Part 5 §54 cites Lighthouse as launch gate. Repo has no `.lighthouserc.js`, no GitHub Action, no published-URL test target. Manual runs drift — every contributor uses a different Chrome version, throttling profile, machine.
**Why fails:** "Lighthouse ≥ 95" is aspirational; PRs ship regressions undetected.
**Time:** **25 min** — `.lighthouserc.json` with 3 URLs (`/`, `/?lhci=1`, `/contact` if exists), assertion config tied to budget (finding #56), GitHub Action runs on PR + posts comment with deltas vs main.

---

## Subtotal Phase 20 (if all 10 promoted to fix phases)

~2h 40m of fix work. Add 25% buffer → **~3h 20m**.

## Cumulative project (Phases 1–15 + 16 + 17 + 18 + 19 + 20)

~3h 45m + 3h 25m + 3h 50m + 4h 15m + 3h + 3h 20m = **~21h 35m** to a truly AI-shippable + governance + perf-gated spec.

## Remaining digging passes (queued — not yet run)

Each likely yields 3–5 more issues:

1. **Print stylesheet test target** — Letter vs A4 decision lock (partially addressed in finding #14).
2. **CMS preview-mode (Sanity)** — draft vs published rule missing.
3. **AVIF format skipped** — no rationale; better compression than WebP.
4. **Calendly UTM XSS sanitization** — URL params flow into Calendly query string unvalidated.
5. **Carousel ARIA pattern choice** — tabs vs carousel vs feed; each has different SR expectations.
6. **Hero copy length budgets for i18n** — German +30%, Arabic line-height +20%; no overflow rules.
7. **TanStack Query `staleTime` / `gcTime`** for engineer list — defaults wrong for CMS data.
8. **Performance budget CI gate** — partially addressed in #56/#57; needs PR-comment template.
9. **Test-data privacy in Storybook fixtures** — real-looking names = PII audit finding.
10. **Release cadence vs semver discipline** — Part 5 §45 no cadence.
11. **Component anatomy doc** — no labeled diagram pointing parts to props/tokens.
12. **Snapshot of canonical at every breakpoint** — only one canonical image; no 768/375 references.
13. **Color-token semantic naming audit** — `--accent-strong` vs `--accent` distinction unclear in usage.
14. **Lazy-load below-the-fold** — Calendly script, particle canvas can be deferred; not specified when.
15. **Hydration mismatch surface** — date/time, randomized particles, `Math.random()` seed not stated → React hydration warnings.
16. **CSP `nonce` for inline JSON-LD** — finding #35 adds inline `<script>`; CSP strict mode requires `nonce`.
17. **Memory leaks** — `IntersectionObserver`, carousel timer, particle RAF — no cleanup rules in spec.
18. **Dev-mode debug overlay** — Part 2 §15 mentions `?grid=1`; no other debug flags (e.g. `?slowMotion=1`, `?safeMode=1`).
19. **Asset CDN strategy** — Part 4 §host cache headers, but no spec on which CDN (Cloudflare R2, ImgIX, plain Workers static).
20. **404 / image-fail fallback chain** — what shows if portrait CDN returns 500? Cached version? Skeleton? Default silhouette?

---

## Updated Phase Plan additions

Phases 20-A through 20-J — promote individually as user says "next" after Phases 1–15 ship. Phase 21+ to be authored after pass 7.
