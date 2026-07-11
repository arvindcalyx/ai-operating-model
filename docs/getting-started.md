# Getting started — instantiate the operating model in a new project

You are copying the **state schema** (`templates/`) into your project and installing the **engine**
(`skills/`) for your agent. Your project then owns its state; the engine stays read-only.

## Steps

```
1. Copy templates/ into your project:
     cp -r ai-operating-model/templates/*  your-project/docs/
     mv    your-project/docs/CLAUDE.md      your-project/CLAUDE.md
   Record the engine version you pulled — add one line to CLAUDE.md:
     operating-model: v1.0.0

2. Write docs/DOCTRINE.md   ← do this first; it is the hardest and everything defers to it.
     Your invariants (things that must always be true), constraints (operational lines you won't
     cross), explicit refusals (what the product will NOT do), and success/failure criteria.
     Start with 3–5 invariants. See the guidance inside the template.

3. Extract docs/CONSTRAINTS_CARD.md from the doctrine — the one-screen, always-loaded surface.

4. Leave docs/ACTIVE_ARCHITECTURE.md, docs/ACTIVE_RUNTIME_MAP.md, docs/DECISIONS_LOG.md as stubs.
   They grow as you build. Leave WATCHPOINTS.md with only the reusable shapes until you hit a real one.

5. Fill docs/HANDOFF_Session_001_Active.md ENTRY: scope = your first real piece of work.

6. Install the skills for your agent (v1):
     - point the agent at ai-operating-model/skills/, OR
     - copy skills/ into the agent's skills directory (e.g. .claude/skills/ for Claude Code).

7. Start your first session:  Initiating S1 Design
```

## The rule while you work

- Read `CLAUDE.md` at the top of every session; it points you at the Tier-0 set. Don't read the whole
  corpus to start.
- The engine files (`skills/`) are never edited inside your project. Only your state files change.
- Nothing is documented as done until it is merged and validated (the `session-wrap` gate).

## Upgrading the engine later

Re-pull `skills/` at a newer `MODEL_VERSION`. Your project state (doctrine, handoff, ADRs, maps) is
untouched, because behavior and state are separate. If the new version is a **major**, check
`MIGRATIONS.md` for the template-shape changes to apply.
