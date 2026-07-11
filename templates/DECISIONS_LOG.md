# <PROJECT> — Decisions Log (ADRs)

Architectural decisions only, append-only. ADRs **point at** the design artifact that carries the detail;
they do not restate it. If an ADR is longer than its source design, something is wrong. Target ≈25 over
the project's life — approaching it triggers a review of whether the architecture went wrong, not a quiet
raise. Operational choices (cadence, tooling, doc shape) do **not** get ADRs; they live in the operating
model.

## ADR format (ADR-001 is the template shape)

- **Title** — "ADR-NNN — short imperative phrase."
- **Date** — session identifier (e.g. "S1 close") or ISO date.
- **Status** — Proposed | Accepted | Locked | Superseded.
- **Design source** — the artifact(s) this records the acceptance of.
- **Decision** — what was decided (numbered list if multi-part; brief).
- **Rationale** — why this decision, and why this *shape* (one ADR vs several, this scope vs broader).
- **Consequences** — what downstream sessions inherit; what becomes load-bearing.
- **Supersedes / superseded by** — lineage (even if "nothing").
- **References** — doc names, not restatements.

---

## ADR-001 — <example: adopt the operating model>

- **Date:** S1 close
- **Status:** Accepted
- **Design source:** `docs/operating-model.md`, `docs/DOCTRINE.md`.
- **Decision:** This project runs under the ai-operating-model engine (v1.0.0). Doctrine is the source of
  truth; the single-gated-writer wrap ritual governs all durable writes.
- **Rationale:** Establishes the authority chain and the review discipline before any code lands, so drift
  has no room to start. Recorded as one ADR because these commitments are interdependent and locked
  together.
- **Consequences:** All later sessions inherit the session lifecycle, the risk-tiered review, and the
  wrap/handoff continuity.
- **Supersedes / superseded by:** nothing / nothing.
- **References:** `README.md`, `docs/DESIGN.md`.
