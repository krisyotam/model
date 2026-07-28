# .model

A convention for giving every AI agent CLI on a machine the same set of facts.

You run Claude Code, Codex, Grok, Antigravity, Gemini, opencode, and a couple
of local models. Each one has its own instruction file, its own config dir,
and no idea what the others know. So you re-explain your SSH hosts to every
one of them, paste the same API key into five places, and none of them knows
it can shell out to the others.

`.model/` fixes that with a directory and one line of glue.

## The standard

1. A directory at `~/.model/` holds shared primitives as plain files.
2. Every agent's system file gets **exactly one line** pointing at it:

   ```
   Shared primitives (hosts/SSH, subagent CLIs, models.env) live in `~/.model/`. Read `~/.model/README.md` on launch and append a row to `~/.model/log.csv` when the session ends.
   ```

   Append it to `~/.claude/CLAUDE.md`, `~/.codex/AGENTS.md`,
   `~/.gemini/GEMINI.md`, `~/.grok/AGENTS.md`,
   `~/.config/opencode/AGENTS.md`, and any other agent's global instructions.

3. Facts live in `~/.model/` once. If a fact is duplicated in an agent's
   system file, move it into `~/.model/` and leave the pointer.

That is the whole standard. No loader, no schema, no daemon. Agents already
read Markdown, so the format is Markdown.

## Core files

| File | What |
|------|------|
| `README.md` | Index of the directory and the rules for adding to it |
| `hosts.md` | Every machine: IPs, SSH command, sudo, what runs there |
| `subagents.md` | How to invoke every other agent CLI headlessly |
| `models.env` | Shared env, sectioned per agent, `chmod 600` |
| `log.csv` | Session log every agent appends to |

Templates for all five are in [`template/`](template/). Copy them, fill them
with your own facts, and never commit the result.

## The session log

One CSV, every agent, one row per session, appended when the session ends:

```
date,company,model,description,resume
2026-07-28,anthropic,fable 5,added the session log to the standard,claude --resume 9cbb2d13-ef46-450b-a999-a4aeb82aff82
2026-07-28,xai,grok-4.5,swept the quant repo for dead code,grok --resume 41c9
```

CSV rather than Markdown so one table serves every agent and you can still
filter it: `grep -i ',anthropic,' ~/.model/log.csv` or
`awk -F, '$3=="fable 5"'`. The `resume` column is whatever that CLI needs to
pick the session back up, so one row is enough to get back into any past
session from any agent.

## Adding a primitive

New topic gets a new `<topic>.md` and one row in the local `README.md` table.
Do not add a config format, a generator, or a CLI. If a primitive needs
tooling to be useful, it does not belong here.

Reasonable additions: `printers.md`, `databases.md`, `deploy.md`,
`registry.md`, `style.md`.

## What does not go in .model

**Documentation.** Guides, notes, runbooks, and reference material belong in
a normal docs repository of your own (`~/src/docs`, `krisyotam/docs`, or
whatever you already use), synced into the home directory the way you sync
the rest of your dotfiles: a symlink, GNU Stow, or a clone at a known path.
`~/.model/` then references those docs by path rather than containing them.

The split is: `.model/` holds facts an agent needs on every launch, and they
have to be short enough to read on every launch. A 400-line guide to your
CDN layout is a doc. The one line saying where that guide lives can go in
`.model/`.

**Secrets.** `models.env` is a template with empty values. Real values come
from your secret manager (Infisical, `pass`, 1Password, whatever) and get
written to the local file, which is `chmod 600` and never committed. This
repo ships no keys and neither should your copy of it.

**Per-project instructions.** Those stay in the project's own
`AGENTS.md`/`CLAUDE.md`. Project rules win over `.model/`.

## Install

```sh
mkdir -p ~/.model
cp template/* ~/.model/
mv ~/.model/models.env.example ~/.model/models.env
chmod 600 ~/.model/models.env

LINE='Shared primitives (hosts/SSH, subagent CLIs, models.env) live in `~/.model/`. Read `~/.model/README.md` on launch and append a row to `~/.model/log.csv` when the session ends.'
for f in ~/.claude/CLAUDE.md ~/.codex/AGENTS.md ~/.gemini/GEMINI.md \
         ~/.grok/AGENTS.md ~/.config/opencode/AGENTS.md; do
  mkdir -p "$(dirname "$f")"
  grep -qF '~/.model/' "$f" 2>/dev/null || printf '\n%s\n' "$LINE" >> "$f"
done
```

Then fill in `~/.model/hosts.md` and `~/.model/models.env`.

## License

Public domain (Unlicense). It is a directory layout.
