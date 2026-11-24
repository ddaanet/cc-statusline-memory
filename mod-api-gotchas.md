---
name: mod-api-gotchas
description: "Claude Code mod (hooks module) API traps hit while writing hooks/register.ts — hookEnv, http.fetch result shape, validator helper rule"
metadata:
  node_type: memory
  type: reference
  originSessionId: a632831f-404a-489b-8cec-7fc7cbea722c
  modified: 2026-11-24T16:41:18.476Z
---

Traps in the Claude Code 2.1.330 mod API, none of which `claude plugin test` catches:

- `$.process.hookEnv()` returns only Claude Code's own `CLAUDE_*` variables (session id, plugin root/data, project dir), not HOME or XDG_RUNTIME_DIR. Read those through `$.process.run(['sh','-c','printf …"$HOME"'])`, whose child inherits the process env.
- An `http.fetch` hook's `await next(e)` resolves `{ value: HttpResponse } | { deny }`: headers are at `res.value.headers` (lower-cased), not `res.headers`. Engine model requests have `next.origin.plugin === 'engine'`.
- `claude plugin validate` refuses passing `$` to a helper unless that helper is a top-level function declaration (or const bound to one) in the module or a file it imports; a closure inside `register` fails.
- Imports between the module's own files work extensionless (`from './fable'`); a `.ts` suffix fails the validator's type-check (TS5097).
