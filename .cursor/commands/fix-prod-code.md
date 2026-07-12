---
description: Fix app/src/money.ts so all vitest tests pass — minimal diff, tests are the spec.
---

Execute the cookbook prompt in `prompts/fix-prod-code.md` (Production — markdown block).

Target: `app/src/money.ts` (tests in `app/src/money.test.ts` are the specification).

Rules:
- Run `npm test` from `app/` first; read every failing test.
- Edit **only** `app/src/money.ts`.
- Do **not** change `money.test.ts` or weaken assertions.
- Re-run `npm test` and `npm run typecheck` from `app/` — both must pass (0 test failures).

Optional context from user: $ARGUMENTS
