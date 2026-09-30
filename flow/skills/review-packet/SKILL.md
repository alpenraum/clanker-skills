---
name: review-packet
description: "Prepare a gate's changes for human review — spec conformance, ordered reading path, deviations, verification evidence, and what to look at hardest. Use when: prepare review, review packet, what changed, hand over for review, ready for review, walk me through the diff. Does not review the code itself."
allowed-tools: [Read, Grep, Glob, Bash]
model: sonnet
effort: medium
context: main
argument-hint: <spec slug> [gate N]
user-invocable: true
---

# Review Packet

the user reviews everything himself. This makes that review cheap and ordered — it does not
substitute for it and never says whether the code is good.

**Target:** $ARGUMENTS

## Gather

```bash
git status --short
git diff --stat
git diff
```

Scope to the gate. If the working tree contains changes from an earlier accepted gate,
diff against that gate's accepted point and say what the baseline is.

## Output

```markdown
# Gate <N> — <topic>

**Spec:** docs/specs/<slug>.md · **Diff:** <n> files, +<a>/-<b>

## Reading order
1. `path:line-range` — <what to understand here first, and why it comes first>
2. ...
<order by understanding, not alphabetically: the decision first, then what follows from it>

## Spec conformance
| Spec item | Status | Where |
|---|---|---|
| <in-scope item> | done / partial / skipped | `file:line` |

Out-of-scope items touched: <none, or list — each one is a flag>

## Look hardest at
- `file:line` — <the change where a mistake would be expensive or silent, and what to check>

## Deviations
- <from the spec's Deviations section, verbatim>
- noted, not done: <adjacent findings, so they are not lost>

## Verification evidence
```
<actual command output — not a claim that it passed>
```

## Unreviewed by design
<generated files, lockfiles, mechanical renames — with counts, so skipping them is a choice>
```

## Rules

- Never state a quality judgement. No "clean", no "looks good", no "minor issue".
- Evidence or nothing: no verification section without real output.
- If the diff exceeds ~400 lines, say so and propose where it should have been split —
  that is a gate-planning defect worth recording for `/retro`.
- Mechanical and semantic changes get separated in the reading order. Mixing them is what
  makes big diffs unreviewable.
