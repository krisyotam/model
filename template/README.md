# .model/

Shared primitives for every agent CLI on this machine. One directory, plain
files, no tooling. Any agent reads what it needs from here instead of asking
again.

Each agent's own system file (`CLAUDE.md`, `AGENTS.md`, `GEMINI.md`, ...) gets
exactly one line pointing here. Nothing else duplicates this content.

## Files

| File | What |
|------|------|
| `hosts.md` | Every machine: IPs, SSH, sudo, what runs there |
| `subagents.md` | How to invoke any other agent CLI headlessly |
| `models.env` | Shared env, sectioned per agent. `chmod 600`. |
| `log.csv` | Session log. Every agent appends one row when a session ends. |

## Session log

Append one row to `log.csv` at the end of a session:

```
date,company,model,description,resume
2026-07-28,anthropic,fable 5,ported the model standard to src,claude --resume 9cbb2d13-ef46-450b-a999-a4aeb82aff82
```

- `date` — `YYYY-MM-DD`.
- `company` — provider, lowercase: `anthropic`, `openai`, `xai`, `google`.
- `model` — the model as a human names it, e.g. `fable 5`, `gpt-5.5`.
- `description` — one line, what the session did.
- `resume` — the full command to resume it, or the bare session id if the CLI has no resume.

Quote any field containing a comma. One table, so `grep -i ',anthropic,'` or
`awk -F, '$3=="fable 5"'` filters it.

## Rules

- Facts live here once. If you find a fact duplicated in an agent's system
  file, move it here and leave the pointer line.
- `models.env` holds no secrets in version control. Real values come from the
  secret manager.
- Documentation lives in the docs repo synced at `<PATH>`, not here. Reference
  it by path.
- Adding a primitive: new `<topic>.md`, one row in the table above. Do not add
  a loader, a schema, or a config format.
