# <PROJECT> — Active Handoff (S1)

**Purpose.** Operational truth the next session reads on entry. **ENTRY/HISTORY split:** read the ENTRY at
start; pull HISTORY only on a "what happened / why" trigger.

**Wrap invariant.** Exactly one active handoff at `docs/` root. At close, archive verbatim →
`docs/archive/Handoff_Session1_Closed.md` and create `HANDOFF_Session_002_Active.md`.

---

# ENTRY (required startup read — keep it thin)

## Standing platform facts (durable; edit only on change)
- <stack, hosting, deploy, auth — the durable environment facts a session needs>

## Next-session scope
- <the center of gravity + concrete deliverables for this session>

## Anti-scope
- <what this session must NOT absorb; locked decisions not to reopen>

## Active carry-forward ledger
- <open items only; each with a one-line reason it hasn't completed>
- **Deferred design seeds:** <non-obvious future-design ideas a later session would else re-derive>

## Known placeholders
- <every deliberate stub still live, with its replacing session + trigger>

## OS backlog
- <temporary operating-system improvement items from the wrap OS review; rolled forward or discharged>

## Doctrine-discipline check
- Active core-doctrine docs: <n> (target ≈8). ADR count: <n> (target ≈25). One-doc-per-session: <ok>.
- Validation status: <what is validated / awaiting validation>.

---

# HISTORY (lazy — load only on a "what happened / why" trigger)

## State at session close
- <what shipped, what was decided, what changed>

## Commits this session
- <branch + one line per commit>

## Validation record
- <blind/hostile findings, fixes applied, accepted risks>
