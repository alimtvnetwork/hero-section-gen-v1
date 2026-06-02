# What to Read — AI Entry Point

> **Mandatory first read for every AI session.** If you start writing code,
> tests, or specs without reading the files in §1, you are doing it wrong.
> Stop and start over.

---

## 1. Read order (mandatory, every session)

Read these in order. Skip none. If a file is missing, create it from the
templates referenced in `.lovable/prompts/01-write-memory.md`.

1. **`readme.md`** (repo root) — high-level layout + how-to overview.
2. **`.lovable/what-to-read.md`** (this file) — workflow contract.
3. **`.lovable/coding-guidelines.md`** — ≤8-line fns, no `any`, no magic
   strings, Enums in dedicated files, Boolean naming, error handling.
4. **`.lovable/strictly-avoid.md`** — hard prohibitions. Violating these
   reverts the PR.
5. **`.lovable/plan.md`** — active roadmap. Check status before starting
   anything; update before finishing.
6. **`.lovable/suggestions.md`** — open + implemented suggestions.
7. **`.lovable/cicd-index.md`** — known CI/CD issues; don't re-debug them.
8. **`.lovable/prompts/index.md`** — registry of reusable prompts.
8b. **`prompts/00-index.md`** (root) — REUSABLE IMAGE → SPEC → AUDIT → NEXT-10
    process. When the user gives an image and says "follow the prompt", start here.
8c. **`prompts/01-image-to-html.md`**, **`prompts/02-write-spec.md`**,
    **`prompts/03-audit.md`**, **`prompts/04-next-10.md`**,
    **`prompts/05-write-memory.md`** — read ALL FIVE in order. They define
    every step of the image → spec → audit → `next` → memory loop. Do not
    skip ahead; Step 3 must finish before the user can invoke `next`.
8d. **`mem/index.md`** — top-level project memory index (separate from
    `.lovable/memory/`). Always-in-context core rules + topical pointers.
9. **`.lovable/memory/index.md`** — master index. Every memory file is
   listed here; if not, it's an orphan — fix it.
10. **`.lovable/memory/workflow/`** — current in-progress workflow state.
11. **`spec/<feature>/00-index.md`** — for the feature you're touching.
    Hero example: `spec/01-rise-of-asia/01-homepage-hero-section/00-index.md`.
12. **`spec-issues/readme.md`** + relevant `phase-xx-*.md` files — audit
    history; check before re-investigating a finding.

---

## 2. Folder map (full)

```
.
├── readme.md
├── .lovable/
│   ├── what-to-read.md            ← THIS FILE
│   ├── overview.md                ← project overview (create if missing)
│   ├── user-preferences.md        ← user comms style + preferences
│   ├── coding-guidelines.md       ← see §3
│   ├── strictly-avoid.md          ← hard prohibitions
│   ├── plan.md                    ← single-file roadmap
│   ├── suggestions.md             ← single-file tracker
│   ├── cicd-index.md              ← CI/CD index
│   ├── prompt.md                  ← pointer → prompts/index.md
│   ├── prompts/
│   │   ├── index.md
│   │   └── 01-write-memory.md
│   ├── suggestions/
│   │   ├── index.md
│   │   └── XX-<name>.md           ← verbatim suggestion capture
│   ├── memory/
│   │   ├── index.md               ← master index
│   │   ├── workflow/              ← active workflow state
│   │   ├── decisions/             ← decision records (XX-name.md)
│   │   ├── specs/                 ← VERBATIM user specs (never paraphrase)
│   │   ├── avoid/                 ← things user said to skip
│   │   └── <topic>/               ← grouped topical memory
│   ├── pending-issues/            ← open bugs (XX-name.md)
│   ├── solved-issues/             ← closed bugs (+ Solution + Learning)
│   └── cicd-issues/               ← CI/CD-specific issues
├── spec/
│   └── 01-rise-of-asia/01-homepage-hero-section/              ← reference layout for all features
│       ├── 00-index.md            ← single entry point per feature
│       ├── 13-spec.md, SPEC-PART-*.md
│       ├── 04-component-contract.md, 02-tokens.md, 23-motion.md, 24-a11y.md, ...
│       ├── 47-ci.md, 56-lighthouse-ci.md, 53-qa-device-matrix.md
│       ├── 21-changelog.md, 45-priorities.md, 46-definition-of-done.md
│       └── 19-archived.md
├── spec-issues/                   ← audit phases / gap analyses
├── src/
│   ├── routes/                    ← file-based routes
│   │   ├── __root.tsx             ← root layout (always present)
│   │   ├── index.tsx              ← home
│   │   └── api/                   ← server routes (webhooks under api/public/)
│   ├── components/                ← reusable components
│   ├── hooks/                     ← custom hooks
│   ├── lib/                       ← business logic + *.functions.ts server fns
│   ├── styles.css                 ← Tailwind v4 + oklch tokens (TOKEN SOURCE)
│   ├── router.tsx
│   ├── start.ts                   ← functionMiddleware: [attachSupabaseAuth]
│   └── server.ts
├── package.json, vite.config.ts, tsconfig.json
└── bun.lock
```

---

## 3. Coding workflow

Before writing code:

1. Read `.lovable/coding-guidelines.md` (and `spec/error-manage/` if it exists).
2. Read the relevant `spec/<feature>/00-index.md`.
3. Check `.lovable/strictly-avoid.md`.
4. Check `.lovable/pending-issues/` and `.lovable/solved-issues/` for prior art.

While writing code:

- Functions ≤ 8 lines. No nested ifs. No negative boolean names.
- No `any` / `unknown` / inline definitions. Use Enums + Constants.
- One concern per file. Files ≤ 100 lines.
- Reusability first — extract before duplicating.
- React/TS components: small, composable, in `src/components/`.
- Design tokens only from `src/styles.css` — never hex/rgb in components.
- Server logic: `createServerFn` in `*.functions.ts`. Webhooks under
  `src/routes/api/public/` with signature verification.

After writing code:

- Add unit tests co-located (`Foo.tsx` → `Foo.test.tsx`).
- Update `.lovable/plan.md` task status.
- If you discovered something the next AI must know, add to
  `.lovable/memory/` and update `.lovable/memory/index.md`.
- Trigger memory write via `.lovable/prompts/01-write-memory.md`.

---

## 4. Adding a new feature

1. Create `spec/<feature>/00-index.md` (mirror `spec/01-rise-of-asia/01-homepage-hero-section/`).
2. Add: `13-spec.md`, `04-component-contract.md`, `10-states.md`, `09-edge-cases.md`,
   `24-a11y.md`. Reuse `spec/01-rise-of-asia/01-homepage-hero-section/02-tokens.md` unless new tokens needed.
3. Open a `.lovable/plan.md` entry.
4. Build under `src/routes/` + `src/components/`.
5. Add unit, a11y, and visual tests per `spec/<feature>/47-ci.md`.
6. Update `spec/<feature>/21-changelog.md` and `46-definition-of-done.md`.
7. Run memory write.

---

## 5. Adding a unit test

- Co-located: `*.test.ts` / `*.test.tsx` next to the source file.
- Runner: `bun test` (Vitest-compatible).
- A11y: Playwright + `@axe-core/playwright`.
- Visual: Chromatic / Playwright snapshots per
  `spec/<feature>/22-snapshots.md`.
- Server-function tests: invoke through `useServerFn` test harness or
  call the handler directly with a mocked context.

---

## 6. Writing/editing specs

- Entry point per feature: `spec/<feature>/00-index.md`.
- One concern per file (TOKENS, MOTION, A11Y, FONTS, CSP, ...).
- Semver `21-changelog.md`; P0/P1/P2 in `45-priorities.md`;
  ship gate in `46-definition-of-done.md`.
- Verbatim user input → `.lovable/memory/specs/XX-<slug>.md`.
- Never delete history — archive in `19-archived.md`.

---

## 7. Anti-patterns (do not do)

- Writing to `mem://` directly. Use `.lovable/memory/` files.
- Creating `.lovable/memories/` (plural). The correct path is
  `.lovable/memory/`.
- Paraphrasing user specs — capture verbatim.
- Deleting history — mark done, never erase.
- Orphan files — every memory file must appear in
  `.lovable/memory/index.md`.
- Writing code without reading `.lovable/coding-guidelines.md`.
- Mixing `pending-issues/` and `solved-issues/` — files move, never duplicate.
- Putting layouts under `src/pages/` or `src/routes/_app/index.tsx`
  (TanStack Start uses `src/routes/__root.tsx`).

---

## 8. Quick reference — where things live

| You need to … | Read / write |
|---|---|
| Understand the project | `readme.md`, `.lovable/overview.md` |
| Know what to read next | `.lovable/what-to-read.md` (this file) |
| Follow code rules | `.lovable/coding-guidelines.md` |
| Avoid known traps | `.lovable/strictly-avoid.md` |
| See active work | `.lovable/plan.md` |
| Find a feature spec | `spec/<feature>/00-index.md` |
| Add a verbatim user spec | `.lovable/memory/specs/XX-<slug>.md` |
| Log a bug | `.lovable/pending-issues/XX-<name>.md` |
| Resolve a bug | move to `.lovable/solved-issues/` + Solution + Learning |
| Add a CI/CD note | `.lovable/cicd-issues/XX-<name>.md` + `cicd-index.md` |
| Add a reusable prompt | `.lovable/prompts/XX-<name>.md` + `prompts/index.md` |
| End the session | run `.lovable/prompts/01-write-memory.md` |
