# Onboarding — New Contributor (Hero Section)

**Owner:** DX · **Closes:** #106 · **Target time-to-first-PR:** < 2 hours

## Day 0 — read order
1. `00-index.md` (5 min)
2. `13-spec.md` (15 min)
3. `05-hero-anatomy.md` + `02-tokens.md` (10 min)
4. `04-component-contract.md` (10 min)
5. `46-definition-of-done.md` (5 min)

## Day 0 — local setup
```bash
git clone <repo> && cd <repo>
bun install
cp .env.example .env.local   # request keys from #hero-team
bun dev
```
Open `http://localhost:5173` → hero renders within 2s. If not, check `29-ssr-rules.md` §troubleshooting.

## First PR exercise
Add your name to `37-codeowners.md` shadow list (commented). Open PR → all CI gates must pass (see `47-ci.md`).

## Comms
- Slack: `#hero-team`
- On-call: PagerDuty rotation `hero-frontend`
- Weekly sync: Tue 10:00 CET

## Escalation
Blocked > 2h → ping `@hero-leads`. Production incident → see `43-slo.md` §incident-response.
