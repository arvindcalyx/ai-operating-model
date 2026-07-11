# <PROJECT> — Active Architecture (static topology)

**What this is.** A navigation index: *what lives where*. Subsystem → runtime location → the one canonical
design doc that owns its detail. **No design content, no rationale, no history** — those live in the design
docs. This map only points.

Read `§2` at session start for Design / Investigation / Implementation intents.

---

## §1. Purpose & maintenance contract

- This is a pointer doc. Keep each row to one line.
- Update it when a new subsystem appears, or when ownership of one materially changes.
- Discoverability test at wrap: *"If a new engineer or agent starts here, can they find this subsystem's
  design doc?"* If not, add a row.

## §2. Subsystem map

| Subsystem | Runtime location | Canonical design doc | Status |
|---|---|---|---|
| <name> | <path/module> | <docs/..._Design_v1.md> | <active / shipped> |

*(Empty on day one. Grows as you build.)*
