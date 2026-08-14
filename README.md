# model

A convention for giving every AI agent CLI on a machine the same set of facts.

You run Claude Code, Codex, Grok, Antigravity, Gemini, opencode, and a couple
of local models. Each one has its own instruction file, its own config dir,
and no idea what the others know. So you re-explain your SSH hosts to every
one of them, paste the same API key into five places, and none of them knows
it can shell out to the others.

`model` fixes that with two XDG directories and one line of glue.

## The standard

1. Shared facts live under the [XDG Base Directory][xdg] paths:

   | Kind | Path | Default |
   |------|------|---------|
   | Config | `$XDG_CONFIG_HOME/model/` | `~/.config/model/` |
   | State | `$XDG_STATE_HOME/model/` | `~/.local/state/model/` |

   Config holds the primitives. State holds `log.csv`. If `XDG_CONFIG_HOME` or
   `XDG_STATE_HOME` is unset, use the default. If the config dir does not
   exist and `~/.model/` does, read `~/.model/` (the pre-XDG location). New
   installs never create `~/.model/`.

2. Every agent's system file gets **exactly one line** pointing at it. Use the
   resolved paths, not the `$XDG_*` variables; most agents will not expand
   those. On a default layout:

   ```
   Shared primitives (hosts/SSH, subagent CLIs, models.env) live in `~/.config/model/`. Read `~/.config/model/README.md` on launch and append a row to `~/.local/state/model/log.csv` when the session ends.
   ```

   Append it to `~/.claude/CLAUDE.md`, `~/.codex/AGENTS.md`,
   `~/.gemini/GEMINI.md`, `~/.grok/AGENTS.md`,
   `~/.config/opencode/AGENTS.md`, and any other agent's global instructions.

3. Facts live in the config dir once. If a fact is duplicated in an agent's
   system file, move it into `~/.config/model/` and leave the pointer.

That is the whole standard. No loader, no schema, no daemon. Agents already
read Markdown, so the format is Markdown.

[xdg]: https://specifications.freedesktop.org/basedir-spec/latest/

## Core files

| File | Where | What |
|------|-------|------|
| `README.md` | config | Index of the directory and the rules for adding to it |
| `hosts.md` | config | Every machine: IPs, SSH command, sudo, what runs there |
| `subagents.md` | config | How to invoke every other agent CLI headlessly |
| `models.env` | config | Shared env, sectioned per agent, `chmod 600` |
| `models.md` | config | Slug index: one canonical id per model, across every vendor |
| `log.csv` | state | Session log every agent appends to |

Templates for all six are in [`template/`](template/). Copy them, fill them
with your own facts, and never commit the result.

## The session log

One CSV, every agent, one row per session, appended when the session ends:

```
date,company,model,effort,description,prompt,resume
2026-07-28,anthropic,claude-fable-5,high,added the session log,~/.prompt/prompt/model-log.html,claude --resume 9cbb2d13-...
2026-07-28,xai,xai-grok-4.5,none,swept the quant repo for dead code,,grok --resume 41c9
```

CSV rather than Markdown so one table serves every agent and you can still
filter it: `grep -i ',anthropic,' ~/.local/state/model/log.csv` or
`awk -F, '$3=="claude-fable-5"'`. `model` is a slug from `models.md`, never a
marketing name, so the column is filterable. `prompt` points into
[`~/.prompt/`](https://github.com/krisyotam/prompt), the sibling standard
that holds the prompt/loop/graph artifacts. `resume` is whatever that CLI
needs to pick the session back up, so one row is enough to get back into any
past session from any agent.

## The slug index

`models.md` is the registry of model slugs. Format:

```
<vendor-prefix>-<vendor's own model id>
```

`claude-fable-5`, `cursor-composer-2.5`, `openai-gpt-5-6-sol`,
`deepseek-v4`. Lowercase, hyphens, no spaces. Where the vendor's own id
already carries the prefix (Anthropic ships `claude-*`) the slug is the id
unchanged.

**An agent using a model that is not in `models.md` mints the slug and adds
the row on that model's first use, before writing its log row.** The index is
append-only in practice; retired models stay so old log rows still resolve.

## Adding a primitive

New topic gets a new `<topic>.md` in the config dir and one row in the local
`README.md` table. Do not add a config format, a generator, or a CLI. If a
primitive needs tooling to be useful, it does not belong here.

Reasonable additions: `printers.md`, `databases.md`, `deploy.md`,
`registry.md`, `style.md`.

## What does not go here

**Documentation.** Guides, notes, runbooks, and reference material belong in
a normal docs repository of your own (`~/src/docs`, `krisyotam/docs`, or
whatever you already use), synced into the home directory the way you sync
the rest of your dotfiles: a symlink, GNU Stow, or a clone at a known path.
The config dir then references those docs by path rather than containing them.

The split is: the config dir holds facts an agent needs on every launch, and
they have to be short enough to read on every launch. A 400-line guide to
your CDN layout is a doc. The one line saying where that guide lives can go
in the config dir.

**Secrets.** `models.env` is a template with empty values. Real values come
from your secret manager (Infisical, `pass`, 1Password, whatever) and get
written to the local file, which is `chmod 600` and never committed. This
repo ships no keys and neither should your copy of it.

**Per-project instructions.** Those stay in the project's own
`AGENTS.md`/`CLAUDE.md`. Project rules win over these files.

**Session history.** `log.csv` is state, not config. It belongs in
`$XDG_STATE_HOME/model/`, not next to `hosts.md`.

## Install

```sh
: "${XDG_CONFIG_HOME:=$HOME/.config}"
: "${XDG_STATE_HOME:=$HOME/.local/state}"
MODEL_CONFIG="$XDG_CONFIG_HOME/model"
MODEL_STATE="$XDG_STATE_HOME/model"

mkdir -p "$MODEL_CONFIG" "$MODEL_STATE"
cp template/README.md template/hosts.md template/subagents.md \
   template/models.md template/models.env.example "$MODEL_CONFIG/"
cp template/log.csv "$MODEL_STATE/log.csv"
mv "$MODEL_CONFIG/models.env.example" "$MODEL_CONFIG/models.env"
chmod 600 "$MODEL_CONFIG/models.env"

config_disp="${MODEL_CONFIG/#$HOME/~}"
state_disp="${MODEL_STATE/#$HOME/~}"
LINE="Shared primitives (hosts/SSH, subagent CLIs, models.env) live in \`${config_disp}/\`. Read \`${config_disp}/README.md\` on launch and append a row to \`${state_disp}/log.csv\` when the session ends."
for f in ~/.claude/CLAUDE.md ~/.codex/AGENTS.md ~/.gemini/GEMINI.md \
         ~/.grok/AGENTS.md ~/.config/opencode/AGENTS.md; do
  mkdir -p "$(dirname "$f")"
  grep -qF "$config_disp/" "$f" 2>/dev/null || printf '\n%s\n' "$LINE" >> "$f"
done
```

Then fill in `$MODEL_CONFIG/hosts.md` and `$MODEL_CONFIG/models.env`.

## Migrating from ~/.model

```sh
: "${XDG_CONFIG_HOME:=$HOME/.config}"
: "${XDG_STATE_HOME:=$HOME/.local/state}"
mkdir -p "$XDG_CONFIG_HOME/model" "$XDG_STATE_HOME/model"

for f in README.md hosts.md subagents.md models.md models.env; do
  [ -e "$HOME/.model/$f" ] && mv "$HOME/.model/$f" "$XDG_CONFIG_HOME/model/"
done
[ -e "$HOME/.model/log.csv" ] && mv "$HOME/.model/log.csv" "$XDG_STATE_HOME/model/"

config_disp="${XDG_CONFIG_HOME/#$HOME/~}/model"
state_disp="${XDG_STATE_HOME/#$HOME/~}/model"
OLD='Shared primitives (hosts/SSH, subagent CLIs, models.env) live in `~/.model/`.'
NEW="Shared primitives (hosts/SSH, subagent CLIs, models.env) live in \`${config_disp}/\`. Read \`${config_disp}/README.md\` on launch and append a row to \`${state_disp}/log.csv\` when the session ends."
for f in ~/.claude/CLAUDE.md ~/.codex/AGENTS.md ~/.gemini/GEMINI.md \
         ~/.grok/AGENTS.md ~/.config/opencode/AGENTS.md; do
  [ -f "$f" ] || continue
  if grep -qF '~/.model/' "$f" 2>/dev/null; then
    # replace the old one-liner; leave any other mention alone
    tmp="$(mktemp)"
    awk -v old="$OLD" -v new="$NEW" '
      index($0, old) { print new; next }
      { print }
    ' "$f" > "$tmp" && mv "$tmp" "$f"
  fi
done
```

Leave `~/.model/` only as a short pointer if something still looks there.
Do not keep writing new facts into it.

## License

Public domain (Unlicense). It is a directory layout.
