# agent-skills

Finn's Claude Code toolkit. Working rules for any session in this repo — and the reference
copy of how he expects work to run in every other repo.

## Non-negotiable

- **Never assume.** Unknown → look it up (order below). Unanswerable → stop and ask.
  "I went with X for now" is a defect.
- **Multiple viable options → he chooses.** Present them with a recommendation first and
  the consequence of each. Do not pick silently.
- **Questions have no deadline.** Never time out a question, never proceed because an
  answer is slow.
- **Review is his, fully.** Never substitute a machine judgement for it.
- **Secrets never** enter code, logs, commits, or prompts. A pasted credential gets flagged
  for rotation, not used.
- **Other repos' `CLAUDE.md` files are read-only.** They are authored by Finn and his team.
  Read them for context; new rules go to that repo's `docs/conventions.md` or to
  `$FLOW_CONVENTIONS`. This file is the one exception — it describes this repo.

## Lookup order for any unknown

1. target repo `docs/conventions.md`
2. target repo `CLAUDE.md`
3. `$FLOW_CONVENTIONS` — default `~/agent-skills/conventions/`
4. established pattern in the surrounding code

Survives all four → ask once, batched, then **write the answer back** to the right
conventions file. The question count is supposed to decay.

## The loop

`analyse → spec → implement (gated) → he reviews → retro`

- **analyse** — `/second-opinion` when his own theory is withheld; `/troubleshoot` for a
  plain error. Every claim cites `file:line`, a command, or a log line.
- **spec** — `/spec`. Tier micro / standard (`docs/specs/<slug>.md`, committed) / full
  (`/openspec-plan`). Must state exact scope, out-of-scope, topic gates, and model+effort
  per gate. Ends at "Scope locked?" — never rolls into implementation.
- **implement** — `/implement`, one gate, then stop. Adjacent findings are logged as
  `noted, not done`, never added to the diff.
- **review** — `/review-packet`: reading order, spec conformance, deviations, real
  verification output. No quality verdicts.
- **retro** — `/retro`: friction becomes a convention rule, a memory file, a skill edit, or
  a backlog line. Nothing else.

## Gates

Split by **topic**, never by file count. Triggers: >~400 lines expected · more than one
topic · a layer boundary crossed · a risky change isolable from mechanical work. Risky and
architectural topics first. Work stops dead at a boundary until he accepts.

## Repo conventions

- Skills live in `<plugin>/skills/<name>/SKILL.md`; every plugin has
  `<plugin>/.claude-plugin/plugin.json` and an entry in `.claude-plugin/marketplace.json`.
- His own work is authored `alpenraum` / `https://github.com/alpenraum/agent-skills`.
  Inherited fork plugins keep their original authorship.
- Adding or removing a skill means updating: the plugin version, the marketplace entry, and
  the plugin table in `README.md`.
- Development uses symlinks from `~/.claude/skills/<name>` into this repo — edits are live,
  no reinstall.

## Comments

Explain the non-obvious **why** only — a load-bearing constraint, a protocol or firmware
quirk, why an ordering matters. Never restate the code, never exceed the code's length, no
doc comments echoing a signature, no narration of a change (that is the commit message).
Applies to tests.

## Git

Commit or push only when asked, with scope approved first. No AI attribution or co-author
tags.
