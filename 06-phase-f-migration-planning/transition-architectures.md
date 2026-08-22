# Transition Architectures

## Why Transition States Matter Here

A payment platform migration cannot move from as-is to to-be in a single step — the business processes live merchant funds throughout, so every intermediate state must itself be a coherent, operable architecture, not a broken halfway point. This document names and describes the key transition architecture Paivo operates in during the migration window, distinct from both the as-is and to-be states documented in Phase B.

## Transition State: "Dual-Rail Coexistence" (Waves 1–4, roughly Q1 2027 – Q4 2027)

### What Is True at This State

During Dual-Rail Coexistence, Paivo simultaneously operates:

- **Legacy point-to-point integrations** for rails not yet migrated, continuing to run exactly as described in [`02-phase-b-business-architecture/as-is-business-architecture.md`](../02-phase-b-business-architecture/as-is-business-architecture.md), including their independent reconciliation pipelines.
- **The orchestration hub, ledger, and transformation layer** (built in Wave 0) actively processing traffic for rails that have completed migration.
- **A reconciliation bridge process** that consolidates ledger data from migrated rails with the legacy reconciliation output from not-yet-migrated rails into a single finance-facing view, so Finance Operations does not have to context-switch between two entirely separate reporting processes during the transition.
- **Dual-run validation** for whichever rail is actively mid-migration: traffic flows through both the legacy integration and the new adapter in shadow mode, with outputs compared before that rail's cutover is finalized.

### Why This Transition State Is Necessary

A "big bang" cutover — migrating all rails simultaneously in one release — was evaluated in Phase A and rejected specifically because it would require validating the entire orchestration platform's correctness across every rail's edge cases at once, with no ability to isolate a defect to a single rail if something went wrong post-cutover. Dual-Rail Coexistence exists to let each rail's migration be independently verified and independently reversible, at the accepted cost of running two operating models concurrently for roughly a year.

### Trade-offs Accepted at This State

- **Operational overhead:** Finance Operations and Platform Engineering must support two reconciliation paradigms simultaneously (the legacy fragmented model and the new unified ledger model) for the migration's duration — quantified in the business case as the "migration program overhead" line item (~$1.2M Year 1, ~$600K Year 2).
- **Partial regulatory compliance exposure:** rails still on legacy integrations do not yet have ISO 20022 structured messaging, which is why the transformation layer's readiness is deliberately decoupled from full migration completion (see [`migration-roadmap.md`](migration-roadmap.md)) — Paivo can demonstrate compliance progress rail-by-rail to the regulator rather than presenting an all-or-nothing state.
- **Temporary increase in system count, not decrease.** Counterintuitively, mid-migration Paivo is running *more* systems than either the as-is or to-be state (both legacy and new infrastructure concurrently), which was flagged to the Board explicitly in the business case narrative so the Year 1 cost spike is understood as transitional, not a sign of runaway scope.

## Transition State: "Reconciliation Bridge Retirement" (Wave 5, Q4 2027 – Q1 2028)

### What Is True at This State

By this point, all rails have migrated and the legacy integrations are being decommissioned, but the reconciliation bridge process built for Dual-Rail Coexistence is still running for a defined wind-down period — not removed the moment the last rail cuts over. This is deliberate: Finance Operations' month-end close cycle needs at least one full closed period fully on the new unified ledger, validated against historical patterns, before the bridge (and the manual reconciliation muscle memory it represents) is retired.

### Why This Transition State Is Necessary

Retiring the reconciliation bridge in the same release as the last rail cutover was considered and rejected — it would remove the safety net (the ability to fall back to legacy reconciliation output for comparison) at precisely the moment the new ledger is handling 100% of volume for the first time, which the ARB judged to be the highest-risk moment to also remove the fallback. Holding the bridge for one additional closed accounting period costs a modest, bounded amount of continued dual maintenance in exchange for a materially safer final cutover.

---
*Fictional case study — see [README](../README.md) for full disclaimer.*
