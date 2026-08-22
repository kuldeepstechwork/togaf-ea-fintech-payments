# Vendor Evaluation: Orchestration & Tokenization Platform Components

*All vendor names below are invented for this fictional case study and do not refer to any real company or product.*

## Scope of This Evaluation

Two separate buy decisions required vendor evaluation: (1) whether to buy a commercial payment orchestration platform rather than build the orchestration core in-house, and (2) which tokenization/vault vendor to use for PCI-DSS scope isolation. This document covers both, scored against weighted criteria by the ARB.

## Decision 1: Orchestration Core — Build vs. Buy

| Vendor / Option | Weight-Adjusted Score | Summary |
|---|---|---|
| **Build in-house** | **7.9 / 10** | Selected |
| Vendor A — "Meridian Orchestrate" | 6.3 / 10 | Runner-up |
| Vendor B — "FlowGate Platform" | 5.8 / 10 | Rejected |
| Vendor C — "PayMesh Connect" | 5.45 / 10 | Rejected |

### Scoring Criteria (weighted)

| Criterion | Weight | Build In-House | Vendor A (Meridian) | Vendor B (FlowGate) | Vendor C (PayMesh) |
|---|---|---|---|---|---|
| Fit to Paivo's idempotency/retry requirements | 20% | 9 | 6 | 5 | 5 |
| ISO 20022 transformation extensibility | 15% | 9 | 7 | 6 | 4 |
| Time to first production rail | 15% | 5 | 8 | 7 | 8 |
| Total cost of ownership (3-year) | 15% | 6 | 6 | 7 | 8 |
| Vendor lock-in / substitutability (principle 9) | 15% | 10 | 4 | 5 | 3 |
| Auditability for PCI-DSS / regulator review | 10% | 9 | 6 | 5 | 5 |
| Operational maturity / support SLA | 5% | 6 | 8 | 6 | 6 |
| Multi-region active-active support | 5% | 8 | 7 | 5 | 4 |

### Rationale

Build in-house won on the criteria the ARB weighted most heavily: fit to Paivo's specific idempotency and retry requirements (no evaluated vendor's retry semantics matched principle 4's platform-enforced idempotency guarantee without significant customization that would have eroded the vendor's own upgrade path), and vendor lock-in / substitutability, where every commercial option would have made the orchestration core itself — the most architecturally central component — dependent on a single vendor's roadmap, directly violating principle 9.

**Vendor A (Meridian Orchestrate)** was the strongest commercial runner-up, scoring well on time-to-first-production-rail and ISO 20022 extensibility, but lost primarily on lock-in risk: Meridian's routing and idempotency logic is proprietary and not portable, meaning a future vendor switch would require rebuilding orchestration logic from scratch rather than replacing a bounded adapter — exactly the coupling risk principle 9 exists to avoid.

**Vendor B (FlowGate Platform)** scored reasonably on cost but had the weakest fit to Paivo's specific idempotency requirements among the three, requiring what the evaluation team estimated as 40%+ custom extension work to meet principle 4 — at that point, the vendor's cost advantage over building in-house was assessed to evaporate.

**Vendor C (PayMesh Connect)** had the best raw time-to-market and lowest sticker cost but the weakest auditability and lock-in scores; it was ruled out primarily because its architecture would not give the Head of Risk & Compliance the code-level audit access the regulatory deadline requires.

**Accepted trade-off:** building in-house costs more time to first production rail (assessed as 5/10 vs. 7-8/10 for vendor options) — a real schedule risk against the ISO 20022 deadline that the migration roadmap explicitly manages by front-loading orchestration core development in Wave 0 (see [`06-phase-f-migration-planning/migration-roadmap.md`](../06-phase-f-migration-planning/migration-roadmap.md)).

## Decision 2: Tokenization / PCI Vault — Vendor Selection

| Vendor / Option | Weight-Adjusted Score | Summary |
|---|---|---|
| **Vendor D — "SecureVault Pro"** | **7.95 / 10** | Selected |
| Build in-house PCI-DSS Level 1 vault | 6.2 / 10\* | Rejected |
| Vendor E — "TokenShield" | 7.0 / 10 | Runner-up |

### Scoring Criteria (weighted)

| Criterion | Weight | Vendor D (SecureVault Pro) | Vendor E (TokenShield) | Build In-House |
|---|---|---|---|---|
| Existing PCI-DSS Level 1 certification | 25% | 9 | 8 | 3 (would need to attain) |
| Integration effort with orchestration core | 20% | 8 | 6 | 9 |
| Cost (3-year TCO) | 20% | 7 | 8 | 4 |
| Detokenization latency (hot-path impact) | 15% | 8 | 6 | 8 |
| Vendor substitutability | 10% | 7 | 6 | 10 |
| Breach history / security track record | 10% | 8 | 7 | n/a |

\*Build In-House has no breach history to score (n/a); its weighted total is computed over the applicable 90% of weight and rescaled to a 0-10 basis, rather than treating the missing criterion as a zero.

### Rationale

For tokenization specifically — unlike the orchestration core — the "buy" case was strong: attaining and maintaining PCI-DSS Level 1 certification in-house is a substantial, ongoing compliance cost (illustrative estimate: ~$650K first-year certification cost plus recurring annual assessment) that a certified vendor has already absorbed and amortized across its customer base. **Vendor D (SecureVault Pro)** was selected over **Vendor E (TokenShield)** primarily on detokenization latency — TokenShield's architecture added an estimated 35ms to the hot-path detokenization call the card adapter occasionally requires, versus SecureVault Pro's 12ms, which matters given the orchestration core's overall latency budget. Building in-house was rejected principally on the certification cost and timeline: achieving Level 1 certification independently was assessed to take 9-12 months, directly conflicting with the regulatory deadline this program is organized around.

**Accepted trade-off:** using a third-party vault means Paivo's card rail data flow has an external dependency for detokenization, which the reconciliation and dispute-handling processes must design around (e.g., cached, time-boxed detokenized access for active disputes rather than repeated vault calls) — captured in [ADR-005](../adrs/adr-005-pci-dss-scope-isolation-strategy.md).

---
*Fictional case study — see [README](../README.md) for full disclaimer.*
