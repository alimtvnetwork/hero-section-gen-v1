# Project Readme

> **AI agents: read `.lovable/what-to-read.md` FIRST.** It is the single entry point
> that tells you which files to read, in what order, before you write any code,
> tests, specs, or features.
>
> **If the user gives you one or more reference images and says "follow the
> prompt"** → start at **`prompts/00-index.md`** and run the full
> image → spec → audit loop (steps 01 → 02 → 03) before stopping. After
> step 03, the user drives implementation by typing **`next`** (or
> `next 10`), which consumes `spec/<site>/<section>/45-priorities.md`
> one batch at a time per `prompts/04-next-10.md`.

## What this project is

A TanStack Start v1 + React 19 + Vite 7 + Tailwind v4 application. Backend is
Lovable Cloud (Supabase under the hood). Edge runtime is Cloudflare Workers
(`nodejs_compat`). Styling uses semantic tokens defined in `src/styles.css`
(oklch). All routing is file-based under `src/routes/`.

## Folder structure

```
.
├── readme.md                  ← you are here (high-level pointer only)
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
├── prompts/                   ← REUSABLE AI PROCESS (image → spec → audit → next-10)
│   ├── 00-index.md            ← prompts entry point (read first when given an image)
│   ├── 01-image-to-html.md
│   ├── 02-write-spec.md
│   ├── 03-audit.md
│   ├── 04-next-10.md
│   └── 05-write-memory.md
├── spec/                      ← product/feature specs
│   └── 01-rise-of-asia/01-homepage-hero-section/          ← canonical hero spec (start at 00-index.md)
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
4. **Consult** `spec/<feature>/00-index.md` for the feature you're touching
   (e.g. `spec/01-rise-of-asia/01-homepage-hero-section/00-index.md` for the hero).
5. **Plan** in `.lovable/plan.md` for non-trivial work.
6. **Implement** following the coding guidelines.
7. **Add unit tests** alongside the code (`*.test.ts` next to the source).
8. **Update memory** at the end of the batch using `.lovable/prompts/01-write-memory.md`.

## The `prompts/` workflow (image → spec → next)

This repo treats a reference image as the source of truth for any new UI
section. The `prompts/` folder encodes the exact, reusable process so any
AI (or human) can pick it up cold:

| Step | File | What happens | Who triggers |
|---|---|---|---|
| 1 | `prompts/01-image-to-html.md` | Save canonical image, build anatomy table + tokens + HTML/CSS/JS prototype. | Auto (after user supplies image). |
| 2 | `prompts/02-write-spec.md` | Scaffold `spec/<NN-site>/<NN-section>/` with the full lowercase-hyphen file tree (`00-index.md` … `56-lighthouse-ci.md`). | Auto. |
| 3 | `prompts/03-audit.md` | Gap-analyse image vs spec, score /100, populate `45-priorities.md` with P0/P1/P2 items. | Auto. |
| 4 | `prompts/04-next-10.md` | Execute the next 10 priority items in one batch and report `Remaining items: N`. | **User types `next`** (loop). |
| 5 | `prompts/05-write-memory.md` | Persist session state for the next AI. | User types `write memory`. |

**Hard rule:** Steps 1 → 2 → 3 always run together. Stopping after step 1
or 2 is a failure mode because the `next` keyword has nothing to consume.

### How the user drives it

1. Drop one or more images in chat, say *"follow the prompt"*. AI runs 1-3.
2. Repeat: type **`next`** (or `next 10 with reasoning and time`) until
   `Remaining items: 0`.
3. Type **`gap analysis`** any time to re-score the spec.
4. Type **`write memory`** at end of session.

Folder selection is automatic from the section slug. If the user wants a
different section, they say *"new section: <name>"* and the AI creates
`spec/<NN-site>/<NN-new-section>/` before running step 1.

## How to add a new feature

1. Open or create `spec/<feature-name>/00-index.md` (mirror `spec/01-rise-of-asia/01-homepage-hero-section/` layout).
2. Add the spec files: `13-spec.md`, `04-component-contract.md`, `02-tokens.md` (if new),
   `10-states.md`, `09-edge-cases.md`, `24-a11y.md`.
3. Add a plan entry in `.lovable/plan.md`.
4. Build under `src/routes/` and `src/components/`.
5. Add unit + a11y tests; wire them into the CI matrix described in
   `spec/<feature>/47-ci.md` (if the feature has one).
6. Run memory-write prompt to record what was done.

## How to add a unit test

- Co-locate: `src/components/Foo.tsx` → `src/components/Foo.test.tsx`.
- Use `bun test` (Vitest-compatible API).
- Server-function tests: `src/lib/foo.functions.test.ts`.
- A11y: Playwright + `@axe-core/playwright` per `spec/<feature>/54-a11y-regression.md`.

## How to write specs

Follow the canonical pattern in `spec/01-rise-of-asia/01-homepage-hero-section/`:

- `00-index.md` is the single entry point.
- One concern per file (TOKENS, MOTION, A11Y, FONTS, ...).
- `21-changelog.md` with semver, `45-priorities.md` for P0/P1/P2 triage,
  `46-definition-of-done.md` as the ship gate.
- Verbatim user specs go in `.lovable/memory/specs/`.

## Quick links

- Lovable docs: https://docs.lovable.dev/
- Cloud features: https://docs.lovable.dev/features/cloud
- Troubleshooting: https://docs.lovable.dev/tips-tricks/troubleshooting
