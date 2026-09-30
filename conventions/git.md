# Git

## Rules

- Commit scope is approved by the user before the commit is written.
- Never commit or push unless asked.
- Secrets never enter a commit, a prompt, or a log.
- No AI attribution or co-author trailers.
- Prefer one complete PR over an incremental series, unless incremental was asked for.
- A project repo's `CLAUDE.md` is never modified.

## TODO — answer on first hit

- Commit message format per repo. Deliberately not codified from observed history: personal repos use lowercase prose without a Conventional Commits prefix, work repos may differ.
- Squash vs merge, and who writes the MR description.
- Whether specs in `docs/specs/` are committed with the change or in a preceding commit.

## Gated work

- **Commit immediately after a gate is approved.** One commit per gate, scoped to that gate's paths.
  The working tree then shows only the gate in progress, so the next review packet's diff is the
  gate and nothing else.
- Never sweep unrelated working-tree changes into a gate commit. Name them and leave them.
