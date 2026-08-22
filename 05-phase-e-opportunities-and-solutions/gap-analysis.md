# Gap Analysis: As-Is to To-Be

## Purpose

This gap analysis inventories the delta between Paivo's as-is capability state (Phase B) and the target state, prioritized by a combination of regulatory urgency, business impact, and implementation risk. Each gap traces to the capability map and feeds directly into the solution building blocks and migration roadmap.

## Prioritization Method

Gaps are scored 1-5 on **Regulatory Urgency**, **Business Impact**, and **Implementation Risk if Unaddressed**, summed to a priority score out of 15. This is a deliberately simple scoring model — the ARB considered a more elaborate weighted-multi-criteria model but rejected it as disproportionate effort for a gap list this size (12 items), where a simple additive score produced the same relative ordering in a side-by-side test.

## Gap Register

| Gap | Regulatory Urgency | Business Impact | Risk if Unaddressed | Priority Score | Addressed By |
|---|---|---|---|---|---|
| No ISO 20022 structured messaging capability | 5 | 4 | 5 | 14 | Message Transformation Layer SBB |
| No single ledger of truth | 3 | 5 | 5 | 13 | Ledger Service SBB |
| Inconsistent/absent idempotency enforcement | 2 | 4 | 5 | 11 | Orchestration Core + Idempotency Store SBB |
| PCI-DSS scope spans 11 systems | 4 | 3 | 4 | 11 | Token Vault SBB, adapter tokenization boundary |
| Reconciliation is manual and rail-fragmented | 2 | 5 | 4 | 11 | Reconciliation Engine SBB |
| New rail onboarding cost is high and unpredictable | 1 | 4 | 3 | 8 | Standard Adapter Interface Contract |
| No standard adapter interface / contract testing | 1 | 3 | 4 | 8 | Solution Building Blocks — Adapter Interface Contract |
| Client-facing apps coupled to rail-specific backend services | 1 | 3 | 3 | 7 | Orchestration Core stable API layer |
| No shared observability/tracing across rail integrations | 1 | 2 | 4 | 7 | Technology Standards — mandatory tracing requirement |
| Bus-factor risk on 2 of 14 integrations (single-engineer knowledge) | 1 | 2 | 3 | 6 | Adapter build absorbs and documents legacy logic during migration |
| No consolidated merchant settlement reporting | 1 | 3 | 2 | 6 | Ledger Service enables downstream reporting (reporting UI itself out of scope) |
| Cross-border rail support absent | 1 | 2 | 1 | 4 | Explicitly out of scope for this program; noted for future initiative |

## Reading the Priorities

The top three gaps — ISO 20022 capability, single ledger of truth, and idempotency enforcement — collectively define the Wave 0 and Wave 1 scope of the migration roadmap, because they score highest on both regulatory urgency and unaddressed risk. Notably, **new rail onboarding cost**, despite being a headline driver of the business case, scores lower (8/15) than the ledger and idempotency gaps, because it is a business-impact-and-cost problem rather than a regulatory-deadline-and-risk problem — it is still addressed, but its resolution follows naturally from building the orchestration core and adapter contract correctly, rather than requiring separate dedicated investment.

**Cross-border rail support** scores lowest and is intentionally not addressed by this program at all — consistent with the Phase A scope boundary, its low score here is corroborating evidence that deferring it was the right sequencing call, not just a scope-management convenience.

## Gaps Explicitly Not Prioritized for This Program

- **No consolidated merchant settlement reporting UI.** The underlying data gap is closed by the ledger service, but building a merchant-facing reporting interface is treated as a Payments Product initiative that can proceed independently once the ledger exists — bundling it here would extend this program's scope without being on the regulatory critical path.
- **Bus-factor risk on legacy integrations.** Addressed as a side effect of migration (adapter teams must understand and document legacy behavior to migrate it correctly) rather than as a dedicated knowledge-transfer project, which the ARB judged as sufficient given the legacy integrations are being retired, not maintained indefinitely.

---
*Fictional case study — see [README](../README.md) for full disclaimer.*
