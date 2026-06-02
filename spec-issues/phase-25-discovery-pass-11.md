# Phase 25 — Discovery Pass 11 (findings 98–107)

Queue was empty after Phase 24. Opening a new dig surface: **build/CI, supply chain, observability, and human-process gaps** that the prior 10 passes didn't touch.

Each finding: **What / Why blind-AI fails / Fix path / Time**.

---

## 98. CI pipeline contract missing
- **What:** No documented CI stages (typecheck → lint → test → build → a11y → lighthouse → visual-diff) or order/parallelism rules.
- **Why AI fails:** Adds a GitHub Action that runs only `npm test`; Lighthouse/axe gates from earlier phases never enforced.
- **Fix:** `47-ci.md` — stage table with timeouts, required-vs-advisory, cache keys, artifact retention.
- **Time:** **25 min**

## 99. Preview deploy URL convention undefined
- **What:** No rule for per-PR preview URL, expiry, password protection, or robots header.
- **Why AI fails:** Ships preview URLs indexable; stakeholders share leaked unreleased copy.
- **Fix:** `pr-{n}.preview.example.com`, `X-Robots-Tag: noindex`, 14-day TTL, basic auth in staging.
- **Time:** **15 min**

## 100. Secret rotation policy missing
- **What:** Calendly key, CMS token, CDN signing key, analytics write-key — no rotation cadence or revocation runbook.
- **Why AI fails:** Secrets live in `.env` forever; on incident, nobody knows where they're used.
- **Fix:** `35-secrets.md` — owner, rotation interval (90 d default), break-glass procedure, usage inventory.
- **Time:** **20 min**

## 101. Supply-chain pinning undefined
- **What:** No rule on `package-lock.json` enforcement, `npm audit` gate, Dependabot policy, or `provenance` for publishes.
- **Why AI fails:** Floating ranges (`^`) silently pull a compromised version; lockfile drift between dev/prod.
- **Fix:** Lockfile required, `--frozen-lockfile` in CI, weekly Dependabot, audit gate at high.
- **Time:** **20 min**

## 102. Observability / SLOs missing
- **What:** No SLO for hero TTFB, error rate, CTA conversion. No alerting recipients.
- **Why AI fails:** "It's slow" reported by users, not monitors; no pager target.
- **Fix:** SLO table (TTFB p75 ≤ 600ms, 5xx ≤ 0.1%, CTA click→Calendly load ≤ 1.5s), error budget, PagerDuty rotation.
- **Time:** **25 min**

## 103. Source-map upload policy undefined
- **What:** Spec mentions Sentry-style error reports (#71, #91) but no rule for source-map upload, retention, or auth-token scope.
- **Why AI fails:** Either ships maps publicly (leak source) or never uploads (errors unreadable).
- **Fix:** Upload at deploy, private, 90-day retention, scoped token in CI.
- **Time:** **15 min**

## 104. License/attribution file missing
- **What:** Fonts, icons, portraits, npm OSS — no `50-third-party-notices.md`.
- **Why AI fails:** MIT/Apache attribution clauses violated; potential takedown.
- **Fix:** Auto-generated notices via `license-checker`; committed; PR-gated.
- **Time:** **15 min**

## 105. Incident postmortem template missing
- **What:** No blameless-postmortem format; nothing tied to error-budget burn.
- **Why AI fails:** Same incident recurs; learnings vanish in Slack.
- **Fix:** `POSTMORTEM-TEMPLATE.md` — timeline, root cause, action items with owners + due dates.
- **Time:** **15 min**

## 106. Onboarding doc for new contributors missing
- **What:** No "day-1" path: clone → install → run → first PR → spec map.
- **Why AI fails:** New AI session or new dev re-discovers conventions and writes against archived spec sections.
- **Fix:** `51-onboarding.md` — 30-min path with links to CONVENTIONS, GLOSSARY, 00-INDEX, this audit log.
- **Time:** **20 min**

## 107. Definition of Done for hero feature missing
- **What:** No explicit checklist that says "this hero is shippable": all P0s closed, canonical diff = 0, Lighthouse ≥ 95, axe = 0 violations, copy approved, owners signed off.
- **Why AI fails:** AI declares "done" after visual match; ignores governance/perf/legal gates.
- **Fix:** `46-definition-of-done.md` — 12 checkbox gates, each linked to its owner + evidence artifact path.
- **Time:** **20 min**

---

**Phase-25 subtotal:** ~3h 10m raw → **~4h 00m** with buffer.
**Cumulative (Phases 1–25):** **~41h 25m** total to a fully governed, observable, supply-chain-safe, ship-decidable spec.

## Remaining digging passes queued (10 — newly opened)

1. **Branching strategy** (trunk vs git-flow, release branches, hotfix)
2. **Commit / PR conventions** (Conventional Commits, PR template, squash vs merge)
3. **Feature-flag retirement policy** (max age, removal owner)
4. **Cache invalidation contract** (CDN, browser, service worker, CMS)
5. **Service worker / PWA decision** (in-scope? offline behavior?)
6. **Domain / DNS / TLS ownership** (renewal, CAA records, registrar)
7. **Cookie inventory + banner UX states** (essential / analytics / marketing)
8. **Email / contact channel SLAs** (mailto fallback responder)
9. **Accessibility statement page** (WCAG conformance level published)
10. **Spec sunset / archival policy** (when do old SPEC-PART-*.md files retire)