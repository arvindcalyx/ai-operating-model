# <PROJECT> — Active Runtime Map (dynamic paths)

**What this is.** Given a feature or route, the ordered call chain, the test that proves a fix, and the
non-obvious invariants. It exists so a session can answer *"where do I look / what else does this affect /
how do I verify the fix"* from one retrieval instead of reading the whole codebase.

**§0 Maintenance contract.** Anchor on **route + function name, never line numbers** (line anchors rot).
One screen per feature block. Link to the canonical design doc for detail; add no design content or history.

---

## §1. Route / entry index

| Route or entry point | Feature block (§3) | Owner module |
|---|---|---|
| <e.g. POST /v1/...> | <#feature-name> | <module> |

## §2. Shared primitives

| Primitive | Used by | Impact radius |
|---|---|---|
| <name> | <features> | <what breaks if it changes> |

## §3. Feature blocks

*(One block per feature, added as you build. Template:)*

### <feature-name>
- **Entry:** <route + handler fn>
- **Call chain:** <fn → fn → fn>
- **Writes:** <durable state touched, or "none">
- **Proves-a-fix test:** <the test that fails without the fix>
- **Gotchas / invariants:** <non-obvious things>
- **Design doc:** <docs/..._Design_v1.md>
