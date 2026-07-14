# Contributing

Thanks for your interest in `ai-operating-model`. This repository is the reusable **engine** for an
operating model, and it grows deliberately: slowly, and only on evidence.

## Before you propose a change

Read [`GOVERNANCE.md`](GOVERNANCE.md). The core rule is **evidence-first**: a new skill, rule, or
template is admitted only after a real session or project proves the current model insufficient.
"This might be useful" or "other frameworks have it" is not evidence — the default answer to an
addition is *no*.

The highest-value contribution is therefore often an **issue** that documents a concrete failure:
what you were doing, what the model lacked, and why an existing skill or doc couldn't absorb it.

## What belongs here

- **Engine** — `skills/` (behavior) and `templates/` (state schema) — lives here and is versioned via
  `MODEL_VERSION`.
- **Your project's own state** — its filled-in doctrine, handoffs, decision log, and maps — does **not**
  live here; it belongs in your project's repo.

## Opening a pull request

1. Open an issue describing the problem and the evidence for it.
2. Keep the change minimal and self-contained; match the existing tone and structure.
3. Note the version impact per `GOVERNANCE.md` (patch / minor / major).

By contributing, you agree that your contributions are licensed under the repository's
[MIT License](LICENSE).
