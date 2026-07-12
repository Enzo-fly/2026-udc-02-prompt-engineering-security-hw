---
description: Add vitest edge cases to money.test.ts only — no production edits. Run after smoke tests are thin.
---

Execute the cookbook prompt in `prompts/add-tests.md` (Production — markdown block).

Target: `app/src/money.ts` / `app/src/money.test.ts`.

Rules:
- Edit **only** `app/src/money.test.ts`.
- Do **not** change `money.ts` or other files.
- Run `npm test` and `npm run typecheck` from `app/` when done.
- If tests fail due to a production bug → stop with a bug report; do not fix `money.ts` (use `/fix-prod-code` next).

Optional context from user: $ARGUMENTS
