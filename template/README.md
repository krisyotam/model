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

## Rules

- Facts live here once. If you find a fact duplicated in an agent's system
  file, move it here and leave the pointer line.
- `models.env` holds no secrets in version control. Real values come from the
  secret manager.
- Documentation lives in the docs repo synced at `<PATH>`, not here. Reference
  it by path.
- Adding a primitive: new `<topic>.md`, one row in the table above. Do not add
  a loader, a schema, or a config format.
