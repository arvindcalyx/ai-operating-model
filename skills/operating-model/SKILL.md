---
name: operating-model
description: >
  Entry point for a session run under this operating model. Enforces the load order and routes by
  intent. Use when starting ANY session on a project that has instantiated this model — before doing
  anything else.
---

# operating-model (meta-skill / router)

## Overview
This is the front door. It does not do work; it makes sure the session starts the disciplined way and
that the skills run in the right order. The operating model is a session **state machine** — this skill
owns the transition order.

```
IDLE ──session-start──▶ ORIENTED ──(work)──▶ ENGINEERING_COMPLETE (STOP)
                                                    │
                                     [human: MERGE + VALIDATE]
                                                    ▼
IDLE (next) ◀──session-wrap── WRAPPED ◀────────────┘
```

## When to use
At the very start of every session, triggered by the line: `Initiating S<N> <Intent>`
(Intent ∈ Bug | Investigation | Design | Implementation). Intent is a **prefetch key, not a workflow** —
execution, review, and wrap rules are identical for every intent; the outcome is classified at wrap,
never assumed at start.

## Process
1. Read `CLAUDE.md` (the project pointer).
2. Run `session-start` (loads Tier-0 by intent, emits the orientation line, performs the open-PR check).
3. Wait for direction. Do work pull-based (fetch the smallest slice on trigger; never scan whole docs).
4. When code reaches Engineering Complete, run `review`. Then **STOP** — create the PR, document nothing.
5. After the human merges and validates, run `session-wrap`.

## Contract
- **Purpose:** enforce load order and route by intent.
- **Inputs:** `Initiating S<N> <Intent>`.
- **Preconditions:** the project has the state files (`docs/` + `CLAUDE.md`).
- **Reads:** `CLAUDE.md`.
- **Writes:** none.
- **Outputs:** delegates to `session-start`; sequences the other skills.
- **Authority:** orchestration only. Decides nothing; owns no durable writes.

## Red flags (stop if you see these)
- Starting work before `session-start` emitted its orientation line.
- Documenting / ADR'ing / editing maps *before* merge + validation (that is `session-wrap`'s job, and
  only after the gate).
- Treating the declared intent as a fixed workflow (a Bug session may end in a design doc).

## Authority chain
`DOCTRINE` > `docs/operating-model.md` > `CLAUDE.md`. If a change conflicts with doctrine, the change is
wrong, not the doctrine.
