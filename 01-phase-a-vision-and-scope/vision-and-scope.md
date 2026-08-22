# Architecture Vision and Scope

## Problem Statement

Paivo Payments connects merchants to payment rails — UPI, cards, wallets, net banking — through integrations built point-to-point: one integration per merchant per rail, often per PSP. This model scaled acceptably while Paivo operated on a small number of rails in a single market. It now fails Paivo on three fronts simultaneously:

1. **Integration cost does not amortize.** Each new PSP partnership or rail expansion requires bespoke integration work regardless of the volume it will carry, so the unit economics of smaller or newer rails never improve with scale.
2. **Reconciliation is fragmented.** Each rail integration reconciles independently against its own PSP statement format. There is no single ledger of truth, so settlement discrepancies surface late, in finance's monthly close, rather than in near-real time, and root-causing them means correlating records across systems that were never designed to be correlated.
3. **The architecture cannot meet the ISO 20022 mandate as-is.** The regulator has set a compliance deadline for ISO 20022 structured financial messaging. Under the current point-to-point model, meeting that deadline means building message transformation logic independently into every existing integration — an approach whose cost scales with the number of integrations, not with the size of the problem.

Leadership's mandate to this architecture function: define a target-state platform architecture that breaks the linear cost relationship between "rails supported" and "integration effort," establishes a single ledger of truth for reconciliation, and provides a single point of ISO 20022 compliance — on a timeline that meets the regulatory deadline.

## Target-State Vision

Paivo moves from N×M point-to-point rail integrations (N merchants × M rails) to a **central payment orchestration hub** that all merchants and all rails connect through once. The hub owns transaction routing, retries, idempotency, and writes every transaction to a single ledger service. Rail- and PSP-specific protocol differences are isolated to individually replaceable adapters. A shared message transformation layer sits between adapters and the orchestration core, translating rail-native formats to and from a canonical internal schema and, where the wire protocol requires it, to ISO 20022 structured messages — built once, not per adapter.

This target state directly resolves the three problem drivers: onboarding a new rail becomes a bounded adapter-build exercise instead of a bespoke integration; reconciliation runs against one ledger instead of N fragmented processes; and ISO 20022 compliance is achieved by extending the shared transformation layer once, not touching every existing integration.

## Scope Boundaries

### In Scope

- Design and build of the central orchestration hub (routing, retries, idempotency, orchestration state management).
- The single ledger of truth service and its reconciliation engine.
- Rail adapters for Paivo's current live rails: UPI, card networks (via existing acquiring PSPs), major wallets, and net banking.
- The ISO 20022 message transformation layer, scoped to the message types the regulatory mandate covers (payment initiation and payment status reporting).
- Migration of existing merchant traffic from point-to-point integrations to the orchestration hub, phased by rail and merchant tier.
- PCI-DSS scope redesign as a consequence of consolidating card data handling into the hub.

### Explicitly Out of Scope (and Why)

- **Cross-border payment rail integrations themselves.** The orchestration hub is being designed to *accommodate* cross-border expansion as a future adapter, but building the actual cross-border rail connections (correspondent banking, FX conversion, cross-border compliance screening) is a separate, subsequent program. Including it here would couple a hard regulatory deadline (ISO 20022) to a much less certain business initiative (cross-border go-to-market timing), which the ARB assessed as an unacceptable schedule risk to the regulatory commitment.
- **Merchant-facing product changes** (checkout UX, merchant dashboard redesign, pricing model changes). This program is an infrastructure and compliance re-architecture; merchant-facing product work has its own roadmap and owner, and bundling it here would blur accountability for the regulatory deadline.
- **Replacement of Paivo's core merchant onboarding and KYC system.** That system is out of scope because it does not sit in the payment execution path this program is re-architecting; a dependency is noted where the orchestration hub needs merchant identifiers from it, but no rebuild is planned.
- **Full data warehouse / analytics platform rebuild.** The ledger will emit events to the existing analytics pipeline; rebuilding that pipeline's own architecture is deferred to a separate initiative so this program's scope stays bounded to the payment execution and reconciliation path.
- **Decommissioning legacy point-to-point integrations on day one.** Legacy integrations are retired progressively as traffic migrates (see [`06-phase-f-migration-planning/`](../06-phase-f-migration-planning/)); a big-bang cutover was considered and rejected as too high-risk for a business processing live merchant funds.

## Success Metrics

| Metric | Baseline (As-Is) | Target (18 Months Post-Launch) |
|---|---|---|
| Time to onboard a new rail/PSP integration | 10–14 weeks | 2–3 weeks (adapter build against a stable interface) |
| Reconciliation discrepancies requiring manual investigation | ~140/month across rails | <20/month, detected within 4 hours of settlement |
| ISO 20022 message compliance coverage | 0% (no structured messaging) | 100% of in-scope message types by regulatory deadline |
| Orchestration core availability | N/A (no shared core exists) | 99.95% (Tier-0 SLA per architecture principle 8) |
| PCI-DSS audit scope (systems in scope) | 11 systems store or touch raw PAN | ≤3 systems (vault + minimum processing path) |
| Cost per new rail integration (fully loaded) | ~$180K per rail (illustrative) | ~$45K per rail (illustrative), per [`business-case.md`](business-case.md) |

---
*Fictional case study — see [README](../README.md) for full disclaimer.*
