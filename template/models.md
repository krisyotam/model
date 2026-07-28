# Model slugs

One canonical slug per model, across every vendor. `log.csv`'s `model` column
holds a slug from this table and nothing else.

## The slug

```
<vendor-prefix>-<vendor's own model id>
```

Lowercase, hyphens, no spaces, dots normalized to hyphens. Where the vendor's
own id already carries the prefix (Anthropic ships `claude-*`) the slug is that
id unchanged.

**Using a model that is not listed here? Mint its slug and add the row on that
model's first use, before writing the log row.** Never mint a second slug for a
model that already has one. Never delete a row — old log entries still point at
it; mark it retired in the notes instead.

`effort` lists the reasoning-effort values the model accepts, or `none` when it
has no such setting. Whatever you actually ran with goes in the log's `effort`
column. `none` in the table means `none` in the log.

Rows marked **?** could not be confirmed against vendor docs. Correct them the
next time you use the model.

## anthropic

| slug | model | effort |
|------|-------|--------|
| `claude-opus-5` | Claude Opus 5 | low, medium, high, xhigh, max |
| `claude-fable-5` | Claude Fable 5 | low, medium, high, xhigh, max |
| `claude-mythos-5` | Claude Mythos 5 (Project Glasswing) | low, medium, high, xhigh, max |
| `claude-sonnet-5` | Claude Sonnet 5 | low, medium, high, xhigh, max |
| `claude-opus-4-8` | Claude Opus 4.8 | low, medium, high, xhigh, max |
| `claude-opus-4-7` | Claude Opus 4.7 | low, medium, high, xhigh, max |
| `claude-opus-4-6` | Claude Opus 4.6 | low, medium, high, max |
| `claude-sonnet-4-6` | Claude Sonnet 4.6 | low, medium, high, max |
| `claude-haiku-4-5` | Claude Haiku 4.5 | none |

## openai

| slug | model | effort |
|------|-------|--------|
| `openai-gpt-5-6-sol` | GPT-5.6 Sol (frontier) | none, minimal, low, medium, high, xhigh, max |
| `openai-gpt-5-6-terra` | GPT-5.6 Terra (balanced) | none, minimal, low, medium, high, xhigh, max |
| `openai-gpt-5-6-luna` | GPT-5.6 Luna (cost-optimized) | none, minimal, low, medium, high, xhigh, max |
| `openai-gpt-realtime-2-1` | GPT Realtime 2.1 | none |
| `openai-gpt-realtime-2-1-mini` | GPT Realtime 2.1 mini | none |
| `openai-gpt-image-2` | GPT Image 2 | none |

Each Sol/Terra/Luna tier also ships `-fast` and `-pro` variants; append the
suffix to the slug (`openai-gpt-5-6-sol-pro`). The GPT-5 / 5.5 / o3 / o4-mini
lineage is deprecated (retires late 2026) and deliberately not listed.

## cursor

| slug | model | effort |
|------|-------|--------|
| `cursor-composer-2-5` **?** | Composer 2.5 | none |

Cursor names Composer only by display name in-product; no public API id string
was found. Confirm and correct on first real use.

## xai

| slug | model | effort |
|------|-------|--------|
| `xai-grok-4-5` | Grok 4.5 | none |
| `xai-grok-4-3` | Grok 4.3 | none |
| `xai-grok-4-20-0309-reasoning` | Grok 4.20 (reasoning) | none |
| `xai-grok-4-20-0309-non-reasoning` | Grok 4.20 (non-reasoning) | none |
| `xai-grok-4-20-multi-agent-0309` | Grok 4.20 multi-agent | none |
| `xai-grok-build-0-1` | Grok Build 0.1 | none |
| `xai-grok-imagine-image` | Grok Imagine Image | none |
| `xai-grok-imagine-video-1-5` | Grok Imagine Video 1.5 | none |

xAI documents no reasoning-effort parameter on current models; the old
grok-3-mini low/high split no longer applies.

## moonshot

| slug | model | effort |
|------|-------|--------|
| `moonshot-kimi-k3` | Kimi K3 (flagship, 1M context) | low, high, max (default max) |

Kimi K2.7 Code and K2.6 exist by marketing name; their API id strings were not
confirmed. Add rows when you use them.

## zhipu

| slug | model | effort |
|------|-------|--------|
| `zhipu-glm-4-6` | GLM-4.6 | none (thinking is an on/off toggle, not a scale) |
| `zhipu-glm-5-2` **?** | GLM-5.2 | none, minimal, low, medium, high, xhigh, max **?** |

The GLM-5.2 row came from a single unconfirmed source that may have been a
template page. Verify before trusting it.

## deepseek

| slug | model | effort |
|------|-------|--------|
| `deepseek-v4-pro` | DeepSeek-V4-Pro | high (full range not documented) |
| `deepseek-v4-flash` **?** | DeepSeek-V4-Flash | unconfirmed |
