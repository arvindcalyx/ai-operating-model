# <PROJECT> — Product Doctrine

**What this is.** The constitution. What the product **is / is not / refuses to do**, the non-negotiable
constraints and invariants, and the success/failure criteria. Everything defers to it. If a design or a
change conflicts with a line here, **the design is wrong, not the doctrine.** ADRs never override it.

**How to write it (do this first, it is the hardest file).** Derive your invariants and constraints from
the failures you already know you must never repeat. An invariant you can't tie to a real risk is
probably not load-bearing. Start with 3–5. Add only when a real need appears.

---

## 1. What the product is / is not

- **Is:** <one or two sentences — the real value, not the feature list>
- **Is not / refuses to do:** <the categories you deliberately reject — name them, so scope creep is a
  doctrine violation, not a judgement call>

## 2. Invariants (the tersest binding form)

> Things that must always be true, across every boundary. Number them `I-1…I-n`.

- **I-1** — <e.g. "The system's source of truth is never silently mutated by an interpretation layer.">
- **I-2** — <e.g. "AI is permitted as a capability, never as an authority.">
- **I-3** — <e.g. "A user-confirmed value overrides any system inference.">

## 3. Constraints (operational lines you won't cross)

> Where the invariants bite in day-to-day work. Number them `C1…C-n`. These are what the Constraints Card
> compresses.

| | Constraint | Where it bites |
|---|---|---|
| **C1** | <e.g. "AI never writes to durable state without explicit human confirmation."> | <the exact path/module where this is enforced> |
| **C2** | <e.g. "Provenance is preserved across every boundary."> | <...> |

## 4. Success / failure criteria

> How you'll know months later that the doctrine held. Write these as checkable statements, not vibes.

- <e.g. "No AI-driven value was persisted without user-visible attribution.">
- <e.g. "No interpretation layer wrote back to the operational source of truth.">

---

**Overrides.** If a change must violate a C or I line, that is a doctrine-level decision, not an inline
call. Stop, route it through the validation chain, and record the override (with its justification) in
`DECISIONS_LOG.md`. Silent drift across many small exceptions is the failure mode this file prevents.
