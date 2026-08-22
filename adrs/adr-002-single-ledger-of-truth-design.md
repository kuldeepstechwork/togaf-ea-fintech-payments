# ADR-002: Single Ledger of Truth — Synchronous Write, Immutable Append-Only Design

## Status
Accepted

## Context

Reconciliation under the as-is architecture is fragmented across N rail-specific stores with no authoritative record, producing late-discovered discrepancies and a multi-day manual reconciliation cycle. Architecture principle 1 mandates a single ledger of truth. This ADR addresses the specific design of that ledger: how and when it is written, and what consistency and mutability guarantees it offers.

## Decision

The ledger is an **append-only, immutable record**, written **synchronously** by the orchestration core as part of determining a transaction's terminal state — meaning a transaction is not considered "orchestrated" (its terminal status is not returned to the caller) until its ledger entry is durably written. No component may update or delete an existing ledger entry; corrections are made by writing a new, linked adjustment entry, never by mutating history.

## Alternatives Considered

**1. Asynchronous ledger write (write ledger entry after returning status to caller, via an eventual-consistency event stream).** This was seriously considered because it would reduce the orchestration core's write-path latency — the ledger write would no longer be on the critical path of the initiate/status API calls, an estimated 15-25ms latency improvement. It was rejected because it reintroduces exactly the "which number is correct" ambiguity the ledger exists to eliminate: in the window between returning status to a caller and the asynchronous ledger write landing, the ledger is not actually authoritative, and a crash in that window could produce a transaction with a caller-visible terminal status but no ledger record at all — an unacceptable audit gap given the regulatory driver behind this program.

**2. Mutable ledger with update-in-place for corrections (e.g., a refund updates the original transaction's status field directly).** This is simpler to query — no adjustment-entry-chasing logic needed to reconstruct current state — and was the pattern several of Paivo's legacy rail-specific stores already used. It was rejected because it destroys the audit trail that both the regulator and internal Finance require: an auditor reviewing a transaction's history needs to see every state change and when it happened, not just the current state. Mutable records also created reconciliation ambiguity in the legacy system whenever a correction happened to overlap with an in-flight reconciliation read — a race condition class that an append-only design eliminates structurally.

**3. Selected: append-only, immutable, synchronous write.** Highest write-path latency and highest storage growth over time (ledger never shrinks, only grows via new entries) of the three options, but the only one that gives an unambiguous, complete, race-free audit trail.

## Consequences

**Positive:** Every transaction's full history is reconstructable and auditable; no race conditions between reconciliation reads and corrections; the ledger's authority is never in question because there is no window where a caller-visible status exists without a corresponding durable ledger entry.

**Negative (accepted trade-off):** Synchronous ledger writes add latency to the orchestration core's hot path — measured in staging at an added 18ms p99 versus an async design — which the orchestration core's latency budget absorbs by design (see [`07-phase-g-implementation-governance/architecture-contracts.md`](../07-phase-g-implementation-governance/architecture-contracts.md) for how adapter latency budgets are set with this reserved). Storage cost grows unboundedly with transaction volume rather than being bounded to current state, requiring a defined archival/cold-storage tiering policy for ledger entries beyond the active audit window (handled operationally, not architecturally, and not a program blocker).

## Stakeholders Affected
ARB (decision owner), Head of Risk & Compliance (audit trail requirement driver), Finance/Controller (reconciliation consumer), Platform Engineering (latency budget owner).

---
*Fictional case study — see [README](../README.md) for full disclaimer.*
