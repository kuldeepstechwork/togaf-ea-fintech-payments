# ADR-005: PCI-DSS Scope Isolation Strategy

## Status
Accepted

## Context

Under the as-is architecture, raw cardholder data (PAN) is stored or handled in 11 separate systems, each independently in PCI-DSS scope, driving significant recurring audit cost (~$38K per system per year, illustrative) and a large breach blast radius. Architecture principle 5 (data minimization at the boundary) requires sensitive data to be tokenized at the earliest point of ingestion. This ADR defines the specific strategy for isolating PCI-DSS scope in the target architecture.

## Decision

Tokenize cardholder data at the **card rail adapter**, the earliest point it enters Paivo's systems, using a certified third-party tokenization vault (Vendor D, "SecureVault Pro" — see [`05-phase-e-opportunities-and-solutions/vendor-evaluation.md`](../05-phase-e-opportunities-and-solutions/vendor-evaluation.md)). Every system downstream of the card adapter — orchestration core, ledger, reconciliation engine, transformation layer, all other adapters — operates exclusively on tokens. Only the vault itself, and the card adapter during the narrow window of initial tokenization, ever handle raw PAN. Detokenization for legitimate business purposes (dispute evidence, regulator request) is a narrow, audited, time-boxed vault API call, never a bulk export or a cached raw-PAN store anywhere else.

## Alternatives Considered

**1. Tokenize at the orchestration core instead of at the adapter.** This was considered because it would centralize tokenization logic in one place rather than requiring the card adapter specifically to integrate with the vault. It was rejected because it would require raw PAN to transit from the card adapter through to the orchestration core before tokenization occurs, expanding PCI-DSS scope to include that transit path and the orchestration core itself — directly undermining the scope-reduction goal that is this decision's entire purpose. Tokenizing as early as possible (at the adapter, before the data ever reaches the shared core) is what actually keeps the core out of scope.

**2. Build an in-house PCI-DSS Level 1 certified vault rather than buying a third-party tokenization service.** This was evaluated in the vendor evaluation and gives Paivo full control over the vault's implementation and data residency, with no external dependency for detokenization. It was rejected primarily on timeline: achieving Level 1 certification independently was assessed at 9-12 months, which conflicts directly with the regulatory deadline this program is organized around, and secondarily on cost (~$650K illustrative first-year certification cost plus ongoing annual assessment) that a vendor who has already amortized that cost across many customers can offer more cheaply.

**3. Selected: tokenize at the card adapter using a certified third-party vault.** Introduces an external vendor dependency for detokenization latency and requires designing dispute-handling and other detokenization-dependent workflows around that dependency, but achieves the scope reduction goal immediately and meets the regulatory timeline that in-house certification could not.

## Consequences

**Positive:** PCI-DSS audit scope reduces from 11 systems to a projected 2-3 (the vault itself, the card adapter during tokenization, and any narrowly-scoped detokenization-dependent workflow), directly reducing both audit cost and breach blast radius. The orchestration core, ledger, and reconciliation engine — the platform's most central and highest-traffic components — are provably out of PCI-DSS scope, simplifying their operation and audit posture materially.

**Negative (accepted trade-off):** Detokenization now has an external vendor dependency and measured latency cost (12ms per call for the selected vendor, per the vendor evaluation) for any workflow that legitimately needs raw PAN, such as dispute evidence retrieval — these workflows must be designed to minimize detokenization calls (e.g., time-boxed cached access during an active dispute investigation rather than repeated per-view calls) rather than assuming raw data is cheaply available. Paivo's PCI-DSS compliance posture for the tokenized-data path is now partially dependent on the vendor's own certification remaining current — tracked as a standing item in the technology standards exceptions register with a mandatory annual vendor certification re-verification.

## Stakeholders Affected
ARB (decision owner), Principal Security Architect (scope design owner), Head of Risk & Compliance (PCI-DSS attestation owner), Card Rail Adapter team (tokenization integration), Dispute-handling process owners (detokenization-dependent workflow design).

---
*Fictional case study — see [README](../README.md) for full disclaimer.*
