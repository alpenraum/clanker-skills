# Pick Model — Extended Reference

Model facts verified 2026-08-20 against the bundled `claude-api` skill. Companion to SKILL.md.

**Effort default is `high` on every model that supports the param** — omitting `effort` equals
`high`. `xhigh` is the best setting for most coding/agentic work on Opus 5 / Opus 4.8 / Sonnet 5 /
Fable 5, and is Claude Code's own default.

## Model Characteristics

### Haiku 4.5 (`claude-haiku-4-5`)
- **$/M**: $1 in · $5 out — cheapest
- **Speed**: fastest (~2-3× Sonnet)
- **Context**: 200K — the only current model under 1M
- **Effort**: none — passing `effort` errors. Thinking needs the legacy `budget_tokens` form
- **Best for**: deterministic, pattern-based, low-reasoning — convert, transcribe, format, extract, regex, typo, status query
- **Limits**: ambiguity, multi-step reasoning, creative nuance

### Sonnet 5 (`claude-sonnet-5`)
- **$/M**: $3 in · $15 out ($2/$10 intro thru 2026-08-31)
- **Speed**: baseline; Anthropic's "most agentic Sonnet yet"; Free/Pro default
- **Context**: 1M
- **Effort**: `low | medium | high | xhigh | max` — first Sonnet with `xhigh`/`max`. Default `high`
- **No mid-conversation system messages** — Opus 5 / 4.8 / Fable 5 accept a `{"role": "system"}` entry in `messages[]`; Sonnet 5 does not
- **Best for**: plumbing (GTD, context, commit, library), single-file coding, bug fix, code review, content, research summaries, agentic tool use, **light multi-file** work
- **Positioning**: "big Sonnet that occasionally reaches Opus range," NOT a mini-Opus. Ties/edges Opus on tool-augmented & knowledge work (HLE-w/tools 57.4 vs 57.9; GDPval 1618 vs 1615; Terminal-Bench 80.4 vs 74.6); Opus lead WIDENS as tasks deepen (SWE-bench Verified 85.2 vs 88.6 → Pro 63.2 vs 69.2) and on UNAIDED reasoning (HLE no-tools 43.2 vs 49.8)
- **Limits / carve-outs**:
  - **Judgment/strategy**: below Opus on trade-off-heavy, unaided reasoning → escalate.
  - **Hard multi-file refactor** (deep repo): Opus edge grows → escalate.
  - 🔒 **Security/audit**: deliberately crippled cyber capability (CyberGym 65%→53%, 0% working Firefox exploits) + MORE false refusals on legit security work (legitimacy 97.33%→91.55%). Anthropic itself recommends Opus for cybersecurity. **Never route security/audit to Sonnet 5.**
  - 💸 **Effort spend**: `max` on Sonnet can approach Opus cost for less capability. Prefer escalating the model over cranking Sonnet to `max`. (`xhigh` on Sonnet 5 is fine — it's a recommended setting for agentic work, not a cost trap.)

### Opus 5 (`claude-opus-5`) — the thinking tier
- **$/M**: $5 in · $25 out · Fast Mode $10 / $50 (Opus 5 + 4.8 only, Claude API only)
- **Context**: **1M**
- **Effort**: `low | medium | high | xhigh | max` — all five; default `high`
- **Thinking**: adaptive **by default** (unlike Opus 4.8/4.7, which run thinking-off when the param is omitted). `disabled` is accepted only at effort ≤ `high` and has two failure modes (tool calls written into visible text; `<thinking>` tag leakage) → **drop effort to `low`/`medium` instead of disabling**
- **Best for**: strategy, architecture, multi-file refactor, security audit, framing ambiguous tasks
- **Limits**: overkill for chores/plumbing; excluded from Priority Tier

### Opus 4.8 (`claude-opus-4-8`)
- Same price and 1M context as Opus 5; effort `low`–`max`, default `high`
- **Omitting `thinking` runs it WITHOUT thinking** — must set `{type: "adaptive"}` explicitly
- Kept here only for migration reference; route new work to Opus 5

### Fable 5 (`claude-fable-5`) — boulder tier
- **$/M**: $10 in · $50 out — most expensive
- **Context**: 1M (max output 128K)
- **Effort**: `low | medium | high | xhigh | max` — full range, same as Opus 5
- **Thinking**: always on. Omit the param; `{type: "disabled"}` returns 400. Effort is the depth dial
- **Best for**: ambitious, long-running, asynchronous, highly multi-step or sustained-ambiguity work
- **Limits**: cost; requires 30-day data retention (unavailable under ZDR); single turns can run many minutes; do not use as an effort dial — pick it for task *shape*

## The Two Levers

| Lever | Cache effect | Bar to recommend |
|---|---|---|
| 🎚️ Effort change (same model) | survives | **low** — judge on quality delta only |
| 🔀 Model switch | breaks (re-read context uncached) | **higher** — needs a real capability gap |

Both levers reach the target → take 🎚️. A recognized chore on Opus `high` is a
downgrade candidate, but dropping the effort to `low` gets most of the saving with no
re-cache at all.

## Routing Table (full)

| Task recognition | Model | Effort | Notes |
|---|---|---|---|
| **Chores** — convert, transcribe, format, extract, regex, typo, lookup, template fill | 🟢 Haiku 4.5 | n/a | Deterministic |
| **Plumbing** — GTD, context save/load, commit, library wiring, serialization | 🟡 Sonnet 5 | `low`–`medium` | Standard workflows |
| **Standard coding/content** — single-file fix, code review, blog/email, research summary, known-pattern API, agentic tool use, light multi-file | 🟡 Sonnet 5 | `medium`–`high` | Moderate reasoning; can absorb some ex-Opus work |
| **Thinking** — strategy, fiscal, hard multi-file refactor (3+), architecture, **security/audit**, cognitive skills, long-form (>2K words), unaided reasoning | 🔴 Opus 5 | `high`, sweep `xhigh` | Trade-offs, nuance; security is a hard carve-out |
| **Ambiguous / big / can't-classify** | 🔴 Opus 5 | `high` to frame & route | Drop tier once scope clears |
| **Boulder** — multi-day, highly multi-step, sustained ambiguity | 🟣 Fable 5 | `high`–`xhigh` | Above-Opus capability. Pick for task *shape* — its 1M window is no longer a differentiator |

### Escalators (apply, cap +1 tier)

- **Ambiguity** (underspecified, multiple interpretations) · **Scope** (3+ files/systems) · **Stakes** (prod, security, data-loss, regulatory) · **Novelty** (no established pattern) → +1
- **Multi-stakeholder / strategic / political / cross-functional** (business) → +1
- **Pattern detection / bias ID / ethical reasoning / multi-framework** (cognitive) → +1
- **Context > 200K needed** → anything except Haiku 4.5 (Opus 5, Sonnet 5 and Fable 5 are all 1M)
- Tie Haiku↔Sonnet: needs *any* judgment? → Sonnet. Sonnet↔Opus: trade-offs to balance? → Opus.

## Verdict Examples

| Intended prompt | Current | Verdict |
|---|---|---|
| "fix typo in README" | Opus 5 high | ⬇️ Haiku (or 🎚️ drop to `low` — no re-cache) |
| "refactor auth across 15 files" | Sonnet 5 high | ⬆️ Opus `high` (scope+stakes) |
| "save session context" | Opus 5 high | ⬇️ Sonnet `low` (plumbing) |
| "design market-entry strategy" | Sonnet 5 high | 🔀 Opus `xhigh` (strategic, multi-framework) |
| "deep reasoning step, one-off" | Opus 5 high | 🎚️ `xhigh` for this step — cache survives, no switch |
| "audit + rewrite entire 400K-line repo" | Opus 5 | 🔀 Fable 5 (long-horizon autonomy) |
| "summarize this transcript" | Haiku 4.5 | ✅ Stay (chore, optimal) |

## Interaction Patterns

| Pattern | Strategy |
|---|---|
| **One-shot** | Match task complexity; lower effort cheaper |
| **Iterative** | Start lower tier/effort, escalate only if it fails |
| **Agentic loop** | Higher effort can REDUCE total cost (better planning, fewer correction turns) |
| **Exploratory/learning** | Sonnet medium — patient, cheap enough to iterate |
| **Production/high-stakes** | +1 tier for safety |
| **Long-horizon / sustained autonomy** | Fable 5 — capability, not window size |

## Signal Words

- **Haiku**: quick, simple, just, only, extract, format, rename, fix typo, convert, transcribe
- **Sonnet**: write, create, explain, review, analyze, debug, single file, summarize
- **Opus**: design, architect, complex, multiple files, refactor, migration, strategy, audit, nuanced, trade-offs
- **Fable**: end-to-end, autonomous, multi-day, whole repo, entire codebase, long-running, huge context
- **Effort-up cues**: "think hard", "be thorough", "deep", "carefully" → bump effort before switching model
- **Effort-down cues**: "quick", "rough", "draft", "just need" → drop effort

## Common Mistakes

- ❌ **Opus default**: using Opus `high` for typos/plumbing — 5-25× cost, often a downgrade or effort-drop is right.
- ❌ **Forgotten effort default**: every model defaults to `high` — silent cost creep; override for routine work.
- ❌ **Effort-as-input confusion**: effort is output spend; on loops higher can be cheaper overall.
- ❌ **Fable as effort dial**: Fable is a task-shape choice (long/ambiguous/huge-context), not "Opus but more".

## Override

Always respect explicit user model/effort selection. User knows best on budget,
speed, past-experience preferences. This skill advises; it never executes the prompt.
