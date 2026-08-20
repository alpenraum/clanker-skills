# 📦 Install

Two ways in, and they are not interchangeable.

| | Plugin install | Symlink |
|---|---|---|
| For | using a plugin | editing a skill |
| Edits are live | no — snapshot copy | yes |
| Invoked as | `/dstoic:pick-model` | `/pick-model` |
| Scope | whole plugin, all skills | one skill |
| Survives a new machine | yes, reproducible | no, manual |

Never do both for the same plugin — the skill appears twice under two names.

## 🏪 Plugin install

```bash
claude plugin marketplace add ~/agent-skills          # local checkout
claude plugin install dstoic@alpenraum-marketplace
```

Restart the session afterwards. From a clean machine, point the marketplace at the remote
instead:

```bash
claude plugin marketplace add https://github.com/alpenraum/agent-skills
```

Plugin names: `flow` · `review` · `dstoic` · `cognitive` · `openspec` · `toolsmith` ·
`retrospect` · `convert` · `content` · `cowork` · `gtd` · `biz` · `experimental` ·
`philosopher` · `coach` · `lazy`.

All at once — costs context in every session, see below:

```bash
for p in flow review dstoic cognitive openspec toolsmith retrospect convert \
         content cowork gtd biz experimental philosopher coach lazy; do
  claude plugin install "$p@alpenraum-marketplace"
done
```

### The install is a copy, not a link

`claude plugin install` snapshots the plugin into

```
~/.claude/plugins/cache/alpenraum-marketplace/<plugin>/<version>/
```

pinned to the plugin version and the git commit SHA. Editing the repo does **not** change
an installed plugin, and the cache directory is keyed on version — so refreshing means
bumping `version` in `<plugin>/.claude-plugin/plugin.json` and the marketplace entry, then:

```bash
claude plugin update dstoic@alpenraum-marketplace   # restart required
```

That is the reason the dev path below exists.

## 🔗 Symlink (dev path)

One skill, live, bare slash name:

```bash
ln -s ~/agent-skills/flow/skills/spec ~/.claude/skills/spec
```

Whole plugin:

```bash
for d in ~/agent-skills/flow/skills/*/; do
  ln -s "$d" ~/.claude/skills/"$(basename "$d")"
done
```

Currently symlinked: `flow` (5) and `review` (3) — Finn's own, the ones under active edit.
Everything else is inherited and belongs on the plugin path.

## 💰 Context cost

```bash
claude plugin details dstoic@alpenraum-marketplace
```

Prints an always-on figure charged to every session plus per-skill on-invoke cost. `dstoic`
is ~700 always-on for 8 skills. `philosopher` carries 24 — check before installing it.

## 🪝 dstoic hooks

`dstoic` ships 5 hooks (SessionStart, PreToolUse, PermissionRequest, Stop, SessionEnd) for
tmux notifications, a `PRAXIS_DIR` guard, and output dumps. All of them no-op unless both
`DSTOIC_HOOKS_ENABLED=1` and `PRAXIS_DIR` are set, so installing the plugin changes nothing
until you opt in. Hooks cost no model context.

## 🔍 Skill not found

`/some-skill` autocompletes nothing → work down this list:

1. `claude plugin list` — is the owning plugin installed and enabled?
2. `ls ~/.claude/skills/` — is it symlinked, and is the link intact?
3. Installed plugin skills are namespaced — try `/<plugin>:<skill>`.
4. Restart the session. A fresh install or `plugin update` needs one.
5. `grep disable-model-invocation <plugin>/skills/<name>/SKILL.md` — if true, the skill is
   deliberately hidden from the model's skill list and only reachable by typing the slash
   command. `tech-debt-audit` is one.

## 🧹 Remove

```bash
claude plugin disable dstoic@alpenraum-marketplace     # keep, stop loading
claude plugin uninstall dstoic@alpenraum-marketplace
claude plugin marketplace remove alpenraum-marketplace
rm ~/.claude/skills/spec                               # symlink only, never -r
```
