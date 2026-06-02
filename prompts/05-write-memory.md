# 05 — Write Memory (Session Close-out)

**Trigger:** User says "write memory" or "end memory".

This file mirrors `.lovable/prompts/01-write-memory.md`. Follow that file
verbatim. Short version:

1. Append a dated entry to `.lovable/memory/workflow/` describing what
   shipped this session, what is still open, and any decisions taken.
2. Index it in `.lovable/memory/index.md`.
3. If the user gave a verbatim spec, copy it word-for-word into
   `.lovable/memory/specs/NN-<slug>.md`.
4. If something blocked you, log it in `.lovable/pending-issues/`.
5. If you discovered a CI/CD trap, add to `.lovable/cicd-issues/` and
   `.lovable/cicd-index.md`.
6. Do NOT delete history. Mark items done, never erase.