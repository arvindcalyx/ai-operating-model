# <PROJECT NAME> — Session Bootstrap

operating-model: v1.0.0

This file **POINTS; it does not RESTATE.** The operating model lives in `docs/` and the engine in
`skills/`. This file is in context every turn, so it stays minimal.

## Start every session
1. State one line: `Initiating S<N> <Intent>`  (Intent ∈ Bug | Investigation | Design | Implementation).
2. Run the `session-start` skill. It loads the Tier-0 set:
   - **Always:** the single active `docs/HANDOFF_Session<N>_Active.md` **ENTRY** + `docs/CONSTRAINTS_CARD.md`.
   - **+ intent index:** `docs/ACTIVE_RUNTIME_MAP.md` and/or `docs/ACTIVE_ARCHITECTURE.md` (see the skill).
   - Read each whole file — keep them small.
3. **Startup safeguard:** check for an open PR from the previous session. If one exists, surface it and
   ask whether we're resuming it (validate → wrap) or starting new work.
4. Emit the orientation line and wait for direction.

Pull everything else (full `DOCTRINE`, `WATCHPOINTS`, a design doc) only on its trigger during the session.

**Authority:** `DOCTRINE` > `docs/operating-model.md` > this pointer.
**Single Gated Writer:** only `session-wrap` writes durable state, and only after merge + human validation.
