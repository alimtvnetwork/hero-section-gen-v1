# Phase 24 — Discovery Pass 10 (findings 88–97)

Final queued batch. Scope: experimentation, resilience, content lifecycle, sign-off.

Each finding: **What / Why blind-AI fails / Fix path / Time**.

---

## 88. A/B testing guardrails missing
- **What:** No rule for hero variant exposure, SRM (sample-ratio mismatch) checks, holdout, or stop conditions.
- **Why AI fails:** Ships a variant flag with no logging of `variant` on every event → results un-attributable; runs forever past stat-sig.
- **Fix:** `EXPERIMENTS.md` — variant assignment via stable hash, `variant` on all events, SRM alert at p<0.001, max 4-week runtime, owner per experiment.
- **Time:** **25 min**

## 89. Error-boundary copy + retry UX undefined
- **What:** Spec mandates `errorComponent` but never specifies the actual copy, illustration, retry CTA, or fallback link.
- **Why AI fails:** Ships raw stack trace or "Something went wrong" — brand-breaking on a luxury hero.
- **Fix:** Canonical error copy (headline + lede + retry + "Email us" mailto), tone-matched, with screenshot ref.
- **Time:** **15 min**

## 90. Analytics event schema versioning missing
- **What:** No `v` field on event payloads; no breaking-change policy.
- **Why AI fails:** Renames `hero_cta_click` → `hero_primary_click` → all historical dashboards break silently.
- **Fix:** Every event has `v: 1`; additive-only within `v`; breaking change = bump `v` + dual-fire for 30 days.
- **Time:** **15 min**

## 91. Data retention policy missing
- **What:** No TTL on analytics rows, server logs, portrait CDN versions, error reports.
- **Why AI fails:** Stores PII-adjacent data indefinitely → GDPR Art. 5(1)(e) violation.
- **Fix:** Retention table: analytics 25 mo, logs 90 d, error reports 30 d, portrait `-vN` keep last 3.
- **Time:** **15 min**

## 92. Localization fallback order missing
- **What:** No rule for `de-AT` → `de-DE` → `en` resolution when a key is missing.
- **Why AI fails:** Renders raw key (`hero.headline`) or empty string in fallback locale.
- **Fix:** Resolver chain documented; missing-key event `i18n_missing_key` (sampled 1%); CI fails on missing required keys.
- **Time:** **15 min**

## 93. CMS publish rollback procedure missing
- **What:** No documented "oops, revert hero copy" path; no version history requirement on CMS side.
- **Why AI fails:** Bad copy ships, edit-history absent, only fix is git revert + redeploy (slow).
- **Fix:** CMS retains last 20 versions; one-click revert; audit log entry; cache-purge hook.
- **Time:** **20 min**

## 94. Visual QA signoff checklist missing
- **What:** No checklist a human signs before merging hero changes (canonical diff, contrast, motion, print, RTL).
- **Why AI fails:** "LGTM" reviews; canonical drift accumulates.
- **Fix:** `VISUAL-QA.md` — 12-item checklist with Yes/No + screenshot field; PR template includes it.
- **Time:** **15 min**

## 95. Accessibility regression matrix missing
- **What:** No mapping of axe rules → component fixtures → CI gate.
- **Why AI fails:** "AAA target" is aspirational; no PR gate; regressions ship.
- **Fix:** `a11y.matrix.json` — rule × fixture × severity; jest-axe gate at AA, warn at AAA.
- **Time:** **20 min**

## 96. Third-party outage mode undefined
- **What:** Calendly down → CTA hangs. CMS down → blank hero. CDN down → no portrait.
- **Why AI fails:** No degraded mode; user sees broken UI.
- **Fix:** Per-dep fallback: Calendly→mailto, CMS→last-known cache + stale-while-revalidate, CDN→bundled JPEG. Status banner via `hero_dep_outage` event.
- **Time:** **25 min**

## 97. Spec priority labels (P0/P1/P2) not applied across 87 findings
- **What:** Findings 1–87 lack uniform priority labels; "next" order is implicit.
- **Why AI fails:** Without P0/P1/P2, teams cherry-pick easy items and leave shipping blockers.
- **Fix:** Add a `45-priorities.md` table: every finding tagged P0 (ship-blocker) / P1 (pre-launch) / P2 (post-launch). One owner per P0.
- **Time:** **30 min**

---

**Phase-24 subtotal:** ~3h 15m raw → **~4h 05m** with buffer.
**Cumulative (Phases 1–24):** **~37h 25m** to a complete AI-shippable + governed + resilient + experiment-safe + retention-compliant spec.

## Remaining digging passes queued

**None.** All 10 originally-queued passes from Phase 23 are now logged. 97 findings total across 9 discovery passes.

### Suggested next moves (not discovery — execution)

1. Run Phase 1 (the actual fix work) per the table in `README.md`.
2. Or: run a triage pass to assign P0/P1/P2 to all 97 findings (finding #97 itself) before any fix work — ensures fix order matches shipping risk.
3. Or: spawn parallel sub-tracks (a11y / perf / governance / content) each owning a contiguous range of finding IDs.