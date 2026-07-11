# <PROJECT> — Watchpoints

**What this is.** Predictable failure modes the architecture is *structurally vulnerable to over time* —
none a problem now, all recorded so they are not re-derived from scratch when they start to manifest. Each
has a **watch trigger** (the concrete event that means "open this") and a **bar** (the test it must pass).
Loaded **only** when a session touches the relevant area.

**The general move:** name the drift before it happens, attach a structural trigger, and make any
relaxation a doctrine-level decision — never a quiet local exception. Sprawl happens through individually-
reasonable exceptions; a named watch trigger makes the cost of each one visible.

---

## Reusable shapes (starter set — keep the ones that fit, delete the rest)

- **Operational sprawl** — an infra/utility module quietly accreting workflow or business-adjacent logic
  until it becomes the whole infra layer and changes to it are high-blast-radius.
  *Trigger:* every change to that module. *Bar:* "true infrastructure primitive, or logic slipping into
  the wrong module?"
- **Shared-module dumping ground** — `shared_*`, `common_*`, `base_types` categories that grow without a
  sharp boundary. *Bar:* a new shared module must answer "what one truth does this own?" in a sentence with
  no "and" — else its content goes inside the consumer.
- **Read-validation erosion** — "the data was validated on write, skip the re-check on the fast path."
  *Bar:* consumer-side validation on integrity paths is non-optional; relaxation is a doctrine question.
- **Convenience-backend drift** — a storage/substrate module growing helpers named for *semantic outcomes*
  (`get_user_streak`) instead of storage primitives. *Bar:* the name describes a storage operation, not a
  caller-side semantic outcome.
- **Authority drift** — for any module owning an authority/truth contract: it must not grow into a
  permissions/policy framework; decisions stay point-in-time and append-only (no read-time reinterpretation
  of history); one evaluator / one policy (no `if migration_mode then relaxed_rules`); and contract
  semantics live in the surface *contract*, not the surface *presentation*.

---

## Project watchpoints (add as you hit them)

### W-1: <name>
- **What it guards:** <the line being held, and why it will be pressured over time>
- **Watch trigger:** <the concrete event that means "open this watchpoint">
- **Bar:** <the test the change must pass; else it's a doctrine-level decision>
