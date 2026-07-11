---
name: session-wrap
description: >
  Commit the durable record after reality is validated: archive and roll the handoff, and touch only the
  docs whose trigger fired. THE SINGLE GATED WRITER — runs only after a human merges and validates a code
  session. Use at session close.
---

# session-wrap (transition: VALIDATED → WRAPPED) — the single gated writer

## Overview
This is the only skill with durable write authority, and it fires only behind the human gate. That is
what keeps the durable record **behind** validated reality, never ahead of it.

## When to use
- **Code session:** ONLY after the PR is merged AND the human validates. Before that, the session STOPs
  at Engineering Complete (PR open, nothing written). Validation fails → the same session fixes, updates
  the PR, re-validates.
- **Design-only / doctrine-only session:** no merge gate; wraps at session end (its gate was the review
  chain).

## Process
1. **Outcome classification** — what the session *actually* produced: {no change · code · design artifact ·
   ADR · doctrine edit · mixed}. This, not the declared intent, drives doc impact.
2. **Touch only triggered docs:**
   | Doc | Touch trigger |
   |---|---|
   | design artifact(s) | session created/changed design content (≤1 new doc) |
   | `DECISIONS_LOG` (ADR) | an architectural decision was made |
   | `ACTIVE_RUNTIME_MAP` | a call chain / route ownership / persistence-write path / major flow / test ownership changed (NOT for UI/copy/test-only) |
   | `ACTIVE_ARCHITECTURE` | new subsystem, or subsystem ownership materially changed |
   | `DOCTRINE` | validation surfaced a doctrine gap (rare; requires an ADR) |
   | `Audit` | a durable finding surfaced and isn't being worked next session |
   | **Handoff** | **always (unconditional)** |
3. **Handoff procedure (always):**
   a. **Archive** the active handoff verbatim → `docs/archive/Handoff_Session<N>_Closed.md`. Immutable.
   b. **Create** `HANDOFF_Session<N+1>_Active.md` from the archived base, in **ENTRY / `---` / HISTORY**
      shape. Transcribe the PR-body review findings into the HISTORY validation record.
   c. **Verify exactly one active handoff** exists at `docs/` root (two = error state).
4. **Carry-forwards & seeds:** roll open items forward (one-line reason each); review deferred-design
   seeds (still-relevant / implemented / rejected / superseded). Flag anything rolled 3+ sessions.
5. **OS review (3 questions, ≤2 items, zero is the expected default):** over-prefetch? under-coverage?
   startup overshoot? → integrate now if trivial, else one bullet in the Handoff OS backlog.
6. **Commit wrap docs** — after merge + validation. They do not ride the code PR.

## Contract
- **Purpose:** commit the durable record once reality is validated.
- **Inputs:** outcome class, the review findings, the merged+validated flag.
- **Preconditions (HARD GATE):** code session → PR merged AND human-validated. Else STOP; no writes.
- **Reads:** active Handoff, the trigger table.
- **Writes:** archive Handoff → create next; conditional ADR / runtime-map / arch-map / doctrine / audit /
  design edits; ≤2 OS items.
- **Outputs:** the next active Handoff (ENTRY/HISTORY).
- **Authority:** the ONLY skill with durable write authority, and only behind the human gate.

## Red flags
- Writing anything durable at Engineering Complete, before merge + validation (the exact failure this
  gate prevents).
- The archive copy and the new active handoff being the same file (the archival step must separate the
  immutable "what N inherited" from the operational "what N+1 inherits").
- Touching a doc because "it might be useful" (that is the sprawl pattern in miniature).
