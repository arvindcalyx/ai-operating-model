# DESIGN — the architecture of ai-operating-model

This is the product spec: the model, the layering, the skill contracts, and the extension points. The
full behavioral rules live in `operating-model.md`; this doc is the architecture around them.

## 1. The core model — a session state machine with one gated writer

A session is a walk through states; each skill is a transition.

```
IDLE ──session-start──▶ ORIENTED ──(work)──▶ ENGINEERING_COMPLETE (STOP)
                                                    │
                                     [human: MERGE + VALIDATE]   ← the gate
                                                    ▼
IDLE (next) ◀──session-wrap── WRAPPED ◀────────────┘
```

Three hard-won properties become structural consequences of this shape rather than rules to remember:
- **Load order** is the transition order — you cannot wrap before you start.
- **"The record stays behind reality"** is the single edge that crosses the human gate.
- **The single gated writer** is: only the `WRAPPED` transition mutates durable state.

### The named invariant

> **Single Gated Writer.** Exactly one transition (`session-wrap`) mutates durable project state, and it
> may fire only after a human merges and validates. Every other skill is read-only.

This structurally guarantees the durable record never runs ahead of validated reality, and it makes the
engine portable: any agent may implement the read-only skills freely; only the one writer needs the gate
wired up.

## 2. The three layers

| Layer | What | Where it lives | Lifecycle |
|---|---|---|---|
| **Engine** | behavior — the skill/transition contracts | `skills/` (this repo) | versioned; `MODEL_VERSION` |
| **State schema** | the shape of project state | `templates/` (this repo) | co-evolves with the engine |
| **State instance** | the filled-in doctrine, handoff, ADRs, maps | the **consuming project's** repo | the project's own cadence |

The behavior/state separation you want is delivered by the instance living in the consuming repo. Engine
and state-schema are coupled by design (a template's shape is dictated by a skill's writes), so they ship
together in one repo and version together.

## 3. Skill contracts (summary; full text in each `skills/*/SKILL.md`)

| Skill | Reads | Writes | Authority |
|---|---|---|---|
| `operating-model` (router) | `CLAUDE.md` | none | orchestration only |
| `session-start` | Handoff ENTRY, Constraints_Card, intent index | none | read-only; ends by waiting |
| `review` | Doctrine (on trigger), Constraints_Card, Watchpoints, design doc | none durable (→ PR body) | may block, may not merge |
| `session-wrap` | active Handoff, trigger table | **archive + roll Handoff; conditional ADR/map/doctrine/audit edits** | **the only durable writer, behind the gate** |

Each contract is stated as: purpose · inputs · preconditions · reads · writes · outputs · authority.

## 4. Extension points (designed-for, deferred until evidence — see GOVERNANCE.md)

- `adapters/<agent>/` — map neutral skills to a runtime (Claude Code `.claude/skills`, Cursor rules, a
  plain system prompt). Build on first real use of a second agent.
- `agents/` — reviewer personas (validator, blind-reviewer, hostile-reviewer). Build when spawned repeatedly.
- new `skills/` transitions (e.g. `consolidation`, `design-review`) — only via GOVERNANCE.
- a reusable **watchpoint library** — accumulate shapes across projects.
- `MIGRATIONS.md` — appears with the first major version that changes a template's shape.
- tooling / CLI / installer — deferred hard.

## 5. Why this shape (rationale, condensed)

- **One repo, not two:** engine↔templates are coupled; splitting them creates cross-repo coordination for
  inherently joint changes. `MODEL_VERSION` gives independent versioning without the split. The `skills/`
  vs `templates/` seam keeps a future split a `git mv` away.
- **Contracts over prose:** stating each skill as inputs/reads/writes/authority makes the engine testable,
  composable, and portable, and it surfaces the single-gated-writer property as a fact, not a hope.
- **Minimal v1:** everything in §4 is deferred. A coherent small v1 beats a framework that hasn't earned
  its complexity — the same discipline the model teaches, applied to the model's own repo.
