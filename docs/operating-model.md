# The Operating Model — how to run an AI-advised software product

**What this is.** A product-agnostic operating model for building software with AI: doctrine-first,
doc-disciplined, AI-navigable, drift-resistant. Instantiate it with the `templates/`, replace every
domain noun with your own, and you get these ways of working from day one.

The whole thing rests on one idea: **the durable record is small, layered, and pointer-shaped, and it
never runs ahead of validated reality.** Everything below serves that.

---

## §0. The file set (the "doc family")

The model targets **≈8 active docs** at the repo's `docs/` root. Instantiate these from `templates/`:

| Doc | Owns | Shape |
|---|---|---|
| `DOCTRINE.md` | What the product **is / is not / refuses to do**; the non-negotiable constraints + invariants; success/failure criteria. The constitution. | Locked, versioned. Everything defers to it. |
| `operating-model.md` | The **operating model** — how sessions run, how wraps happen, the validation chain, doc discipline. (This file.) | Executable entry model + deep reference. |
| `CONSTRAINTS_CARD.md` | The **always-loaded** terse constraint surface — the only doctrine lines cited during execution, one screen. | Table of C1..Cn + invariants. Defers to Doctrine. |
| `ACTIVE_ARCHITECTURE.md` | **Static topology** — what lives where. Subsystem → location → canonical design doc. | Navigation index. No design content. |
| `ACTIVE_RUNTIME_MAP.md` | **Dynamic paths** — per feature, the call chain, the proving test, the non-obvious invariants. | Pointer doc. Route + function name, **never line numbers**. |
| `DECISIONS_LOG.md` | **ADRs only.** Architectural decisions, append-only. | Pointer + rationale, never a restated design. ≈25 lifetime. |
| `HANDOFF_Session<N>_Active.md` | **Operational truth the next session inherits.** Exactly one exists. | ENTRY (thin startup read) + `---` + HISTORY (lazy). |
| `<Module>_Design_v1.md` | One canonical design doc **per subsystem**, created during that subsystem's design phase, archived after it ships. | Owns the detail; ADRs and maps only point at it. |
| `Audit.md` *(on first durable finding)* | Durable audit-worthy findings not being worked next session. | Append-only. |

Plus `CLAUDE.md` at repo root: a **pointer, not a restatement.**

**Authority chain:** `DOCTRINE` > `operating-model.md` > `CLAUDE.md`. If a design conflicts with doctrine,
**the design is wrong, not the doctrine.** ADRs never override doctrine. Archive folder `docs/archive/` is
append-only, never edited, never deleted, and does not count against the ≈8-doc target.

---

## §1. The session lifecycle

Every session is one of four **intents** — `Bug | Investigation | Design | Implementation` — but intent is
a **prefetch key, not a workflow.** Execution, wrap, and review rules are identical for every intent; the
*outcome* is classified at wrap, never assumed at start.

```
Initiating S<N> <Intent>          ← one line in
   ↓  tiered startup read (§2)
Emit one orientation line, WAIT for direction
   ↓  pull-based execution (§3)
Implementation → Blind review → Hostile review → Verification
   ↓
ENGINEERING COMPLETE → Create PR → STOP        ← attention-bounded; pause here
   ↓  [human merges → human validates]         ← the gate
Wrap ritual (§5)                  ← runs ONLY after merge + validation, never before
```

**The STOP is load-bearing.** A code session reaches Engineering Complete (PR open, nothing documented)
and pauses; the wrap happens only after merge + validation. This keeps the durable record *behind* reality.
A design-only / doctrine-only session has no merge gate and wraps at session end.

**Design precedes implementation, within a session.** A session may combine design and implementation, but
decisions are made and recorded before code lands against them. **Implementation that reaches an unanswered
design question STOPS for an explicit decision — it does not decide inline.** "I'll just decide this small
thing inline" is the classic drift pattern; refuse it. A doctrine-only or design-only session is valid work.

**Startup safeguard:** before orienting to new work, check for an open PR from the previous session. If one
exists it probably STOPped awaiting merge + validation — surface it, ask resume-or-start-new.

---

## §2. Tiered startup reads (the budget discipline)

Don't read the whole corpus to start. Load a thin, intent-specific Tier-0 set (~10K tokens); pull the rest
on demand.

**Universal core (every intent, always):** the active **Handoff ENTRY** + **`CONSTRAINTS_CARD.md`**.

**Intent adds one thin index:**

| Intent | Adds |
|---|---|
| Bug | `ACTIVE_RUNTIME_MAP` route index + shared primitives |
| Investigation | `ACTIVE_RUNTIME_MAP` + `ACTIVE_ARCHITECTURE` subsystem map |
| Design | `ACTIVE_ARCHITECTURE` subsystem map + the Wrap/Risk card |
| Implementation | `ACTIVE_RUNTIME_MAP` + `ACTIVE_ARCHITECTURE` + Wrap/Risk card |

The **full Doctrine, full design docs, per-feature runtime blocks, and watchpoints are NEVER read at
start** — they are execution-triggered. Sanity check, not a tuning target: Tier-0 ≈ ENTRY + a thin index;
materially above "ENTRY + ~3K" means something over-prefetched → note it in the wrap OS-review.

**Loading law:** load the smallest *file* that holds what you need; pointer-first, never scan. For small,
one-screen docs, read the whole file. Sub-file targeting is achieved by **splitting the file, never by line
numbers** (line anchors rot).

---

## §3. Pull-based execution (need-to-know)

The entry indexes are routing tables — they name the exact next read, so you fetch a *section*, never scan
a *doc*.

| Trigger during the session | Load (smallest slice) |
|---|---|
| Work reaches a feature/route | that `ACTIVE_RUNTIME_MAP` feature block (→ files, tests, gotchas, design-doc pointer) |
| Need subsystem internals | the **one** canonical design doc named by that block |
| Touching a watchpointed module | **only that watchpoint** (§6) |
| A constraint / decision question arises | the relevant **full Doctrine section** |
| About to ship code | the **Wrap/Risk card** (§4) |
| Need "what happened / why last time" | the **Handoff HISTORY** tail, or a closed handoff |

**Use what already exists.** Before hand-rolling something, check whether the platform, framework, or
design system already provides it. An existing sanctioned capability is the default; reinventing it is the
exception that needs a reason.

---

## §4. Validation chain + risk tiering (the anti-drift core)

**Three roles, never collapsed:**
- **Advisor** (the AI in-session): proposes, drafts, articulates rationale, raises open questions. Defers to the human on every architectural call, to doctrine on every conflict.
- **Validator** (an *independent* agent/session with no context from the design): reads the artifact cold against doctrine, surfaces gaps and blind spots.
- **Decider** (the human): reviews both, makes the call, records it.

Why: **the advisor's blind spots are systematic** — once a framing is set, the advisor defends it. Only a
fresh reader catches what it cannot see. **Implementation agents are never their own validators.**

**Pacing.** Validation happens **between sessions, not inside them.** Daily cadence does not mean every
session ships fully validated — it means every *architectural commitment* ships validated. A draft may live
a day or two awaiting independent validation before it is locked.

**At the diff level this becomes blind + hostile review:**
- **Blind review** — independent agent cold-reads the final diff with no plan/history, reports structure first, then concerns.
- **Hostile review** — independent agent adversarially probes for edge cases, contract violations, regression vectors, unstated assumptions.
- Record findings + fixes + accepted risks (held in the PR body between STOP and wrap, then transcribed into the closing handoff).

**Risk tier is set by TOUCHED CODE PATHS, not stated intent.** "Just cosmetic / tiny / one-liner" is
irrelevant; what the change *touches* sets the tier. Risk **escalates, never silently demotes**; the
implementer never self-grants a lower tier.

| Tier | Examples | Required before merge |
|---|---|---|
| **Low** | presentation / copy, *and nothing in the trigger list* | targeted verification, re-run by a non-implementer |
| **Medium** | behavior, no doctrine-critical path | + independent verification + full suite + **blind + hostile review** |
| **High** | commit paths, auth, persistence, authority/truth contracts, deletion, logging | + **blind + hostile review on the diff before push** |

Any diff touching a watchpointed file, a recently-shipped engine, or a save/auth/persistence/async-mutation
path is **Medium minimum**, even when the visible intent is cosmetic. Every bug fix lands a
regression/sentinel test that would fail without the fix. Efficiency comes from **not duplicating expensive
work** (one verification pass; reviewers read the diff, don't each re-run the suite) — **never** from
cutting reviews.

---

## §5. The wrap ritual (the only thing that makes the next session possible)

Runs **only after merge + human validation** for code sessions. Outputs are **trigger-based** — a doc is
touched only if its condition fired. The **Handoff is the single unconditional output.**

| Doc | Touch trigger |
|---|---|
| design artifact(s) | session created/changed design content |
| `DECISIONS_LOG` (ADR) | an architectural decision was made |
| `ACTIVE_RUNTIME_MAP` | a call chain / route ownership / persistence-write path / major flow / test ownership changed (NOT UI/copy/test-only) |
| `ACTIVE_ARCHITECTURE` | new subsystem, or subsystem ownership materially changed |
| `Audit` | a durable finding surfaced and isn't being worked next session |
| `DOCTRINE` | validation surfaced a doctrine gap (rare; requires an ADR) |
| `operating-model.md` | an operational/process rule changed (not an ADR) |
| **Handoff** | **always** |

**Handoff procedure (always):**
1. **Archive** the active handoff verbatim → `docs/archive/Handoff_Session<N>_Closed.md`. Immutable historical truth.
2. **Create** `HANDOFF_Session<N+1>_Active.md` from the archived base, in **ENTRY / `---` / HISTORY** shape:
   - **ENTRY (thin, the only required startup read):** standing platform facts · next-session scope · anti-scope · active carry-forward ledger (incl. deferred-design seeds) · known placeholders · OS backlog · doctrine-discipline check.
   - **HISTORY (lazy, on trigger):** state at close · commits this session · validation record · roadmap changes.
3. **Verify exactly one active handoff** at `docs/` root (two = error state).
4. Commit wrap docs **after** merge + validation (they don't ride the code PR).

The archive copy and the new active handoff are **two truths that must never be the same file** — the
archival step separates "what session N inherited" (immutable) from "what N+1 inherits" (operational).

**Carry-forwards** are a standing ledger: an item rolls forward (with a one-line reason) until done /
re-scoped / dropped. Rolled 3+ sessions untouched → flagged for an explicit decision.

**Deferred-design seeds** — at every wrap, ask: does this work contain non-obvious future-design seeds a
later session would expensively re-derive? Capture only if it's a future architectural decision / subsystem
/ cross-cutting policy (1–3 bullets, the idea not the implementation). Bugs, backlog, speculation do not
qualify. Each seed is reviewed every wrap: still-relevant / implemented / rejected / superseded.

**Operating-system review (every wrap, 3 questions, ≤2 items, zero is the expected default):**
1. Did I read anything I never used? (over-prefetch → tighten the intent profile)
2. Did I fail to find something, or hit a stale/missing index entry? (under-coverage)
3. Did startup overshoot budget?
→ Resolve each either **(a) integrate now** (trivial + in-scope) or **(b) OS backlog** (one bullet in the
active handoff, rolled forward until discharged). No permanent improvement log; the correction surface
stays capped so process never becomes bureaucracy.

---

## §6. Long-horizon watchpoints (predictable failure modes)

A standing list of failure modes the architecture is *structurally vulnerable to over time* — none a
problem now, all recorded so they aren't re-derived when they start to manifest. Each has a **watch
trigger** (the event that means "open this") and a **bar** (the test it must pass). Loaded only when a
session touches that area. Reusable shapes worth stealing (see `templates/WATCHPOINTS.md`):

- **Operational sprawl** — an infra/utility module quietly accreting workflow/business logic. *Bar:* "true infrastructure primitive, or logic in the wrong module?"
- **Shared-module dumping grounds** — `shared_*`, `common_*`, `base_types`. *Bar:* a new shared module answers "what one truth does this own?" with no "and."
- **Read-validation erosion** — "validated on write, skip the read-side re-check." *Bar:* consumer-side validation on integrity paths is non-optional; relaxation is a doctrine question.
- **Convenience-backend drift** — a storage module growing helpers named for *semantic outcomes*. *Bar:* names describe storage operations, not caller-side outcomes.
- **Authority drift** — an authority/truth module growing into a permissions framework; read-time reinterpretation of history; per-context policy branching; semantics inferred from UI presentation instead of the surface contract.

The general move: **name the drift before it happens, attach a structural trigger, and make any relaxation
a doctrine-level decision rather than a quiet local exception.** Sprawl happens through individually-
reasonable exceptions; watch triggers make the cost of each one visible.

---

## §7. Doc discipline (so the doc surface never becomes the problem)

- **One new design doc per session, max.** Either create one *or* edit existing — never both. (Wrap docs exempt.)
- **Doc creation requires justification:** "Can this fit an existing doc?" Default is edit, not create.
- **Numerical targets are triggers for scrutiny, not religious constants:** ≈8 active docs, ≈25 ADRs. Approaching either triggers a session to ask *whether the architecture went wrong*, not whether to quietly raise the limit. Raising a limit is a doctrine-level decision recorded with rationale.
- **Append before edit; edit before rewrite.** Rewrite/consolidation happens only in a dedicated session.
- **Archive deliberate, never delete.** Active docs stay small; the archive grows without limit.
- **Docs optimize for AI retrieval, not human narrative.** Prefer decisions, contracts, ownership, invariants, boundaries, checklists. Avoid repeated rationale, history, duplicate explanation, prose-where-a-checklist-suffices.
- **Pointer docs survive; narrative docs decay.** Maps and ADRs *point at* the one canonical design doc; they never restate it.
- **ADR shape:** Title · Date(session) · Status · Design source · Decision · Rationale (defends the *framing*, not just the choice) · Consequences · Supersedes/superseded-by · References. If an ADR is longer than its source design, something is wrong.

---

## §8. The five ideas, if you remember nothing else

1. **Doctrine is a constitution.** Constraints + invariants + explicit refusals, written once, deferred to forever. When code and doctrine disagree, the code is wrong.
2. **The record stays behind reality.** Nothing is documented / ADR'd / handed-off until it's merged and validated. STOP at Engineering Complete. (One gated writer: `session-wrap`.)
3. **Independent validation is non-negotiable.** The advisor cannot review itself; blind + hostile review on the real diff, scaled to *touched* blast radius.
4. **Read thin, pull on trigger.** Tiered startup + pointer-shaped routing maps orient a session in ~10K tokens instead of reading the whole corpus.
5. **The operating model self-corrects but stays capped.** A 3-question OS-review every wrap, ≤2 items, zero expected — process improves without becoming ritual.

---

*Setup order for a new product: write the Doctrine first (hardest; everything defers to it) → keep this
operating model → extract the Constraints_Card from the Doctrine → stub the maps + Decisions_Log empty →
create `CLAUDE.md` as the pointer → create the first Handoff ENTRY/HISTORY. Then run session 1.*
