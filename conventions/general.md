# General

## Working agreement

- Analyse before deciding. No decision rests on an assumption that was never checked against the code.
- Never assume when uncertain — consult these files, then ask with concrete options.
- Multiple viable paths → present them, the user picks. Do not pick silently.
- Review is the user's job, fully. Skills prepare review, never replace it.
- Questions have no deadline. Never time out a question or proceed because an answer is slow.
- A project repo's `CLAUDE.md` is read-only. Read it; write new rules to that repo's `docs/conventions.md` or here.
- **Name the blast radius that was not inspected.** A change to a shared abstraction lists that abstraction's other consumers and says, per consumer, whether it was looked at. Reporting "done" over an uninspected radius is a false report, not brevity.

### This is a team, not a solo run

- **Working alone is not a requirement.** Asking costs one turn. Inferring around a gap costs many
  turns and can still be wrong.
- **When the user can observe something more directly than you can, ask him to look.** A device's front
  panel, a UI, a physical state, a log on a machine you cannot reach, whether a thing is actually
  running — these are one message for him and an inference chain for you.
- **When the user can act more directly than you can, ask him to act.** Starting hardware, flipping a
  physical switch, an interactive login, anything behind a credential you do not hold.
- **Ask early, not after the inference fails.** The trigger is "he can see this and I cannot", not
  "I have exhausted my options".
- **Say what you would do with the answer.** A request for an observation names the question it settles,
  so he knows what to look for and why it matters.
- **An unexplained result is a question, not a puzzle to solve silently.** If the system does something
  you cannot account for, say so and ask — he may know the reason in one line.

## Gates

- Any change expected to exceed **~400 changed lines** or touch **more than one topic** is split into topic gates.
- **The spec's gate table carries a line estimate per gate, written before implementation starts.** A gate estimated over the limit is split in the spec. Discovering the size while writing the code is too late — the diff already exists.
- Splitting a gate mid-implementation is a **spec defect**: log it as one, do not present it as a routine decision.
- Estimating is cheap and coarse: count the files the gate names and the seams it crosses. Pure logic and the stateful service that drives it are almost always two gates.
- A gate is one reviewable topic, not one file and not one commit-sized slice of a topic.
- Gate boundary = stop, hand over a review packet, wait. No work on gate N+1 before gate N is accepted.
- Accepted gate may still be revised later; revision is a new gate, not a reopen.
- **Code written before its spec is approved is discarded, not reshaped.** Gate 1 starts from the base branch. (2026-09-25.)
- **Full tier without OpenSpec tooling uses the `/spec` contract file.** `docs/specs/<slug>.md` carries the full-tier content and gate plan. (2026-09-25.)

## Boy Scout rule

- Leave every file the gate touches cleaner than it was found — the stale name, the dead branch, the comment that lies, the duplicated constant.
- **Bounded by the code the gate already changes.** Mess inside it is fixed and ships with the gate. Mess outside it is logged `noted, not done` and never widens the diff.
- Cleanup is behaviour-preserving. A tidy-up that changes semantics is a separate topic and a separate gate.
- Mess larger than the change carrying it, or load-bearing in a way that isn't obvious → stop and say so. A rewrite wearing a cleanup's clothes is a spec defect.
- The review packet names every Boy Scout fix separately from the specced change, so review can read them apart.
- Applies to these convention files: a rule this gate proved wrong gets corrected in this gate, not deferred to retro.

## Clean code — modern reading, not ceremony

- Clarity and small honest units. Not class-per-noun, not one-method-per-line decomposition.
- A clear 10-line function beats a "clean" 5-class hierarchy. Over-abstraction is as much a defect as under-abstraction.
- No interface, port, or extension point without a second implementation or a real test seam **today**.
- **YAGNI** — build exactly what the spec asks. No config flags, hooks, or generality for a future that is not specced.
- **KISS** — the simplest construction that satisfies the spec wins. Extra complexity needs a stated reason in the spec, not a comment defending it afterwards.
- Dead code, unused parameters, and orphaned abstractions get deleted in the gate that touches them, never left "just in case". Revealed but untouched → `noted, not done` (see Boy Scout rule).
- Use the existing shared primitive instead of re-implementing it locally — never hand-roll a timer, poller, retry, or cache that already exists.
- Cross-cutting utilities are designed domain-agnostic from the start, not built for the first domain and generalised later.
- Wiring a new pattern in covers every consuming call site in one pass — no follow-up PRs unless asked.

## Comments

- **Before writing a comment, name what it tells a reader that the code does not.** No answer means no
  comment. Run this per comment while writing it, not as a review pass afterwards — the reflex to
  narrate fires first, and a comment that survives to review usually survives review.
- **One line is the default.** A second line earns its place only by carrying a second fact. A comment
  longer than the code it sits above is a defect however true it is.
- Cut every clause restating an identifier already on screen. A comment opening with its own function's
  name in prose ("Whether X is Y", "Adds a Z") has said nothing yet — start where the reader cannot see.
- `@param` / `@returns` naming a parameter and its type is pure restatement. Drop them. Where a
  parameter carries a constraint the type cannot express — a unit, a range, a required ordering — state
  the constraint alone.
- Needing a comment to explain **what** code does is a signal the code is too complex. Simplify, rename, or extract first; comment only if that fails.
- What survives is the **why** the code cannot express: a protocol or firmware quirk, a load-bearing ordering, a constraint from outside the system.
- Never longer than the code it explains. No doc comment restating a signature or parameter name. No narration of guards, defaults, or control flow.
- Documentation of a non-obvious mechanism belongs in the spec or an architecture note, not in a comment block above the function.
- Reusable library surface may carry doc comments; application code does not.
- No comments describing what a change did — that is the commit message. **The worst form is the one
  written in the past tense about the old behaviour** ("this used to X, which is why Y"): it explains
  a state the reader cannot see, and it is dead the moment the next change lands. A reader who wants
  the history has `git log`; write the sentence there instead, where it is true forever and costs the
  next reader nothing.
- **One fact, one place.** The same explanation in a constant's comment, the function's header and the
  policy block above it is slop even when each instance is true — three copies drift, and the reader
  who finds the second one wonders what it adds. Put it where someone looking for that fact will land,
  and delete the restatements.
- **A comment states the constraint, never argues for it.** No aphorisms, no rhetorical flourish, no sentence that would read as a slogan. If it sounds quotable, delete it.
- No class or module doc block narrating what the type does **not** do. An absent behaviour is absent from the code; describing it dates instantly and reads as an argument with a reader who is not there.
- Applies to tests. A test name is its description.

## Code-level style

- Guard clauses and early return. Never nested-if pyramids.
- Magic numbers become named constants with the unit in the name (`STABILITY_THRESHOLD_DEG`), declared next to the logic that uses them.
- Full words in names. No abbreviations beyond `id`, `url`, `ui`, `api`, `db`.
- Model flows as explicit state machines (`Guide → Idle → Measuring → Result`), never as combinations of boolean flags.
- No truthiness, no loose equality. Compare explicitly (`x == null`, not `!x`) — `0` and `""` are values, not absence.
- Static typing is non-negotiable. A type system bolted on after the fact is unsafe theatre, not a guarantee.

## Design system

- The visual identity every app follows — colour roles, type, shape, spacing, components — is [design.md](design.md). Only the colours change per app.
- No hardcoded visual values in components. Typography and colour come from the design system, derived via copy/override (Flutter: `TextTheme.of` / `ColorScheme.of` + `.copyWith()`; Compose: `MaterialTheme.typography` / `colorScheme`).
- No design-system entry fits → ask before adding one. Never inline a one-off value.
- Semantic states stay visually distinct: warning ≠ error ≠ info. Two different problems never render identically on one screen.
- A shared component that hardcodes a semantic value gains an optional override parameter — never a fork of the component.

## Build and tooling

- CI: when a script needs a richer shell, fix the invocation — don't downgrade the script to the lowest common denominator.
- A mandated process doc (e.g. a security audit note) is skipped when the thing it documents carries no real risk. Confirm that first; don't assume it.

## Definition of done

- **A green local run hands a gate over.** The review packet needs the suite, the typecheck and the linter green locally, and nothing more.
- **CI blocks the merge, not the review.** A red runner is fixed before merge; it does not hold up a review packet.
- **Hardware verification is the same: a merge gate, never a hand-over gate.** Code that can only be exercised on the target device is reviewed on local green and ships its device check as a named `noted, not done` entry that the merge must clear.

## Feature flags and rollout

- **Flag what writes, not what watches.** A change that only observes and reports — detection, telemetry, a new fault surface — ships unflagged and always on. A change that issues a command to hardware or a third-party system gets its own flag, opt-in (default off), so enabling it is a deliberate act.
- A kill-switch flag (default on, `X=false` disables) is for behaviour that is already the norm and needs an escape hatch. It is not the right shape for a new control path.
- **Automated recovery needs a spend limit.** Anything that reacts to a bad state by acting on hardware caps its attempts over a rolling window and then surfaces the problem instead. An uncapped retry converts a visible fault into an invisible loop.
- A recovery attempt budget resets on evidence of real recovery (the thing works again), never on the error condition merely clearing.
