# Technology Standards

## Purpose

This catalog defines the approved technology stack and standards for the orchestration platform program, and the process for requesting an exception. It operationalizes architecture principle 11 (prefer managed infrastructure over custom-built undifferentiated capability) and principle 9 (vendor and rail substitutability).

## Approved Technology Stack

| Layer | Standard | Notes |
|---|---|---|
| Orchestration core & adapters runtime | Managed container orchestration (Kubernetes-based managed service) | Chosen for multi-region active-active deployment support required by Tier-0 SLA |
| Inter-service messaging | Managed event streaming (Kafka-compatible managed service) | Adapter-to-core transaction events and ledger event sourcing |
| Ledger persistence | Managed relational database with multi-region replication, strict consistency mode | Ledger requires ACID guarantees; eventual-consistency stores were evaluated and rejected for this store specifically (though used elsewhere) |
| Reconciliation & analytics store | Managed columnar/analytical data store | Read-heavy, high-cardinality matching workloads |
| Idempotency key store | Managed low-latency key-value store with TTL support | Sub-10ms lookup requirement on the payment initiation hot path |
| Secrets & key management | Managed KMS / secrets manager | No application-managed secrets storage permitted |
| Tokenization / PCI vault | Certified third-party tokenization service or PCI-DSS Level 1 certified in-house vault (see PCI-DSS scope note below) | Must hold current PCI-DSS Level 1 attestation |
| Message transformation runtime | Standard service framework, language-agnostic requirement | ISO 20022 schema validation library must be independently certified against the current ISO 20022 schema release |
| Observability | Standard managed logging, metrics, and distributed tracing stack | Mandatory: every cross-service call in the orchestration path must carry a trace ID for incident correlation |
| CI/CD | Standard managed pipeline platform with mandatory contract testing gate for adapter interfaces | Adapter contract tests are a merge-blocking gate, not advisory |

## Standards

### ISO 20022 Compliance
- All in-scope message types (payment initiation, payment status reporting) validated against the current ISO 20022 schema version before the transformation layer is considered release-ready.
- Schema version upgrades are tracked as a standing agenda item at the quarterly ARB deep review, since regulatory bodies periodically issue updates.
- The transformation layer maintains a compatibility test suite that replays a representative corpus of historical (anonymized, synthetic) transactions against each schema version to catch regressions before rollout.

### PCI-DSS Scope Boundaries
- **In scope:** the token vault, the card rail adapter (until tokenization occurs), and any system that stores or displays a detokenized PAN for a defined, audited business purpose (e.g., dispute evidence retrieval).
- **Explicitly out of scope by design:** the orchestration core, ledger, reconciliation engine, message transformation layer, and all non-card adapters — none of these persist or process raw PAN under the to-be architecture, per architecture principle 5.
- Any proposal that would cause PAN data to flow into an out-of-scope system requires ARB review and Security Architect sign-off before implementation, treated as a principle violation by default.

### Availability & Deployment Standards
- Tier-0 services (orchestration core, ledger): active-active multi-region, documented RTO ≤ 5 minutes, RPO ≤ 0 (synchronous replication for ledger writes).
- Tier-1 services (adapters): active-passive acceptable, documented RTO ≤ 30 minutes.
- Tier-2 services (reporting/analytics): best-effort within a 4-hour RTO.

## Exceptions Process

A team seeking to deviate from this catalog — using a non-standard database, a different messaging technology, or a runtime not on this list — submits an Architecture Contract (see [`07-phase-g-implementation-governance/architecture-contracts.md`](../07-phase-g-implementation-governance/architecture-contracts.md)) that must state:

1. Which standard is being deviated from and why the standard does not fit the specific use case.
2. The quantified trade-off (cost, operational burden of supporting a non-standard technology, vendor lock-in risk per principle 9).
3. A time-boxed review date (default: 2 quarters) after which the exception is either formalized as a catalog addition or the team migrates back to standard.

Exceptions are logged in a standing register reviewed at each quarterly ARB deep review; an exception that is never revisited past its review date is treated as a governance gap and escalated to the Executive Sponsor.

## Notable Exception Granted To Date

The reconciliation engine was granted an exception to use a specialized time-series-optimized store rather than the standard analytical data store, because its matching workload pattern (continuous, low-latency stream joins against a rolling window) was a poor fit for the standard batch-oriented analytical store. This was reviewed and approved by the ARB with a 2-quarter re-review date, and re-approved as a permanent catalog addition after the review confirmed the fit.

---
*Fictional case study — see [README](../README.md) for full disclaimer.*
