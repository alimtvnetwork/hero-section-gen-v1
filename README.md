# Project README

> **AI agents: read `.lovable/what-to-read.md` FIRST.** It is the single entry point
> that tells you which files to read, in what order, before you write any code,
> tests, specs, or features.

## What this project is

A TanStack Start v1 + React 19 + Vite 7 + Tailwind v4 application. Backend is
Lovable Cloud (Supabase under the hood). Edge runtime is Cloudflare Workers
(`nodejs_compat`). Styling uses semantic tokens defined in `src/styles.css`
(oklch). All routing is file-based under `src/routes/`.

## Folder structure

```
.
├── README.md                  ← you are here (high-level pointer only)
├── .lovable/                  ← AI memory, plans, prompts, guidelines
│   ├── what-to-read.md        ← AI ENTRY POINT — read first, every session
│   ├── coding-guidelines.md   ← code rules (≤8-line fns, no `any`, etc.)
│   ├── plan.md                ← active roadmap (single file)
│   ├── suggestions.md         ← active + implemented suggestions tracker
│   ├── strictly-avoid.md      ← hard prohibitions
│   ├── cicd-index.md          ← CI/CD issue index
│   ├── prompt.md              ← top-level prompt pointer
│   ├── prompts/               ← reusable prompts + index.md
│   ├── suggestions/           ← per-suggestion verbatim capture + index.md
│   ├── memory/                ← session memory
│   │   ├── index.md           ← master index (every file listed here)
│   │   ├── workflow/          ← in-progress workflow state
│   │   ├── decisions/         ← decision records
│   │   ├── specs/             ← verbatim user specs
│   │   ├── avoid/             ← things the user said to skip
│   │   └── [topic]/
│   ├── pending-issues/        ← open bugs (XX-name.md)
│   ├── solved-issues/         ← closed bugs w/ Solution + Learning
│   └── cicd-issues/           ← CI/CD-specific issues
├── spec/                      ← product/feature specs
│   └── hero-section/          ← canonical hero spec (start at 00-INDEX.md)
├── spec-issues/               ← spec gap-analysis / audit phases
├── src/
│   ├── routes/                ← file-based routes (__root.tsx, index.tsx, ...)
│   ├── components/            ← reusable React components
│   ├── hooks/                 ← custom hooks
│   ├── lib/                   ← business logic, server functions (*.functions.ts)
│   ├── styles.css             ← Tailwind v4 + oklch design tokens
│   ├── router.tsx             ← router config
│   ├── start.ts               ← TanStack Start config (functionMiddleware)
│   └── server.ts              ← SSR entry
├── package.json
├── vite.config.ts
└── tsconfig.json
```

## How an AI should work in this repo

The full step-by-step workflow lives in **`.lovable/what-to-read.md`**.
Quick summary:

1. **Read** `.lovable/what-to-read.md` → it lists every must-read file in order.
2. **Ground** in `.lovable/coding-guidelines.md` before writing any code.
3. **Check** `.lovable/strictly-avoid.md` for hard prohibitions.
4. **Consult** `spec/<feature>/00-INDEX.md` for the feature you're touching
   (e.g. `spec/hero-section/00-INDEX.md` for the hero).
5. **Plan** in `.lovable/plan.md` for non-trivial work.
6. **Implement** following the coding guidelines.
7. **Add unit tests** alongside the code (`*.test.ts` next to the source).
8. **Update memory** at the end of the batch using `.lovable/prompts/01-write-memory.md`.

## How to add a new feature

1. Open or create `spec/<feature-name>/00-INDEX.md` (mirror `spec/hero-section/` layout).
2. Add the spec files: `SPEC.md`, `COMPONENT-CONTRACT.md`, `TOKENS.md` (if new),
   `STATES.md`, `EDGE-CASES.md`, `A11Y.md`.
3. Add a plan entry in `.lovable/plan.md`.
4. Build under `src/routes/` and `src/components/`.
5. Add unit + a11y tests; wire them into the CI matrix described in
   `spec/<feature>/CI.md` (if the feature has one).
6. Run memory-write prompt to record what was done.

## How to add a unit test

- Co-locate: `src/components/Foo.tsx` → `src/components/Foo.test.tsx`.
- Use `bun test` (Vitest-compatible API).
- Server-function tests: `src/lib/foo.functions.test.ts`.
- A11y: Playwright + `@axe-core/playwright` per `spec/<feature>/A11Y-REGRESSION.md`.

## How to write specs

Follow the canonical pattern in `spec/hero-section/`:

- `00-INDEX.md` is the single entry point.
- One concern per file (TOKENS, MOTION, A11Y, FONTS, ...).
- `CHANGELOG.md` with semver, `PRIORITIES.md` for P0/P1/P2 triage,
  `DEFINITION-OF-DONE.md` as the ship gate.
- Verbatim user specs go in `.lovable/memory/specs/`.

## Quick links

- Lovable docs: https://docs.lovable.dev/
- Cloud features: https://docs.lovable.dev/features/cloud
- Troubleshooting: https://docs.lovable.dev/tips-tricks/troubleshooting
