---
name: retro
description: "Close the loop after a task — what caused friction, which questions should never have been asked, what belongs in the conventions database, and which skill or memory needs changing. Writes the answers back. Use when: retro, retrospect, what did we learn, how do we improve this, that was painful, post-mortem this task, update memory after this."
allowed-tools: [Read, Grep, Glob, Bash, Edit, Write, AskUserQuestion]
model: opus
effort: high
context: main
argument-hint: [spec slug | "session"]
user-invocable: true
---

# Retro

The only stage that changes the system instead of the code. Output is edits to the
conventions database, memory, and skills — not a report.

**Target:** $ARGUMENTS (a spec slug, or the session as a whole)

## 1. Evidence

Read, don't recall:

```bash
CONV="${FLOW_CONVENTIONS:-$HOME/agent-skills/conventions}"
sed -n '/## Deviations/,$p' docs/specs/<slug>.md
git log --oneline -20
grep -rn "TODO" "$CONV" | head -20
```

Session signal worth mining: questions that got asked, questions that should have been
asked, gates that were too big, points where the user corrected course, work that got redone.

## 2. Classify each friction point

| Type | Fix goes to |
|---|---|
| Question that a convention should have answered | `$CONV/<file>.md` or repo `docs/conventions.md` |
| Missing project fact (architecture, ownership, quirk) | repo `docs/conventions.md` — **never** the repo's `CLAUDE.md` |
| Fact about how the user works | `~/.claude/projects/-Users-the userzimmer-agent-skills/memory/` |
| Skill behaved wrong or missed a step | the `SKILL.md` itself |
| Gate was too big / wrongly split | `$CONV/general.md` gate rules |
| Capability that does not exist yet | skill backlog (`flow/BACKLOG.md`) |

A friction point with no destination is not a finding — drop it.

## 3. Write the fixes

Make the edits. For each, show a one-line diff summary. Rules:

- Conventions get **checkable** rules. Replace the `TODO` you are answering; never leave
  both. A rule that contradicts an existing one replaces it, dated.
- Memory files follow the `name`/`description`/`metadata.type` frontmatter, one fact per
  file, and get a line in `MEMORY.md`.
- Skill edits are surgical. Do not rewrite a skill to fix one missed step.
- Never record what the code or git history already says.
- **A project repo's `CLAUDE.md` is read-only.** It is authored by the user and his team; new
  rules go to that repo's `docs/conventions.md` or to `$CONV`, never into `CLAUDE.md`.

## 4. Score the loop

Short, numeric, no prose padding:

```
Gates: <n> · avg diff <n> lines · <n> exceeded 400
Questions asked: <n> — <n> answerable from conventions (should not have been asked)
Stops for uncertainty: <n> · assumptions made: <n> (target 0)
Spec defects found mid-implementation: <n>
Rework: <n> gates revisited
```

Then one sentence: the single change most likely to reduce next task's friction.

## 5. Confirm

Ask once, batched, only about entries where the right rule is genuinely the user's call — not
about whether to record them. Then report what changed as a file list.
