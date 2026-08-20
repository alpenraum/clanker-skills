---
name: second-opinion
description: "Independent analysis of a problem with the user's own theory deliberately withheld, then a reconcile pass that diffs both. Use when: analyse this, what do you think is wrong, second opinion, independent view, validate my theory, I have a suspicion but don't want to bias you, investigate without my input, why does X happen. NOT for challenging an answer already on the table (use /challenge) and NOT for errors you should just fix."
allowed-tools: [Read, Grep, Glob, Bash, AskUserQuestion]
model: opus
effort: high
context: main
argument-hint: <symptom or question, no theory>
user-invocable: true
---

# Second Opinion

Finn has usually already analysed this and is withholding the conclusion on purpose. The
value of this skill is **non-contamination**: an independent line of reasoning he can
agree with, or that contradicts him usefully.

**Target:** $ARGUMENTS

## Phase 1 — Independent analysis

Rules for this phase:

- **Do not ask what he suspects.** If he volunteers a theory mid-phase, note it and keep
  the independent line running to its own conclusion anyway.
- **Read, don't guess.** Every claim cites `file:line`, a command you ran, or a log line.
  A claim you cannot cite is labelled `UNVERIFIED` or deleted.
- Reproduce or observe before explaining. If you cannot observe it, say what you would
  need to.
- Follow the actual call/data path. No pattern-matching to a generic cause.

Deliver:

```
## Observed
<what the system actually does, with evidence>

## Mechanism
<the causal chain, each link cited>

## Root cause
<one sentence. or: "not determinable without X">

## Ruled out
<candidate causes checked and eliminated, with why>

## Confidence
high | medium | low — and what would move it
```

Then stop. Ask: **"Your read?"** Nothing else.

## Phase 2 — Reconcile

Once he states his theory, produce the diff. This is the whole point — be exact, not polite.

```
## Agreement
<where both lines land in the same place>

## Divergence
<each point of difference: his claim, my claim, the evidence that decides it>

## Deciding test
<the cheapest check that settles any open divergence>

## Revised conclusion
<after weighing his input — state plainly if his theory beats mine and why>
```

If his theory is right and mine was wrong, say so in one sentence and move on. If his
theory is wrong, show the evidence that kills it — do not soften it into "both could be
true" when the evidence decides.

## Handover

Nothing here plans or fixes. On agreement, the next step is `/spec` — offer it, do not
start it.
