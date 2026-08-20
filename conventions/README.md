# Conventions — the answer database

Every question the flow skills would otherwise ask you lives here. The rule:

> **Consult before asking.** If the answer is in these files or in the target repo's
> `docs/conventions.md`, apply it silently. Ask only when nothing here answers it —
> then write the answer back so it is never asked twice.

## Lookup order

1. **Target repo** `docs/conventions.md` — project-specific, wins on conflict
2. **Target repo** `CLAUDE.md` — project instructions
3. **This directory** — cross-project defaults (`$FLOW_CONVENTIONS`, default `$HOME/agent-skills/conventions`)
4. **Ask** — via `AskUserQuestion` with concrete options, then append the answer to the right file below

## Files

| File | Answers |
|------|---------|
| [general.md](general.md) | Working agreement, gates, Boy Scout rule, clean code (YAGNI/KISS), comments, code-level style, design system |
| [architecture.md](architecture.md) | Architecture-first, separation of concerns, use-case pattern, presentation, error handling, boundaries |
| [testing.md](testing.md) | What gets tested, quality over quantity, testability as a design constraint |
| [naming.md](naming.md) | Names for symbols, constants, tests, branches |
| [logging.md](logging.md) | Levels, format, what never gets logged |
| [git.md](git.md) | Commit approval, PR shape, what stays read-only |
| [dart-flutter.md](dart-flutter.md) | Dart/Flutter/riverpod specifics |
| [kotlin.md](kotlin.md) | Kotlin/Gradle/Compose specifics |
| [OPEN-QUESTIONS.md](OPEN-QUESTIONS.md) | Decisions still needed, and rules deliberately not adopted |

## Status

Live. `TODO` marks an unanswered question — the first time flow hits one it asks you once,
records the answer, and drops the marker. Density grows by use, not by upfront authoring.

Rules arrive from three places: what Finn states directly, what `/retro` extracts from
friction, and what gets harvested from project memory. A rule that was considered and
rejected goes to `OPEN-QUESTIONS.md` so it does not get re-proposed.

## Writing rules for these files

- One decision per bullet. State the rule, not the rationale — unless the rationale is
  the load-bearing part, then one clause.
- Rules must be checkable. "Prefer clear names" is not a rule; "no abbreviations except
  `id`, `url`, `ui`" is.
- Contradiction is a bug. When a new answer conflicts with an existing rule, replace the
  rule and note the date.