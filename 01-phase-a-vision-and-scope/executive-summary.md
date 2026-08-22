# Executive Summary: Payment Orchestration Platform

**Prepared for:** Paivo Payments Executive Committee and Board
**Program Sponsor:** CTO

## The Problem

Paivo connects merchants to payment rails through custom, one-off integrations — one per merchant, per rail, per PSP. That approach is now costing us in three ways: every new rail partnership costs roughly $180K to integrate regardless of how much volume it carries; reconciliation runs separately per rail with no single source of truth, so settlement errors surface in month-end close instead of same-day; and the regulator's ISO 20022 structured-messaging mandate cannot be met under this model without retrofitting every one of our 20 integrations individually — a $1.3M, high-risk undertaking with no shared path to compliance.

## The Recommendation

Build a central payment orchestration hub: one platform all merchants and rails connect through, with a single ledger of truth for reconciliation and one shared layer for ISO 20022 message compliance. Rail-specific complexity (PSP APIs, protocol quirks) is isolated into small, independently replaceable adapters so a PSP changing their API touches one component, not the whole platform.

## Cost

Illustrative 3-year TCO: **$17.5M to build and run the orchestration platform** versus **$8.4M to continue as-is** (which itself includes a forced $1.3M ISO 20022 retrofit spend that happens regardless of which path we take). The gap is front-loaded — Year 1 build cost is the majority of the delta — and it inverts by Year 3, where the orchestration platform is already running cheaper per year than the as-is model. Full cumulative payback is projected around Year 6-7, which is the normal timeline for platform infrastructure evaluated against its 7-10 year operating life, not a short-term ROI play. See [`business-case.md`](business-case.md) for the full arithmetic.

## Timeline

The regulatory deadline drives sequencing. The migration runs in waves by rail, lowest-risk first, over roughly 18 months, with the ISO 20022 transformation layer and its first compliant message types live well ahead of the regulatory date to leave buffer for audit findings. Full detail in [`06-phase-f-migration-planning/migration-roadmap.md`](../06-phase-f-migration-planning/migration-roadmap.md).

## Risk

The primary risk is treating this as a like-for-like infrastructure swap: it is a live-funds platform migration, and every wave carries a dual-run and rollback plan rather than a hard cutover. The next largest risk is scope discipline — cross-border rail build and merchant-facing product changes are explicitly excluded from this program so the compliance deadline isn't put at risk by unrelated scope. Governance for both is defined in [`07-phase-g-implementation-governance/`](../07-phase-g-implementation-governance/).

## The Ask

Board approval of the $17.5M 3-year investment envelope, and confirmation that cross-border rail expansion remains a separate, subsequent initiative that depends on — but does not delay — this program's regulatory-driven timeline.

---
*Fictional case study — see [README](../README.md) for full disclaimer.*
