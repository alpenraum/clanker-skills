# General

## Working agreement

- Analyse before deciding. No decision rests on an assumption that was never checked against the code.
- Never assume when uncertain — consult these files, then ask with concrete options.
- Multiple viable paths → present them, Finn picks. Do not pick silently.
- Review is Finn's job, fully. Skills prepare review, never replace it.
- Questions have no deadline. Never time out a question or proceed because an answer is slow.
- A project repo's `CLAUDE.md` is read-only. Read it; write new rules to that repo's `docs/conventions.md` or here.

## Gates

- Any change expected to exceed **~400 changed lines** or touch **more than one topic** is split into topic gates.
- A gate is one reviewable topic, not one file and not one commit-sized slice of a topic.
- Gate boundary = stop, hand over a review packet, wait. No work on gate N+1 before gate N is accepted.
- Accepted gate may still be revised later; revision is a new gate, not a reopen.

## Clean code — modern reading, not ceremony

- Clarity and small honest units. Not class-per-noun, not one-method-per-line decomposition.
- A clear 10-line function beats a "clean" 5-class hierarchy. Over-abstraction is as much a defect as under-abstraction.
- No interface, port, or extension point without a second implementation or a real test seam **today**.
- **YAGNI** — build exactly what the spec asks. No config flags, hooks, or generality for a future that is not specced.
- **KISS** — the simplest construction that satisfies the spec wins. Extra complexity needs a stated reason in the spec, not a comment defending it afterwards.
- Dead code, unused parameters, and orphaned abstractions get deleted in the gate that reveals them, never left "just in case".
- Use the existing shared primitive instead of re-implementing it locally — never hand-roll a timer, poller, retry, or cache that already exists.
- Cross-cutting utilities are designed domain-agnostic from the start, not built for the first domain and generalised later.
- Wiring a new pattern in covers every consuming call site in one pass — no follow-up PRs unless asked.

## Comments

- Needing a comment to explain **what** code does is a signal the code is too complex. Simplify, rename, or extract first; comment only if that fails.
- What survives is the **why** the code cannot express: a protocol or firmware quirk, a load-bearing ordering, a constraint from outside the system.
- Never longer than the code it explains. No doc comment restating a signature or parameter name. No narration of guards, defaults, or control flow.
- Documentation of a non-obvious mechanism belongs in the spec or an architecture note, not in a comment block above the function.
- Reusable library surface may carry doc comments; application code does not.
- No comments describing what a change did — that is the commit message.
- Applies to tests. A test name is its description.

## Code-level style

- Guard clauses and early return. Never nested-if pyramids.
- Magic numbers become named constants with the unit in the name (`STABILITY_THRESHOLD_DEG`), declared next to the logic that uses them.
- Full words in names. No abbreviations beyond `id`, `url`, `ui`, `api`, `db`.
- Model flows as explicit state machines (`Guide → Idle → Measuring → Result`), never as combinations of boolean flags.
- No truthiness, no loose equality. Compare explicitly (`x == null`, not `!x`) — `0` and `""` are values, not absence.
- Static typing is non-negotiable. A type system bolted on after the fact is unsafe theatre, not a guarantee.

## Design system

- No hardcoded visual values in components. Typography and colour come from the design system, derived via copy/override (Flutter: `TextTheme.of` / `ColorScheme.of` + `.copyWith()`; Compose: `MaterialTheme.typography` / `colorScheme`).
- No design-system entry fits → ask before adding one. Never inline a one-off value.
- Semantic states stay visually distinct: warning ≠ error ≠ info. Two different problems never render identically on one screen.
- A shared component that hardcodes a semantic value gains an optional override parameter — never a fork of the component.

## Build and tooling

- CI: when a script needs a richer shell, fix the invocation — don't downgrade the script to the lowest common denominator.
- A mandated process doc (e.g. a security audit note) is skipped when the thing it documents carries no real risk. Confirm that first; don't assume it.

## Definition of done

TODO — ask once: does "done" require passing CI, or is a green local run enough to hand over for review?
