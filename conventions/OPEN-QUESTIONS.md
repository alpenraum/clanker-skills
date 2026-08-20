# Open questions

Carried over from the triage. Each one becomes a rule or a fix when answered — `/retro`
picks these up.

## Decisions still needed

- **Definition of done** — does "done" require passing CI, or is a green local run enough to hand over for review?
- **Comment rule home** — the rule exists in three places: global `~/.claude/CLAUDE.md`, `conventions/general.md`, and 21energy-app memory `feedback_comment_brevity`. Pick one home, make the others pointers.
- **Security audit doc** — 21energy-app's own `CLAUDE.md` mandates `docs/security/audits/YYYY-MM-DD_*.md` for security-sensitive changes. Repo-local rule, or does it belong in `conventions/git.md`? (That `CLAUDE.md` is read-only either way.)

## Codebase questions raised while harvesting memory

- `TemperatureControllerV2` deviates from the 0–4 power-level domain scale (accepts and emits 1–5). Bug to fix, or an intentional new contract?
- Analytics opt-out exists in code (`setEnabled(false)`) but is wired to no UI toggle. Still intended?
- The Loki bearer token was hardcoded in `lib/services/loki_logger.dart` and confirmed non-productive. Was it actually removed?

## Rules deliberately not adopted

Left unmarked during triage — recorded so they don't get re-proposed:

- Commit message format from observed history (lowercase prose, no Conventional Commits).
- Kotlin formatting values from `.editorconfig` (4 spaces, 130 columns, trailing-comma policy).
- Wire-format ISO 8601 timestamps, underscore persistent keys, shared polling primitive guarantees, "report your own action" — remain project memory, not portable rules.
- Generated-code and toolchain-pin rules.
- Locale scope (EN + DE) and German informal voice.
- Personality and voice notes derived from the blog.
