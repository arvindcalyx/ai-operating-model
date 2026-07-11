---
name: review
description: >
  Independently validate a change before it ships: classify risk by touched paths, then run blind and
  hostile review with agents that are NOT the implementer. Use when code reaches Engineering Complete,
  or when a design artifact needs validation. Read-only (findings held in the PR body until wrap).
---

# review (transition: WORKING → ENGINEERING_COMPLETE)

## Overview
The implementer cannot review itself — once a framing is set in a session it defends that framing. This
skill brings in a fresh reader to catch what the implementer structurally cannot see. It mirrors the
design-level chain (advisor → independent validator → decider) at the diff level.

## When to use
When a code change reaches Engineering Complete, or after any new design artifact / doctrine edit / ADR.

## Process
1. **Classify risk by TOUCHED CODE PATHS, not stated intent.** "Just cosmetic / tiny / one-liner" is
   irrelevant; what the change *touches* sets the tier. Risk **escalates, never silently demotes**, and
   the implementer never self-grants a lower tier.

   | Tier | Examples | Required before merge |
   |---|---|---|
   | **Low** | presentation / copy, *and nothing in the trigger list* | targeted verification, re-run by a non-implementer |
   | **Medium** | behavior, no doctrine-critical path | + independent verification + full suite + **blind + hostile review** |
   | **High** | commit paths, auth, persistence, authority/truth contracts, deletion, logging | + **blind + hostile review on the diff before push** |

   Any diff touching a watchpointed file, a recently-shipped engine, or a save/auth/persistence/async-
   mutation path is **Medium minimum**, even when the visible intent is cosmetic.
2. **Blind review** — an independent agent cold-reads the final diff with no plan or session history;
   reports observed structure first, then concerns.
3. **Hostile review** — an independent agent adversarially probes the diff for edge cases, contract
   violations, regression vectors, and unstated assumptions.
4. Apply real fixes for majors. Every bug fix lands a regression/sentinel test that fails without the fix.
5. Record findings + fixes + accepted risks **in the PR body** (they transcribe into the closing handoff
   at wrap, which may be a later session).

## Contract
- **Purpose:** validate a change independently before it ships.
- **Inputs:** the final diff (or design artifact), the touched paths.
- **Preconditions:** Engineering Complete; **reviewer ≠ implementer.**
- **Reads:** Doctrine (on constraint trigger), Constraints_Card, Watchpoints (if a watched path is
  touched), the one relevant design doc.
- **Writes:** none durable (findings → PR body). Spawns independent reviewer agents.
- **Outputs:** risk tier + blind report + hostile report + findings + accepted risks.
- **Authority:** may block or flag; may **not** merge. Risk escalates, never self-demotes.

## Red flags
- The implementing agent reviewing its own diff (does not satisfy the ritual — blind spots are baked in).
- Grading a change Low because the *intent* was cosmetic while it touches a guarded path.
- Reviewers each re-running the whole suite (waste — one verification pass; reviewers read the diff).

## Efficiency
Efficiency comes from **not duplicating expensive work**, never from cutting reviews. One verification
pass; heavy reading stays in the reviewer agents' contexts.
