# Stakeholder Map

## Purpose

This map identifies every stakeholder group whose concerns materially shape the orchestration platform architecture, states their primary concern in their own terms, and assigns RACI across the program's major decision categories. It was compiled during Phase A stakeholder interviews and is re-validated at each quarterly ARB deep review.

## Stakeholder Concerns

| Stakeholder | Primary Concern |
|---|---|
| CEO / Board | Program delivers the ISO 20022 deadline without disrupting live merchant payment volume; cost stays within the approved business case envelope |
| CTO (Executive Sponsor) | Architecture is technically sound, does not create a new single point of failure that undermines the availability SLA merchant contracts already require |
| Head of Risk & Compliance | ISO 20022 and PCI-DSS requirements are met with auditable evidence, not just functional coverage |
| Head of Payments Product | Merchant experience (settlement speed, dispute handling, rail coverage) does not regress during migration |
| Head of Platform Engineering | Delivery teams can build against a stable, well-documented adapter interface; migration does not require an unsustainable parallel-run burden |
| Principal Security Architect | PCI-DSS scope is genuinely reduced, not just re-labeled; sensitive data handling is centralized and auditable |
| Finance / Controller | Reconciliation discrepancies are reduced measurably; month-end close time shortens; TCO tracks the approved business case |
| Merchant-facing Account Management | Existing merchants experience no downtime or settlement delay during migration; new rail onboarding gets faster, which is a sales talking point |
| PSP / Rail Partners | Integration contract (API surface, SLAs, dispute process) with Paivo is stable and clearly versioned during the transition |
| External Auditor | Evidence trail for ISO 20022 and PCI-DSS compliance is complete, timestamped, and independently verifiable |
| Existing Point-to-Point Integration Engineers | Their integrations are retired on a plan that doesn't leave them holding unsupported legacy code indefinitely, and there's a clear path for their skills into the new platform |

## RACI — Program-Level Decisions

R = Responsible, A = Accountable, C = Consulted, I = Informed

| Decision Area | CEO/Board | CTO | Risk & Compliance | Payments Product | Platform Engineering | Security Architect | Finance |
|---|---|---|---|---|---|---|---|
| Program approval & budget | A | R | C | C | C | C | C |
| Target-state architecture (Phase B–D) | I | A | C | C | R | C | I |
| ISO 20022 message scope & compliance sign-off | I | C | A/R | I | C | I | I |
| Vendor / PSP orchestration platform selection | I | A | C | C | R | C | C |
| PCI-DSS scope redesign | I | C | A | I | R | R | I |
| Migration roadmap & cutover sequencing | I | A | C | R | R | I | C |
| Business case & ROI tracking | A | C | I | I | C | I | R |
| Merchant communication during migration | I | I | I | A/R | I | I | I |
| Architecture Contract approval (ongoing) | I | I | C | C | R (ARB member) | R (ARB member) | I |

## Notes on Contested Concerns

Two stakeholder concerns were in direct tension during Phase A and required explicit ARB resolution rather than being smoothed over:

- **Platform Engineering wanted a slower, more thoroughly tested migration; Payments Product wanted rail coverage parity as fast as possible to support sales commitments already made to prospective enterprise merchants.** Resolved by the wave-based migration roadmap in [`06-phase-f-migration-planning/migration-roadmap.md`](../06-phase-f-migration-planning/migration-roadmap.md), which sequences by risk (lowest-volume rail first) rather than by sales priority, with an explicit executive-approved exception process if a specific merchant deal requires reordering.
- **Security Architect wanted all PSP adapters built in-house for full code-level audit control; Platform Engineering wanted to evaluate commercial orchestration middleware to save build time against the regulatory deadline.** Resolved via the vendor evaluation in [`05-phase-e-opportunities-and-solutions/vendor-evaluation.md`](../05-phase-e-opportunities-and-solutions/vendor-evaluation.md), which scored vendor options partly on auditability, not just cost and speed.

---
*Fictional case study — see [README](../README.md) for full disclaimer.*
