# Dependency RFC + Allowlist (Authoritative)

**Authority:** Promotes the allowlist from `SUPPLY-CHAIN.md` into a stand-alone, versioned document with an RFC template for additions.

## Current allowlist (hero scope, runtime)

See `SUPPLY-CHAIN.md` §Allowlist (canonical table). This file owns the **process**.

## Current denylist

See `SUPPLY-CHAIN.md` §Denylist.

## RFC template (required for any new runtime dependency)

File: `spec-issues/dep-rfc-<package>.md`

```md
# Dependency RFC — <package>@<version>

- **Proposer:** @handle
- **Scope:** runtime | dev | optional
- **Hero-relevant?** yes/no
- **Replaces:** <package> or "new capability"
- **Bundle cost (gz):** <Xkb> measured via `npx vite-bundle-visualizer`
- **License:** MIT/Apache-2.0/...
- **Maintainership:** weekly commits? sole maintainer? funded?
- **Worker/edge compat:** yes/no (see Server-Runtime rules)
- **SSR-safe:** yes/no
- **Alternatives considered:** ...
- **Rejection criteria:** what would make us remove this in 6 months
- **Approvals needed:** @security @platform @hero-owner
```

## Approval gate

- Runtime addition: `@security` + `@platform` + `@hero-owner` (3-of-3).
- Dev addition: `@platform` (1-of-1).
- Removal: same as addition.

## Quarterly review

1. List every runtime dep with `bun pm ls --depth 0`.
2. For each, confirm: still used (no orphans), maintainer alive, no known CVEs, bundle cost within budget.
3. Orphan or stale-maintainer deps → file removal RFC.

## Acceptance

- `package.json` runtime deps == allowlist (CI assertion).
- No package in denylist appears anywhere (`grep` gate).
- Every runtime dep has a passing RFC under `spec-issues/dep-rfc-*.md`.