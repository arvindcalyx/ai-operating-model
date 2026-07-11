# GOVERNANCE — how this repo is allowed to grow

The failure mode this model exists to prevent is sprawl: rules, docs, and abstractions accreting through
individually-reasonable additions until the surface itself becomes the problem. This repo governs its own
growth by the same rule it teaches.

## The promotion rule (evidence-first)

**A change to the operating model — a new skill, a new rule, a new template, a raised limit — is admitted
only after it proves the current model insufficient against real project history.**

Before adding anything, answer in writing:

1. **What real session or project failed** because the current model lacked this? (Not hypothetical.)
2. **Could an existing skill / rule / doc absorb it** instead of a new one?
3. **What does it cost** — added startup tokens, another file to load, a new concept to hold?
4. If it can't point to a real insufficiency, **the correct decision is no change.**

"This might be useful," "other frameworks have it," and "it feels more complete" are not evidence. The
default answer to a proposed addition is no.

## Versioning

- `MODEL_VERSION` is the engine's semantic version. Consuming projects pin to it.
- **Patch** — wording/clarity fixes to skills or docs; no behavior change.
- **Minor** — a new skill or rule that is additive and doesn't change how existing projects run.
- **Major** — a change to a skill contract or a template's shape that consuming projects must migrate to.
  A major ships with a `MIGRATIONS.md` entry describing the upgrade.

## What lives where

- Behavior changes → `skills/`. State-schema changes → `templates/`. Both are engine changes and bump
  `MODEL_VERSION`.
- A consuming project's own doctrine, handoff, ADRs, and maps are **not** governed here — they live in
  that project's repo and evolve on its own cadence.

## Scope discipline

Numerical targets in the model (≈8 active docs, ≈25 ADRs per project) are **triggers for scrutiny, not
limits to quietly raise.** Approaching one triggers a review of whether the architecture went wrong.
Raising a target is itself a governed decision recorded with its evidence.
