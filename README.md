# ai-operating-model

A portable, installable operating model for building real software with AI — without letting the AI
corrupt your code or your decisions. Drop it into any project, on any agent, and every session runs the
same disciplined way: doctrine-first, tiered context, independently reviewed, and committed only after a
human validates.

This is the reusable **engine** extracted from a production AI product built over 100+ sessions. It is
packaged here, not simplified.

## The one property that defines it

> **Single Gated Writer.** Exactly one skill — `session-wrap` — mutates durable project state, and it
> may fire only after a human has merged and validated the work. Every other skill is read-only.

That is what structurally guarantees the durable record never runs ahead of validated reality, and it is
what makes the engine safe to port: any agent may run the read-only skills freely; only the one writer
needs the human gate wired up.

## The model: a session state machine

A session is a walk through states; each skill is a transition.

```
IDLE ──session-start──▶ ORIENTED ──(work)──▶ ENGINEERING_COMPLETE (STOP)
                                                      │
                                       [human: MERGE + VALIDATE]   ← the gate
                                                      ▼
IDLE (next) ◀──session-wrap── WRAPPED ◀──────────────┘
```

Load order, "the record stays behind reality," and the single writer all fall out of this shape instead
of being rules you must remember.

## Two layers: engine vs. state

- **The engine — `skills/` — reuse as-is.** Behavior. Never edited by a consuming project.
- **The state schema — `templates/` — copied out once, then owned by your project.** Your project's
  doctrine, constraints, handoff, decisions log, and maps. The filled-in *instance* lives in your
  project's repo, never here.

## File map

```
README.md            this
MODEL_VERSION        engine version; a consuming project pins to it
GOVERNANCE.md        evidence-first rule for how THIS repo is allowed to grow
docs/
  DESIGN.md          the architecture: state machine, skill contracts, invariants
  operating-model.md the full portable manual (the source of truth)
  getting-started.md instantiate this model in a new project (5 steps)
skills/              THE ENGINE (portable SKILL.md contracts)
  operating-model/   meta-skill: entry point + load-order router
  session-start/     transition IDLE → ORIENTED            (read-only)
  review/            transition WORKING → ENG-COMPLETE      (read-only; spawns reviewers)
  session-wrap/      transition VALIDATED → WRAPPED         (the single gated writer)
templates/           STATE SCHEMA (copy into your project, then fill in)
  CLAUDE.md  DOCTRINE.md  CONSTRAINTS_CARD.md  DECISIONS_LOG.md
  ACTIVE_ARCHITECTURE.md  ACTIVE_RUNTIME_MAP.md  HANDOFF_Session_001_Active.md  WATCHPOINTS.md
```

## Install / use

See `docs/getting-started.md`. In short: copy `templates/` into your project, write your `DOCTRINE.md`
first, install the skills for your agent, and start with `Initiating S1 Design`.

## Philosophy (non-negotiable)

- The operating model is the source of truth. This repo **packages** it; it never simplifies it.
- Start minimal. A coherent v1 beats a comprehensive framework that hasn't earned its complexity.
- Evidence-first growth. A new capability is added only after real use proves the current model
  insufficient. See `GOVERNANCE.md`.

## Status

v1. Deliberately small. Extension points (agent adapters, reviewer personas, additional skills, a
watchpoint library) are named in `docs/DESIGN.md` §4 and deferred until evidence justifies them.
