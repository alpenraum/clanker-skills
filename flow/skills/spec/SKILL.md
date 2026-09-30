---
name: spec
description: "Write the change contract before implementing — exact scope, out-of-scope, topic gates, model+effort per gate, and resolved open questions. Tiers from a one-screen contract to a full OpenSpec proposal. Use when: spec this, plan the fix, plan the implementation, what exactly are we changing, scope this, before you implement, let's define what to change. Runs after analysis, before /implement."
allowed-tools: [Read, Grep, Glob, Bash, Write, Edit, AskUserQuestion]
model: opus
effort: high
context: main
argument-hint: <what to change> [--tier micro|standard|full]
user-invocable: true
---

# Spec

Turn an agreed analysis into a contract precise enough that implementation needs no
conversation. Scope ambiguity is the failure mode this exists to kill.

**Change:** $ARGUMENTS

## 1. Pick the tier

Judge from the change, then **state the tier and why in one line** before writing anything.

| Tier | When | Artifact |
|---|---|---|
| **micro** | Single topic, one layer, no API/behaviour change. Extracting a method into a use case, renaming, moving a widget. | Inline contract in chat. No file. |
| **standard** | Multiple files or layers, observable behaviour changes, or ≥2 topics. Most bug fixes and small features. | `docs/specs/<slug>.md` |
| **full** | New capability, protocol or API change, cross-repo, or fleet/firmware compatibility involved. | OpenSpec proposal via `/openspec-plan`, with the gate plan from §4 attached |

Overrule the table only on explicit request. `--tier` in `$ARGUMENTS` wins.

## 2. Resolve unknowns without asking first

For every open question, walk the lookup order:

1. `docs/conventions.md` in the target repo
2. `CLAUDE.md` in the target repo
3. `$FLOW_CONVENTIONS` (default `$HOME/agent-skills/conventions/`) — `general`, `naming`, `logging`, `architecture`, `testing`, `git`, plus the language file
4. The code itself — an established local pattern is an answer

```bash
CONV="${FLOW_CONVENTIONS:-$HOME/agent-skills/conventions}"
ls "$CONV"
grep -rn "<term>" docs/conventions.md CLAUDE.md "$CONV" 2>/dev/null
```

Answered → apply silently, cite the source in the spec.
Not answered → collect it. Do **not** guess, and do **not** ask one at a time.

Then ask all remaining questions in **one** `AskUserQuestion` round, each with concrete
options and a recommendation first. After the answers land, append every convention-shaped
answer to the right file under `$CONV` (or the repo's `docs/conventions.md` when it is
project-specific) so it is never asked again. Say which file you wrote to.

A project repo's `CLAUDE.md` is **read-only** — read it for context, never write to it.

Questions have no deadline. Never proceed on an unanswered question.

## 3. Write the contract

```markdown
---
slug: <kebab-slug>
tier: micro | standard | full
created: <YYYY-MM-DD>
status: draft
---

# <Title>

## Problem
<one paragraph, evidence-cited — from the analysis, not restated assumptions>

## In scope
- <exact change, file-level where known>

## Out of scope
- <the things a reasonable reader would assume are included but are not>

## Approach
<the mechanism of the change, layer by layer. Enough that implementation is transcription.>

## Gates
<from §4>

## Verification
- <command or observation that proves each gate, incl. what the user should see>

## Conventions applied
- <rule> — source: <file:line>

## Open questions
- <resolved: answer + who decided> | none

## Deviations
<empty — /implement appends here>
```

Path: `docs/specs/<slug>.md` in the **target repo**, committed with the change.

## 4. Plan the gates

Split by **topic**, never by file count. A gate is a unit the user can review and reason about
on its own — reviewing 2k lines at once is the thing being prevented here.

Split when: expected diff > ~400 lines · more than one topic · a layer boundary is crossed ·
a risky change (protocol, crypto, migration) can be isolated from mechanical work.

Each gate gets:

```
### Gate N — <topic>
Scope: <what changes>
Files: <paths>
Est. diff: ~<n> lines
Verify: <command / observation>
Model: <model> · effort: <level> — <one-line reason>
Blocked by: Gate <n-1> acceptance | nothing
```

Order gates so risky/architectural topics land first while attention is fresh, mechanical
follow-through later. A gate that only exists to make another gate compile is not a gate —
fold it in.

## 5. Call model + effort per gate

Mandatory, not optional. Use `/pick-model` for the live session read and the cost rule
(effort change keeps cache, model switch breaks it). Default shape:

- Mechanical, pattern-following, high-volume edits → `sonnet`, effort `medium`
- Log/CI parsing, dependency bumps, renames → `haiku`, effort `low`
- Protocol, crypto, concurrency, compat-across-firmware, ambiguous scope → `opus`, effort `high`

State the recommendation per gate. If the whole spec runs on one tier, say that explicitly
rather than repeating it per gate.

## 6. Approval gate

Print the contract, then stop with:

> **Scope locked?** — approve, or name what is wrong.

No implementation from this skill. Ever. On approval, hand to `/implement <slug>`.
