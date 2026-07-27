# Subagents

Any agent here can orchestrate any other. All commands below are headless
(single prompt in, text out) and safe to shell out to. Verified against the
binaries installed on this machine; re-check after upgrades.

| Agent | Headless invocation | Model flag |
|-------|--------------------|-----------|
| Claude | `claude -p "PROMPT"` | `--model fable\|opus\|sonnet\|haiku` |
| Codex | `codex exec "PROMPT"` | `-m` |
| Grok | `grok -p "PROMPT"` | `-m` |
| Antigravity | `agy -p "PROMPT"` | `--model` |
| Gemini | `gemini -p "PROMPT"` | `-m` |
| pi | `pi -p "PROMPT"` | `--model provider/model` |
| opencode | `opencode run "PROMPT"` | `-m provider/model` |
| Local | `ollama run MODEL "PROMPT"` | n/a |

## Rules

- Fan-out: launch several with `&` and wait. Each is a fresh context, so the
  whole task must be in the prompt string.
- Auto-approve flags exist (`--yolo`, `--always-approve`,
  `--dangerously-skip-permissions`). Do not pass them unless told to.
- If a CLI is a wrapper script (secret injection, environment setup), call the
  wrapper, never the raw binary.
- Prefer the cheapest agent that can do the job. Research and mechanical
  checks to the small models, implementation to the mid tier, architecture to
  the large one.
- Some repos ban specific agents. Repo instructions win over this file.
