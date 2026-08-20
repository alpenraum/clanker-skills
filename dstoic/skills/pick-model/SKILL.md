---
name: pick-model
description: Evaluate whether the CURRENT model+effort fits a task and recommend the right tier. Use when user asks "which model", "pick model", "model for", "what effort", "is haiku enough", "Opus or Sonnet", "should I downgrade/upgrade", or when routing a non-trivial task to a tier before starting costly/complex work. Judges model AND effort level. Does NOT execute the prompt — only judges fit. Covers tech and non-tech tasks.
model: sonnet
effort: low
context: main
---

# Pick Model

Take the user's intended prompt as input. **Do NOT execute it.** Classify the
prompt, emit **verdict + delta + strategy**: is the current model+effort right
for this task, and if not, what fits.

> **CLAUDE.md contract**: this skill is the single source of truth for the model/effort
> routing call. CLAUDE.md "Execution defaults" carries no model table — it defers here by
> capability (auto-discovery on the description). Don't re-derive or duplicate the tier table
> elsewhere; if routing changes, it changes here.

**Two levers, asymmetric cost**: an effort change keeps the warm cache; a model switch breaks
it and re-reads context uncached. Both viable → prefer 🎚️ effort.

> A **third lever — parallelism** (linear vs fan-out, sub-agents vs Workflow) — is out of scope here.
> To design a *skill/agent's* per-step execution topology (and have it call this skill per step), use `/pick-workflow`.

Recognize-then-route: hit the right tier directly; reserve top-tier (Opus `high` / Fable) for ambiguous/big/can't-classify. Effort = output-spend, not input. See `reference.md` for principles, routing detail, escalators, examples.

## Workflow

### 1. Classify + pick ideal model + effort

Match prompt to tier. Apply escalators (cap +1 tier). See cheatsheet + routing table in `reference.md`.

| Tier | Model | Effort | Shape |
|---|---|---|---|
| Chore | 🟢 Haiku 4.5 | none | convert/format/extract/typo/lookup |
| Plumbing/standard | 🟡 Sonnet 5 | `low`–`high` | GTD/commit, single-file code, content, review, agentic tool use, light multi-file |
| Thinking | 🔴 Opus 5 | `high`→`xhigh` | strategy, hard 3+ file refactor, architecture, **security/audit**, PhD-reasoning |
| Boulder | 🟣 Fable 5 | `high`–`xhigh` | multi-day, sustained ambiguity, long-horizon autonomy |

Effort sets: Opus 5 · Sonnet 5 · Fable 5 all take `low|medium|high|xhigh|max` · Haiku 4.5 takes none (passing it errors).
⚠️ **Default is `high` everywhere** — omitting `effort` equals `high`, so routine work needs an explicit `low`/`medium` or it silently spends. `xhigh` is Claude Code's own default and the best setting for most coding/agentic work; `max` when correctness beats cost.

### 2. Emit verdict

```
🎯 Verdict: [✅ Stay | 🎚️ Change effort | 🔀 Switch | ⬆️ Upgrade | ⬇️ Downgrade]
Current:  [🤖 model] [effort]
Ideal:    [model] [effort]
Why: [task shape vs current capability, 1-2 lines]
🧭 Strategy: [e.g. "start Sonnet 5 med, escalate to Opus high only if scope >3 files or security-sensitive"]
```

**Logic:** same model+effort ok → ✅ Stay · same model, effort off → 🎚️ tweak · diff model → 🔀/⬆️/⬇️ · both levers reach the target → prefer 🎚️ · chore/plumbing on Opus → ⬇️.

## Anti-patterns

- ❌ Opus `high` as default — wastes tokens on chores/plumbing.
- ❌ Effort-as-input cost — it's output spend; on loops higher can be cheaper.
- ❌ Loading `/claude-api` for a model fact — use this skill's table.
