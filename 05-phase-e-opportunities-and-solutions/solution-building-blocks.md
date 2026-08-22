# Solution Building Blocks

## Purpose

This document decomposes the required capabilities identified in Phase B ([`capability-map.md`](../02-phase-b-business-architecture/capability-map.md)) into discrete solution building blocks (SBBs) — units of the platform that are individually buildable or buyable, each traceable to a stated business or regulatory requirement per architecture principle 10.

## Solution Building Block Inventory

| SBB | Traces to Requirement | Build or Buy | Notes |
|---|---|---|---|
| Orchestration Core (routing, idempotency, retry) | Vision & Scope: eliminate linear integration cost; Principle 2 | Build | Core differentiator; no commercial product matches Paivo's specific routing and idempotency requirements without heavy customization — see [`vendor-evaluation.md`](vendor-evaluation.md) |
| Ledger Service | Principle 1: single ledger of truth | Build | Financial system of record; build in-house for full audit control per Security Architect concern in [`stakeholder-map.md`](../01-phase-a-vision-and-scope/stakeholder-map.md) |
| Message Transformation Layer (ISO 20022 + rail-native) | Regulatory mandate; Principle 6 | Buy (schema validation library) + Build (transformation orchestration) | Certified ISO 20022 schema validation library bought, not built, per principle 11; transformation orchestration logic around it is built in-house |
| Rail Adapters (UPI, Card, Wallet, Net Banking) | Vision & Scope: bounded rail onboarding cost | Build, against a standard adapter interface | See adapter interface contract below |
| Token Vault / PCI Scope Boundary | Principle 5; PCI-DSS scope reduction | Buy (certified third-party tokenization service) | Evaluated against building in-house; buying was selected — see [`vendor-evaluation.md`](vendor-evaluation.md) for the trade-off |
| Reconciliation Engine | Vision & Scope: discrepancy detection within hours | Build | Matching logic is specific enough to Paivo's settlement file variety that commercial reconciliation tools evaluated did not clear the bar — see gap analysis |
| Idempotency Key Store | Principle 4 | Buy (managed key-value store) + Build (application logic) | Infrastructure bought per principle 11; idempotency semantics built in-house |
| Compliance Reporting Interface | Regulatory mandate; Head of Risk & Compliance concern | Build | Thin reporting layer over the ledger; no meaningful buy option given Paivo-specific regulatory format needs |

## Standard Adapter Interface Contract

Every rail adapter, regardless of rail, must implement the same contract to the orchestration core, so that onboarding a new rail is a bounded engineering task rather than a redesign of the core:

- `initiate(canonical_transaction) -> adapter_reference` — accepts a canonical transaction (already transformed by the message layer into whatever the adapter needs) and returns a rail-specific reference for tracking.
- `status(adapter_reference) -> canonical_status` — polls or receives a webhook for rail-native status and returns it mapped to the canonical status enumeration.
- `settlement_file() -> normalized_settlement_records` — exposes the rail's settlement data in a normalized schema the reconciliation engine consumes uniformly.
- Idempotency: every adapter must honor the idempotency key supplied by the orchestration core and guarantee it will not double-submit to the underlying PSP for a repeated key, even if the adapter's own process restarts.
- Adapters are contract-tested in CI against a mock harness that exercises the full interface, including failure and timeout scenarios, before being allowed into the deployment pipeline — this is the mechanism that makes principle 7 ("bounded, well-defined onboarding task") operational rather than aspirational.

## Sequencing Logic

Solution building blocks are not all built simultaneously. The Orchestration Core, Ledger Service, and Idempotency Key Store are built first because every other SBB depends on them. The Message Transformation Layer follows immediately given the regulatory deadline. Rail adapters are then built in migration-wave order (see [`06-phase-f-migration-planning/migration-roadmap.md`](../06-phase-f-migration-planning/migration-roadmap.md)), starting with the lowest-volume, lowest-risk rail so that early production issues are discovered against the smallest possible blast radius.

---
*Fictional case study — see [README](../README.md) for full disclaimer.*
