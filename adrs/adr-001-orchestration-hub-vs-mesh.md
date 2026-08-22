# ADR-001: Central Orchestration Hub vs. Point-to-Point Mesh vs. Hybrid Shared-Ledger Mesh

## Status
Accepted (escalated to Executive Sponsor for final ratification after ARB-delivery team disagreement — see Governance Notes)

## Context

Paivo's current architecture is a point-to-point mesh: every merchant-rail combination has its own bespoke integration. This has produced unamortized integration cost, fragmented reconciliation, and no viable path to centralized ISO 20022 compliance ahead of the regulatory deadline. The architecture function needed to choose the target topology for the re-architecture program.

## Decision

Adopt a **central orchestration hub with rail-specific adapters**: all payment traffic routes through one orchestration core that owns routing, idempotency, retries, and ledger writes; rail-specific protocol handling is isolated to independently deployable adapters behind a standard interface.

## Alternatives Considered

**1. Point-to-point mesh, improved (keep current topology, add shared libraries for reconciliation and ISO 20022 formatting).** Rejected because it does not remove the fundamental coupling problem — every rail integration would still independently call into the shared libraries, meaning a library bug or upgrade still requires coordinated changes across N integration teams, and there is still no single ledger of truth, only a shared formatting utility. This alternative was attractive mainly because it required the least short-term engineering investment (estimated 30% less Year 1 cost than the hub), but the ARB assessed it as solving the symptom (duplicated formatting code) without solving the root cause (no central point of coordination).

**2. Hybrid: shared ledger service with decentralized rail adapters calling it directly, but no central orchestration/routing layer.** Each rail integration keeps its own routing and retry logic but writes to a common ledger. This was the most seriously contested alternative — it would have delivered the single-ledger-of-truth benefit (the single biggest reconciliation win) with substantially less build effort than a full orchestration hub (estimated 35 fewer person-months). It was rejected because it does not solve idempotency consistency (architecture principle 4) — without a central point enforcing idempotency semantics uniformly, each integration would still implement retry logic independently, and the double-debit risk that already exists today would persist. It also does not provide a natural home for the ISO 20022 transformation layer, which would again end up duplicated per integration or bolted onto the ledger service in a way that violates principle 2.

**3. Full central orchestration hub (selected).** Highest build cost and longest time-to-first-production-rail of the three options, but the only one that simultaneously satisfies principles 1 (single ledger), 2 (rail-agnostic core), 4 (idempotency by design), and 6 (centralized transformation layer) without a structural gap.

## Consequences

**Positive:** New rail onboarding becomes a bounded adapter-build task; reconciliation runs against one ledger; ISO 20022 compliance is centrally implemented and auditable; idempotency is platform-guaranteed rather than team-dependent.

**Negative (accepted trade-off):** The hub is now the single most architecturally critical component in the estate — its availability directly gates all payment processing across every rail. This requires active-active multi-region deployment to meet the 99.95% Tier-0 SLA (architecture principle 8), adding an estimated 20% to steady-state infrastructure cost versus a simpler single-region deployment. Build cost and time-to-first-production-rail are also the highest of the three alternatives, which the migration roadmap manages by front-loading Wave 0 foundation work well ahead of any rail cutover.

## Governance Notes

This decision produced the program's one recorded escalation to the Executive Sponsor: Platform Engineering initially favored the hybrid shared-ledger option specifically to hit an earlier date for the first live rail, and disagreed with the ARB's assessment that the idempotency gap was disqualifying. The Executive Sponsor (CTO) reviewed both positions and ratified the ARB's recommendation, citing the existing double-debit incident history as decisive evidence that idempotency could not be left to per-integration discipline.

## Stakeholders Affected
ARB (decision owner), Head of Platform Engineering (build cost and timeline owner), Head of Risk & Compliance (ISO 20022 compliance path), Executive Sponsor (final ratification).

---
*Fictional case study — see [README](../README.md) for full disclaimer.*
