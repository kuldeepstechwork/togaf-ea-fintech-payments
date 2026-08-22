# Implementation Governance Framework

## Purpose

Phase G governs how the target architecture defined in Phases B-E is actually built, ensuring delivery teams stay within the approved architecture without turning the ARB into a bottleneck for routine engineering work. This document defines the compliance checkpoints, the relationship to the Architecture Contract process, and how non-compliance is handled when it's found.

## Compliance Checkpoints

| Checkpoint | When | Owner | What's Verified |
|---|---|---|---|
| Architecture Contract approval | Before build starts on any new SBB or interface | ARB | Proposal traces to a stated requirement, states principle impact, and fits the reference architecture (or has an approved waiver) |
| Adapter contract test gate | Every CI run, merge-blocking | Platform Engineering (tooling), ARB (defines the contract) | New/changed adapter code honors the standard adapter interface, including idempotency and failure-mode behavior |
| PCI-DSS scope review | Before any change that touches cardholder data flow | Security Architect + Head of Risk & Compliance | Change does not expand PCI-DSS scope beyond the approved boundary (vault + card adapter) |
| Wave go/no-go review | Before each migration wave cutover | ARB | Wave exit criteria from [`migration-roadmap.md`](../06-phase-f-migration-planning/migration-roadmap.md) are met |
| ISO 20022 compliance validation | Before each rail's transformation logic goes live, and at each ISO schema version update | Head of Risk & Compliance, with external auditor liaison | Message output validates against the current ISO 20022 schema and the regulator's specific structured-messaging requirements |
| Quarterly architecture health audit | Quarterly | Chief Architect | Principle compliance across all shipped SBBs; exception register review (see [`04-phase-d-technology-architecture/technology-standards.md`](../04-phase-d-technology-architecture/technology-standards.md)) |

## Relationship to Architecture Contracts

Every checkpoint above that gates a delivery team's work is backed by a submitted Architecture Contract (defined and exemplified in [`architecture-contracts.md`](architecture-contracts.md)). The contract is the unit of governance: it is what gets reviewed, approved, and — if the delivery team deviates from what was approved — what non-compliance is measured against.

## Non-Compliance Handling

Non-compliance is treated in three tiers, deliberately calibrated so that governance responds proportionally rather than treating every deviation as a crisis:

1. **Tier 1 — Unintentional drift (e.g., a team's implementation diverges slightly from its approved contract due to a discovered technical constraint).** Handled through a contract amendment: the team documents what changed and why, the ARB reviews at the next standing session, and the amendment is either approved or the team is directed to remediate. No escalation beyond normal ARB process.
2. **Tier 2 — Undisclosed deviation discovered after the fact (e.g., a quarterly health audit finds an adapter with rail-specific logic leaking into the orchestration core, undocumented).** Triggers a formal non-compliance finding logged against the responsible team, a mandatory remediation plan with a committed date, and a follow-up audit to confirm remediation. The Head of Platform Engineering is accountable for the remediation plan's execution.
3. **Tier 3 — Deviation that creates active regulatory or security risk (e.g., cardholder data found flowing into an out-of-scope system).** Immediate escalation to the Executive Sponsor and Head of Risk & Compliance, work on the affected component paused pending a fix, and a mandatory root-cause review at the next ARB session to determine whether the checkpoint that should have caught this needs strengthening.

To date in this program, the framework has recorded two Tier 1 amendments (both related to the reconciliation engine's data store exception, see [`technology-standards.md`](../04-phase-d-technology-architecture/technology-standards.md)) and zero Tier 2 or Tier 3 findings, which the Chief Architect attributes largely to the mandatory "principle impact" section in the Architecture Contract template catching issues before build rather than after.

## Governance Is Not a Gate on Everything

Consistent with the Preliminary Phase governance design, teams retain full decision rights over implementation details that do not touch a stated principle, cross a service boundary, or affect PCI-DSS scope. The purpose of this framework is to protect the small number of decisions that are expensive to get wrong or expensive to reverse — not to review every line of code or every internal design choice a delivery team makes within its own service boundary.

---
*Fictional case study — see [README](../README.md) for full disclaimer.*
