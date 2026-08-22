# Architecture Contracts

## What an Architecture Contract Contains

An Architecture Contract is the formal agreement between the architecture function (represented by the ARB) and a delivery team, defining the scope, standards, and acceptance criteria a piece of work must meet before it is considered architecturally approved. Per TOGAF's treatment of architecture contracts as the bridge between architecture definition (Phases A-D) and governed delivery (Phase G), every contract at Paivo contains the following mandatory sections:

1. **Requesting team and business owner** — who is accountable for the delivered component.
2. **Traceability** — which stated business/regulatory requirement (Phase A) and which gap analysis entry (Phase E) this work addresses. A contract that cannot cite a specific traced requirement is returned unreviewed, per architecture principle 10.
3. **Scope of the solution building block** — precisely what is being built or bought, and its boundary (what it explicitly does not do).
4. **Principle impact statement** — which of the twelve architecture principles this proposal touches, and how it complies or requires a waiver. Mandatory; silent submissions are returned unreviewed per the governance framework.
5. **Interface commitments** — for any component with more than one consumer, the interface contract it commits to (e.g., the standard adapter interface), including versioning and backward-compatibility commitments.
6. **Non-functional requirements** — availability tier, latency budget, data classification/PCI-DSS scope status.
7. **Acceptance criteria** — the specific, testable conditions the ARB will check before sign-off (contract tests passing, load test results, security review outcome).
8. **Deviation and amendment process** — acknowledgment that any deviation from the above during build must be reported per the Tier 1 non-compliance process in [`governance-framework.md`](governance-framework.md).
9. **Approval record** — ARB vote outcome, date, and any conditions attached to approval.

## Worked Example: UPI Rail Adapter Architecture Contract

**Requesting team:** Rail Adapters Squad
**Business owner:** Head of Platform Engineering
**Date submitted:** 2027-04-02
**ARB review date:** 2027-04-09

### Traceability
Addresses gap analysis entry "New rail onboarding cost is high and unpredictable" (priority score 8) and is Wave 3 of the migration roadmap. Regulatory driver: UPI transaction volume must be ISO 20022-compliant by the mandated deadline; UPI is Paivo's highest-transaction-count rail.

### Scope
Build a UPI rail adapter implementing the standard adapter interface (`initiate`, `status`, `settlement_file`), integrating with Paivo's existing UPI PSP relationship. Explicitly excludes: any change to the PSP commercial relationship or fee structure (owned by Payments Product, not this contract); excludes UPI Autopay/mandate functionality, which is a distinct capability not in this program's scope.

### Principle Impact Statement
- **Principle 2 (rail-agnostic core):** Confirmed — all UPI-specific logic (its specific callback and status-polling model) is contained entirely within the adapter; no core changes required.
- **Principle 4 (idempotency by design):** Confirmed — adapter honors the orchestration core's supplied idempotency key and additionally de-duplicates on UPI's own transaction reference as a defense-in-depth measure, since UPI's PSP webhook delivery has known at-least-once semantics.
- **Principle 8 (availability tiering):** Adapter is Tier-1 (99.9%, active-passive), consistent with standard adapter tiering; no waiver needed.

### Interface Commitments
Implements the standard adapter interface v2.1. Adapter team commits to a 2-release deprecation window for any future adapter-side change that would affect the interface's contract-test expectations (though the interface itself is owned by the orchestration core team, not this adapter).

### Non-Functional Requirements
- Availability Tier: Tier-1, 99.9%, active-passive.
- Latency budget: initiate() call must return within 800ms p99 (UPI PSP's own SLA is 600ms; 200ms budget reserved for transformation layer overhead).
- Data classification: no raw PAN or account data handled by this adapter (UPI transaction identifiers are not PCI-DSS-scoped data); standard data handling applies, no vault integration required.

### Acceptance Criteria
- Passes full adapter contract test suite, including simulated PSP timeout, duplicate webhook delivery, and partial-failure scenarios.
- Dual-run against legacy UPI integration for minimum 4 weeks with discrepancy rate within the Wave 3 exit criteria defined in the migration roadmap.
- Load-tested to 3x current peak UPI transaction volume (headroom for projected growth).

### Deviation and Amendment Process
Standard Tier 1/2/3 process per [`governance-framework.md`](governance-framework.md) applies. Any change to the 800ms latency budget or the exclusion of Autopay/mandate functionality requires a contract amendment, not a unilateral team decision.

### Approval Record
Approved unanimously by the ARB on 2027-04-09, with one condition: the dual-run discrepancy monitoring dashboard must be reviewed by Finance Operations before cutover sign-off, added at the Finance representative's request during review.

---
*Fictional case study — see [README](../README.md) for full disclaimer.*
