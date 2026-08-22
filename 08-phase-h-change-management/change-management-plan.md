# Change Management Plan

## Purpose

Phase H addresses the organizational, not just technical, change this program requires. The orchestration platform re-architecture changes how Finance Operations reconciles transactions, how Platform Engineering onboards new rails, how Security manages PCI-DSS scope, and how several teams whose work is currently organized around legacy integrations will see that work retired. This plan treats that organizational transition as a first-class deliverable, not a side effect of the technical migration.

## Organizational Change Impact

| Group | Nature of Change | Impact Level |
|---|---|---|
| Finance Operations (reconciliation team, 4.5 FTE) | Moves from manual, rail-fragmented reconciliation to operating a continuous, automated matching engine; role shifts from data assembly to exception investigation and process ownership | High |
| Legacy Integration Engineers (point-to-point teams) | Their integrations are retired progressively; individuals transition to adapter-building roles on the new platform or to other engineering priorities | High |
| Platform Engineering (new orchestration/adapter teams) | New standard interface, contract-testing discipline, and ARB governance process to work within — a more structured process than the ad hoc integration builds of the past | Medium |
| Merchant-facing Account Management | New rail onboarding timeline (2-3 weeks vs. 10-14) becomes a sales asset; needs updated talking points and realistic expectation-setting during the migration window when timelines are still transitional | Medium |
| Security & Compliance | New centralized PCI-DSS scope boundary changes what they audit and how; less breadth, more depth on fewer systems | Medium |
| Merchants (external) | No process change expected if migration executes as planned (see Dual-Rail Coexistence transition architecture); communication is precautionary, not because disruption is expected | Low (with contingency plan if a wave's dual-run reveals issues) |

## Training Plan

- **Finance Operations:** A structured 3-week transition curriculum per migration wave, run in parallel with that wave's dual-run period — reconciliation staff learn the new ledger-based matching tooling on live (shadow) data before it becomes their primary system of record, rather than training on a system they've never seen live traffic in.
- **Legacy Integration Engineers:** Offered a defined path onto adapter-building teams, with a structured onboarding to the standard adapter interface and contract-testing discipline; this is explicitly framed as a skills transition (their deep PSP-specific knowledge is directly valuable in adapter migration work) rather than a redundancy process.
2 dedicated "migration office hours" sessions per week during Waves 1-4 for any engineering team with adapter or integration questions.
- **ARB governance training:** A one-time onboarding session for any team submitting its first Architecture Contract, run by the Chief Architect, to reduce the friction of the new governance process for teams unfamiliar with it.

## Communication Plan

| Audience | Channel | Cadence | Content |
|---|---|---|---|
| Board / Executive Committee | Steering committee update | Monthly | Program health, budget vs. business case, regulatory deadline tracking |
| All-hands (internal) | Company all-hands segment | Quarterly | Program narrative, wins, and what's changing for which teams |
| Finance Operations | Dedicated working sessions | Weekly during active migration waves | Reconciliation tooling changes, exception-handling process updates |
| Merchant-facing teams | Internal briefing + FAQ document | Per wave, ahead of each cutover | What's changing for merchants (ideally nothing), what to say if asked |
| Merchants (external) | Account management outreach, only if a wave's dual-run surfaces a customer-visible change | As needed, wave-triggered | Reserved for genuine customer impact, not blanket "we're migrating" messaging that would create unnecessary concern about a live-funds system |
| PSP / Rail Partners | Direct technical liaison | Per adapter build, ahead of cutover | API/webhook configuration changes required on their side, if any |

## Adoption Metrics

| Metric | Target |
|---|---|
| Finance Operations reconciliation tooling adoption (post go-live per wave) | 100% of reconciliation work for migrated rails performed via new ledger-based tooling within 2 weeks of cutover, with legacy tooling access revoked at that point |
| Architecture Contract submissions using the mandatory template correctly (no return-unreviewed for missing sections) | ≥90% by Wave 3, tracked as a proxy for governance process adoption |
| Legacy Integration Engineer transition | ≥80% of affected engineers placed into adapter-building or other roles within one quarter of their legacy integration's decommission, tracked jointly with People team |
| Merchant support tickets related to migration | Held to baseline (no statistically significant increase during any wave's cutover week) — the clearest signal that migration is operationally invisible to merchants, which is the explicit goal |
| Finance month-end close duration | Reduced from current ~6 business days to a target ≤3 business days by Wave 5, as continuous reconciliation reduces end-of-period cleanup work |

## Why This Plan Is Scoped the Way It Is

The heaviest change management investment goes to Finance Operations and Legacy Integration Engineers because they are the two groups whose day-to-day work changes most fundamentally — everyone else experiences this program primarily as "a new interface to build against" or "a process that got easier," which needs communication and training but not the same intensity of support. Merchant communication is deliberately conservative (only triggered by genuine customer-visible impact) because over-communicating a live-funds migration to external parties risks manufacturing concern about a system that, if the transition architecture in Phase F works as designed, they should never notice changed at all.

---
*Fictional case study — see [README](../README.md) for full disclaimer.*
