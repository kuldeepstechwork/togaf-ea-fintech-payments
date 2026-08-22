# Architecture Principles

These principles were ratified by the Architecture Review Board (ARB) during the Preliminary Phase and bind every architectural decision made in Phases A through H. Each principle follows the TOGAF format: Name, Statement, Rationale, Implications. Where a later phase decision appears to conflict with a principle, the ARB waiver process in [`governance-framework-setup.md`](governance-framework-setup.md) applies.

## 1. Single Ledger of Truth

**Statement:** Every payment transaction, regardless of rail or PSP, is recorded exactly once in a single, authoritative ledger service that all downstream reconciliation, settlement, and reporting consumes.

**Rationale:** Fragmented per-rail reconciliation is the direct cause of Paivo's current settlement discrepancy backlog and the primary driver of manual finance effort. A single ledger removes the "whose number is correct" problem structurally rather than procedurally.

**Implications:** Every rail adapter must emit a normalized transaction event to the ledger before it is considered "orchestrated." No team may stand up a parallel or shadow ledger for convenience. The ledger becomes a Tier-0 system and inherits the strictest availability and audit requirements in the estate.

## 2. Rail-Agnostic Orchestration Core

**Statement:** The orchestration core (routing, retries, idempotency, ledger writes) must contain no rail-specific or PSP-specific logic; all such logic is isolated to adapters.

**Rationale:** Rail and PSP APIs change on vendor schedules Paivo does not control. Isolating that volatility to adapters means a PSP contract change is a bounded, single-team change instead of a core regression risk.

**Implications:** New rail onboarding is an adapter-build exercise, not a core-platform change. Adapter interfaces must be versioned and contract-tested independently of the core. Core release cadence can be decoupled from adapter release cadence.

## 3. Regulatory Compliance Is Non-Negotiable Scope, Not a Feature

**Statement:** ISO 20022 structured messaging, PCI-DSS scope isolation, and RBI-equivalent audit trail requirements are treated as mandatory constraints on every design, not as optional backlog items that compete with feature work.

**Rationale:** The program exists because of a regulatory deadline. A design that satisfies functional requirements but slips compliance readiness fails the program's primary mandate regardless of how well it performs otherwise.

**Implications:** Compliance checkpoints are embedded in the Phase G governance gates (see [`07-phase-g-implementation-governance/`](../07-phase-g-implementation-governance/)), not bolted on before audit. Any solution building block touching cardholder data or structured payment messages requires ARB sign-off before build starts.

## 4. Idempotency by Design, Not by Convention

**Statement:** Every state-changing operation in the orchestration platform — payment initiation, retry, refund, reconciliation adjustment — must be idempotent at the API contract level, enforced by the platform, not by caller discipline.

**Rationale:** Cross-rail payment orchestration involves retries across unreliable networks and partially-failed PSP calls. Relying on calling services to "be careful" about duplicate submission has already caused double-debit incidents in the current architecture's ad hoc retry logic.

**Implications:** Every orchestration API requires a caller-supplied idempotency key validated and stored by the platform. Adapter developers cannot bypass this for "simple" rails. See [ADR-004](../adrs/adr-004-idempotency-retry-strategy.md) for the mechanism.

## 5. Data Minimization at the Boundary

**Statement:** Sensitive cardholder and account data is tokenized or truncated at the earliest point of ingestion and never persisted in full form outside the PCI-DSS-scoped vault.

**Rationale:** Every system that stores raw PAN or account data enters PCI-DSS scope and multiplies audit cost and breach blast radius. Minimizing where sensitive data lives is cheaper than securing it everywhere.

**Implications:** All internal services — ledger, reconciliation, analytics, orchestration core — operate on tokens, not raw data. Only the vault and the minimum set of PCI-scoped services can detokenize. See [`technology-standards.md`](../04-phase-d-technology-architecture/technology-standards.md) for scope boundaries.

## 6. Build the Transformation Layer Once, Centrally

**Statement:** ISO 20022 (and any future message standard) transformation logic lives in one shared transformation layer positioned between rail adapters and the orchestration core, not duplicated per adapter.

**Rationale:** Duplicating message transformation per adapter was evaluated and rejected specifically because it reproduces the point-to-point maintenance burden the program exists to eliminate — see [ADR-003](../adrs/adr-003-iso20022-transformation-layer-placement.md).

**Implications:** Adapters emit and consume a single canonical internal message schema; only the transformation layer knows both the canonical schema and any external wire format. Standard version upgrades (e.g., a future ISO 20022 minor revision) are made in one place.

## 7. Design for Multi-Rail Extensibility, Not Multi-Rail Prediction

**Statement:** The platform must make adding an unspecified future rail or PSP a bounded, well-defined engineering task, without attempting to pre-build support for rails not yet on Paivo's roadmap.

**Rationale:** Over-generalizing for hypothetical future rails (e.g., building abstractions for payment types Paivo has no committed plan to support) has historically produced speculative complexity that is never fully exercised and becomes a maintenance tax. TOGAF principle of "just enough" architecture applies.

**Implications:** The adapter interface is documented and contract-tested well enough that a new rail can be onboarded by following a defined process (see [`solution-building-blocks.md`](../05-phase-e-opportunities-and-solutions/solution-building-blocks.md)), but no adapter code is written speculatively.

## 8. Availability Commitments Are Explicit and Tiered

**Statement:** Every service in the orchestration estate is assigned an explicit availability tier (Tier-0 through Tier-2) with a stated SLA, and architecture decisions must state which tier they are designed to meet.

**Rationale:** Paivo's largest merchant contracts carry 99.95% availability commitments with financial penalties. Without explicit tiering, teams default to "best effort," which under-serves Tier-0 services and over-invests in low-criticality ones.

**Implications:** The orchestration core and ledger are Tier-0 (99.95%, active-active multi-region). Adapters are Tier-1 (99.9%, active-passive acceptable per rail). Reporting and analytics are Tier-2 (99.5%). Infrastructure spend is justified against tier, not applied uniformly.

## 9. Vendor and Rail Substitutability

**Statement:** No single PSP, rail, or orchestration vendor product may become structurally load-bearing to the point that switching it requires a core platform redesign.

**Rationale:** Paivo has already experienced vendor lock-in cost with legacy point-to-point integrations that make PSP renegotiation leverage-poor. The orchestration platform is explicitly a hedge against repeating that mistake.

**Implications:** Any commercial component evaluated in [`vendor-evaluation.md`](../05-phase-e-opportunities-and-solutions/vendor-evaluation.md) is scored partly on how cleanly it can be replaced. Adapters, not the core, absorb vendor-specific coupling.

## 10. Traceability from Business Requirement to Deployed Component

**Statement:** Every solution building block deployed under this program must trace back to a stated business or regulatory requirement captured in Phase A or a subsequent gap analysis.

**Rationale:** Architecture scope creep — building capability nobody asked for — is a recurring failure mode in platform re-architecture programs and a direct threat to the program's cost and timeline commitments to the Board.

**Implications:** The Phase E gap analysis and solution building blocks explicitly cite the driving requirement. The ARB rejects proposals that cannot demonstrate this traceability.

## 11. Prefer Managed Infrastructure Over Custom-Built Undifferentiated Capability

**Statement:** Where a managed cloud service or reputable commercial product meets a non-differentiating requirement (e.g., message queuing, object storage, secrets management), it is preferred over a custom-built equivalent.

**Rationale:** Paivo's competitive differentiation is orchestration logic and rail coverage, not infrastructure operations. Engineering capacity spent maintaining undifferentiated infrastructure is capacity not spent on the ledger, reconciliation, and rail adapters that actually move the business.

**Implications:** Technology standards (see [`technology-standards.md`](../04-phase-d-technology-architecture/technology-standards.md)) default to managed services with a documented exceptions process for cases where control or cost genuinely requires custom build.

## 12. Architecture Decisions Are Recorded, Not Tribal

**Statement:** Any decision that is expensive to reverse, affects more than one team, or trades off a stated principle must be captured as a formal Architecture Decision Record (ADR) and reviewed by the ARB.

**Rationale:** The current point-to-point architecture's biggest hidden cost is undocumented tribal decisions that later engineers could not safely change because nobody could reconstruct why they were made.

**Implications:** See [`adrs/`](../adrs/) for the ADR log and format. ADRs are immutable once accepted; a reversal is a new ADR that supersedes the old one, not an edit.

---
*Fictional case study — see [README](../README.md) for full disclaimer.*
