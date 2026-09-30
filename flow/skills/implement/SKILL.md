---
name: implement
description: "Execute an approved spec gate by gate, stopping at every gate boundary for review. Resolves unknowns against the conventions database and hard-stops with options when they are genuinely unanswered. Use when: implement the spec, execute gate, build it, start implementation, continue with gate 2, work through the plan."
allowed-tools: [Read, Grep, Glob, Bash, Edit, Write, AskUserQuestion]
context: main
argument-hint: <spec slug> [gate N]
user-invocable: true
---

# Implement

Transcribe an approved spec into code. One gate at a time. The spec is the authority —
if the code needs something the spec does not say, that is a spec defect, not a licence
to decide.

**Spec:** `docs/specs/$ARGUMENTS` (resolve the slug; if absent, stop — run `/spec` first)

## Per-gate loop

### 1. Load

Read the spec. Read the gate. Restate in two lines: what changes, what proves it. If the
gate's model/effort recommendation differs from the live session, say so before starting —
the user decides whether to switch.

### 2. Build

Only what the gate says. Adjacent improvements you notice go to the **Deviations** section
as `noted, not done` — never into the diff. Scope creep inside a gate defeats gated review.

### 3. Unknowns — lookup, then stop

Every unknown runs the lookup order before it becomes a question:

1. `docs/conventions.md` in this repo
2. `CLAUDE.md` in this repo
3. `$FLOW_CONVENTIONS` (default `$HOME/agent-skills/conventions/`)
4. Established pattern in the surrounding code — cite it as the answer

```bash
CONV="${FLOW_CONVENTIONS:-$HOME/agent-skills/conventions}"
grep -rn "<term>" docs/conventions.md CLAUDE.md "$CONV" 2>/dev/null
```

Answered → apply, note the source in Deviations only if it changed the approach.

Not answered → **hard stop**. `AskUserQuestion`, concrete options, recommendation first,
consequence of each spelled out. No assumption, no "I went with X for now", no proceeding
on the parts that depend on the answer. Batch questions that surfaced together; do not
drip-feed.

After the answer: append it to the correct conventions file (repo-specific → repo's
`docs/conventions.md`, cross-project → `$CONV/<file>.md`), then continue. Say which file
you wrote. This is how the question count decays over time.

A project repo's `CLAUDE.md` is **read-only** — it is a lookup source, never a write target.

### 4. Verify

Run the gate's verification. Show the actual output — never claim green without it. Failing
verification means the gate is not done; fix or stop, do not hand over.

### 5. Log and hand over

Append to the spec's **Deviations**:

```
### Gate N
- <deviation> — reason: <why> — decided by: <the user | convention file:line>
- noted, not done: <adjacent finding>
```

Then run `/review-packet <slug> <gate>` and **stop**.

> **Gate N complete. Review?**

No work on gate N+1 until the user accepts. Not "starting the next one while you look" — stop.

## Hard rules

- Spec silent on something behavioural → ask. Never infer intent.
- Spec wrong → stop, say so, propose the amendment. Do not implement around a wrong spec.
- Commit only when asked. Commit scope gets approved first.
- Never weaken or delete a test to hide a failing behaviour. Tests a change turned red are triaged
  with `/write-tests triage` first; a test for a removed or superseded behaviour is deleted and the
  deletion logged in Deviations.
- Secrets never enter code, logs, or a prompt.
