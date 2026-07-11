---
name: session-start
description: >
  Orient a session on the minimum necessary context (Tier-0), emit one orientation line, and wait.
  Use immediately after `operating-model` routes the session. Read-only.
---

# session-start (transition: IDLE → ORIENTED)

## Overview
Load a thin, intent-specific Tier-0 set (~10K tokens), not the whole corpus. Everything else is pulled
on demand during the session. This is the budget discipline that keeps sessions cheap and consistent.

## When to use
Once per session, right after the `operating-model` router.

## Process
1. **Universal core (every intent, always):** the active `HANDOFF_Session<N>_Active.md` **ENTRY** +
   `CONSTRAINTS_CARD.md`.
2. **Add the intent's thin index:**
   | Intent | Adds |
   |---|---|
   | Bug | `ACTIVE_RUNTIME_MAP` route index + shared primitives |
   | Investigation | `ACTIVE_RUNTIME_MAP` + `ACTIVE_ARCHITECTURE` subsystem map |
   | Design | `ACTIVE_ARCHITECTURE` subsystem map (+ the review skill's risk card) |
   | Implementation | `ACTIVE_RUNTIME_MAP` + `ACTIVE_ARCHITECTURE` (+ risk card) |
3. **Startup safeguard:** check for an open PR from the previous session. If one exists, it likely
   STOPped awaiting merge + validation — surface it and ask: resume (validate → wrap) or start new?
4. **Emit one orientation line** and stop:
   `S<N> <Intent> · scope: <from ENTRY> · loaded: <docs> · ~<N>K tokens` — then **wait for direction.**

## Contract
- **Purpose:** orient on minimum context.
- **Inputs:** intent, session number N.
- **Preconditions:** exactly one active Handoff exists; open-PR check performed.
- **Reads:** Handoff ENTRY, Constraints_Card, + the intent index (maps).
- **Writes:** none (read-only).
- **Outputs:** orientation line + loaded context; the open-PR safeguard prompt if a PR is open.
- **Authority:** read-only. May not begin work; ends by waiting for direction.

## Loading law
Load the smallest *file* that holds what you need; pointer-first, never scan. For small one-screen docs,
read the whole file. Achieve sub-file targeting by **splitting the file, never by line numbers.** Never
read the full doctrine, full design docs, per-feature runtime blocks, or watchpoints at start — those are
pulled on trigger.

## Red flags
- Reading the full doctrine or a design doc "to be safe" at start (over-prefetch → flag it at wrap).
- Proposing work before the orientation line and before the human gives direction.
- Two active handoffs at `docs/` root (an error state — resolve before continuing).
