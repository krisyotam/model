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
| `models.md` | Slug index. One canonical id per model, every vendor. |
| `log.csv` | Session log. Every agent appends one row when a session ends. |

## Model slugs

Every model has one slug, listed in `models.md`, formed as
`<vendor-prefix>-<vendor's own model id>` — lowercase, hyphens, no spaces:
`claude-fable-5`, `cursor-composer-2.5`, `openai-gpt-5-6-sol`, `deepseek-v4`.

**Using a model that is not in `models.md`? Mint its slug and add the row on
first use, before writing the log row.** Never invent a second slug for a
model that already has one, and never retire a row — old log rows still
point at it.

## Session log

Append one row to `log.csv` at the end of a session:

```
date,company,model,effort,description,prompt,resume
2026-07-28,anthropic,claude-fable-5,high,ported the model standard to src,,claude --resume 9cbb2d13-ef46-...
```

- `date` — `YYYY-MM-DD`.
- `company` — provider, lowercase: `anthropic`, `openai`, `cursor`, `xai`, `moonshot`, `zhipu`, `deepseek`.
- `model` — the **slug** from `models.md`. Not the marketing name.
- `effort` — reasoning effort actually used: `none`, `low`, `medium`, `high`, `xhigh`, `max`. `none` if the model has no such setting.
- `description` — one line, what the session did.
- `prompt` — path to the artifact in `~/.prompt/`, e.g. `~/.prompt/loop/pr-triage.html`. Empty for an ad-hoc session.
- `resume` — the full command to resume it, or the bare session id if the CLI has no resume.

Quote any field containing a comma. One table, so `grep -i ',anthropic,'` or
`awk -F, '$3=="claude-fable-5"'` filters it.

## Rules

- Facts live here once. If you find a fact duplicated in an agent's system
  file, move it here and leave the pointer line.
- `models.env` holds no secrets in version control. Real values come from the
  secret manager.
- Documentation lives in the docs repo synced at `<PATH>`, not here. Reference
  it by path.
- Adding a primitive: new `<topic>.md`, one row in the table above. Do not add
  a loader, a schema, or a config format.
