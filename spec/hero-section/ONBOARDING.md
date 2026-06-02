# Onboarding — New Contributor (Hero Section)

**Owner:** DX · **Closes:** #106 · **Target time-to-first-PR:** < 2 hours

## Day 0 — read order
1. `00-INDEX.md` (5 min)
2. `SPEC.md` (15 min)
3. `HERO-ANATOMY.md` + `TOKENS.md` (10 min)
4. `COMPONENT-CONTRACT.md` (10 min)
5. `DEFINITION-OF-DONE.md` (5 min)

## Day 0 — local setup
```bash
git clone <repo> && cd <repo>
bun install
cp .env.example .env.local   # request keys from #hero-team
bun dev
```
Open `http://localhost:5173` → hero renders within 2s. If not, check `SSR-RULES.md` §troubleshooting.

## First PR exercise
Add your name to `CODEOWNERS` shadow list (commented). Open PR → all CI gates must pass (see `CI.md`).

## Comms
- Slack: `#hero-team`
- On-call: PagerDuty rotation `hero-frontend`
- Weekly sync: Tue 10:00 CET

## Escalation
Blocked > 2h → ping `@hero-leads`. Production incident → see `SLO.md` §incident-response.
