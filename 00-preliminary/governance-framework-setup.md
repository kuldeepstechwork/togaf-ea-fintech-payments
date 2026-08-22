# Governance Framework Setup

## Purpose

Before Phase A scoping began, the architecture function stood up a governance model sized for Paivo's scale — large enough to give the orchestration program real teeth against a regulatory deadline, small enough not to become the bottleneck that point-to-point delivery teams historically used as a reason to route around architecture entirely. This document defines how the Architecture Review Board (ARB) is constituted, how decisions move through it, and how principle non-compliance is escalated and resolved.

## Architecture Review Board (ARB) Constitution

**Voting members (5):**
- Chief Architect (chair, tie-break vote)
- Head of Platform Engineering
- Head of Risk & Compliance (regulatory sign-off authority)
- Head of Payments Product
- Principal Security Architect

**Standing invitees (non-voting, attend by topic):**
- Engineering leads for the domain under review (ledger, adapters, reconciliation)
- Finance representative for cost-impacting decisions
- External auditor liaison, invited quarterly and for any ISO 20022 / PCI-DSS-scoped decision

**Rationale for a five-person voting board:** A larger board was considered and rejected — Paivo evaluated an 9-person cross-functional board modeled on its existing change advisory board, but rejected it because quorum logistics were already the single biggest complaint about the legacy CAB process, adding a median 9 calendar days to any decision. A two- or three-person board was also rejected as too narrow to carry regulatory and security sign-off without a separate escalation step for every compliance-adjacent decision. Five voting members with defined regulatory and security seats gives every decision category (technical, commercial, compliance) a standing vote without requiring ad hoc escalation for the majority of program decisions.

## Cadence

- **Standing ARB session:** weekly, 60 minutes, decisions logged the same day.
- **Architecture Contract review:** on submission, target turnaround 5 business days (see [`07-phase-g-implementation-governance/architecture-contracts.md`](../07-phase-g-implementation-governance/architecture-contracts.md)).
- **Principle compliance audit:** monthly self-audit by the architecture function against the twelve principles in [`architecture-principles.md`](architecture-principles.md); results reported to the ARB and to the program's Executive Sponsor.
- **Quarterly deep review:** full program health check against the Phase F roadmap, with the external auditor liaison present given the ISO 20022 deadline.

## Decision Rights and Escalation Path

1. **Delivery-team level:** Teams may make implementation decisions that do not touch a stated architecture principle or cross a service boundary without ARB involvement — this is deliberate, to avoid architecture becoming a bottleneck for routine engineering.
2. **Architecture Contract required:** Any decision that (a) introduces a new solution building block, (b) changes an interface consumed by more than one team, (c) touches PCI-DSS scope, or (d) deviates from an approved technology standard requires a submitted Architecture Contract reviewed by the ARB before build starts.
3. **Waiver process:** A team that believes a principle should not apply to their specific case submits a waiver request with the trade-off explicitly quantified (cost, risk, timeline). The ARB votes; a waiver is time-boxed (default 2 quarters) and re-reviewed, it is never a permanent exception without a follow-up ADR.
4. **Escalation on disagreement:** If a delivery team and the ARB cannot reach agreement, the matter escalates to the Executive Sponsor (the CTO) with both positions documented. This has been invoked once in the program's first two quarters, over the orchestration hub vs. mesh topology decision recorded in [ADR-001](../adrs/adr-001-orchestration-hub-vs-mesh.md).
5. **Regulatory veto:** The Head of Risk & Compliance holds a standing veto on any decision that would put the ISO 20022 or PCI-DSS deadline at risk, exercisable without a full ARB vote, subject to ratification at the next standing session.

## Principles Enforcement

The ARB does not enforce principles by memory. Every Architecture Contract template (see Phase G) includes a mandatory "principle impact" section requiring the submitting team to state explicitly which of the twelve principles the proposal touches and how. A submission that is silent on this section is returned unreviewed. This single procedural gate has proven, in comparable programs, to catch the majority of principle violations before they reach a vote — reviewers spend their time evaluating a stated trade-off rather than discovering an unstated one.

## Governance Artifacts Produced in This Phase

- The twelve architecture principles ([`architecture-principles.md`](architecture-principles.md)), ratified by unanimous ARB vote.
- The ARB charter and escalation path (this document), ratified by the Executive Sponsor.
- The Architecture Contract template, carried forward into Phase G.

---
*Fictional case study — see [README](../README.md) for full disclaimer.*
