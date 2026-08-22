# Business Case

*All figures in this document are illustrative, invented for this fictional case study, and constructed to be internally consistent — they are not real financial data.*

## Approach

This business case compares a 3-year total cost of ownership (TCO) for staying on the as-is point-to-point integration model against building and operating the to-be orchestration platform. It uses a blended engineering rate of **$110/person-month** (fully loaded, illustrative) and treats avoided integration cost and avoided compliance remediation cost as the primary quantifiable benefits, alongside the reconciliation labor savings.

## As-Is Cost Model (Point-to-Point, 3-Year Projection)

Paivo's growth plan calls for adding an average of 6 new rail/PSP integrations per year (new PSPs, wallet partners, or geographic rail variants) over the next 3 years, on top of maintaining the 14 integrations already live.

| Line Item | Basis | Year 1 | Year 2 | Year 3 |
|---|---|---|---|---|
| New integration build cost | 6 integrations/yr × 16 person-months × $110K/PM (annualized as $110K per PM, illustrative) — simplified to $180K fully loaded per integration | $1,080,000 | $1,080,000 | $1,080,000 |
| Maintenance of existing integrations | 14 (Yr1) → 20 (Yr2) → 26 (Yr3) integrations × $22K/yr upkeep each | $308,000 | $440,000 | $572,000 |
| Manual reconciliation labor | 4.5 FTE finance ops × $95K fully loaded | $427,500 | $427,500 | $427,500 |
| ISO 20022 remediation (retrofit per integration, forced by deadline) | 20 integrations × $65K retrofit cost, concentrated in Year 2 | $0 | $1,300,000 | $0 |
| PCI-DSS audit scope cost (11 systems in scope) | $38K per system per year (QSA assessment, compensating controls) | $418,000 | $418,000 | $418,000 |
| **As-Is Total** | | **$2,233,500** | **$3,665,500** | **$2,497,500** |
| **As-Is 3-Year Total** | | | | **$8,396,500** |

## To-Be Cost Model (Orchestration Hub, 3-Year Projection)

| Line Item | Basis | Year 1 | Year 2 | Year 3 |
|---|---|---|---|---|
| Orchestration core + ledger build | 42 PM Year 1 (design + build), $110K/PM | $4,620,000 | $0 | $0 |
| ISO 20022 transformation layer build | 14 PM, one-time, Year 1 | $1,540,000 | $0 | $0 |
| Adapter build (14 existing rails migrated) | 6 PM/adapter avg × $110K/PM, phased per the migration roadmap ([`06-phase-f-migration-planning/migration-roadmap.md`](../06-phase-f-migration-planning/migration-roadmap.md)): 9 adapters in Year 1, the remaining 5 in Year 2 | $2,310,000 (9 adapters) | $3,300,000 (5 adapters) | $0 |
| New rail onboarding (to-be cost) | 6/yr × 45K fully loaded per adapter (per architecture principle 2 & 7) | $0 | $270,000 | $270,000 |
| Platform run cost (infra, active-active multi-region, Tier-0) | ~$1.1M/yr steady state, ramping Year 1 | $550,000 | $1,100,000 | $1,100,000 |
| Reduced reconciliation labor | 1.5 FTE (down from 4.5) × $95K | $142,500 | $142,500 | $142,500 |
| PCI-DSS audit scope cost (≤3 systems in scope, post-consolidation) | $38K × 3 systems | $0 (Yr1 still transitioning) | $114,000 | $114,000 |
| Migration program overhead (dual-run, cutover engineering) | One-time | $1,200,000 | $600,000 | $0 |
| **To-Be Total** | | **$10,362,500** | **$5,526,500** | **$1,626,500** |
| **To-Be 3-Year Total** | | | | **$17,515,500** |

## 3-Year TCO Comparison

| | As-Is | To-Be | Delta |
|---|---|---|---|
| 3-Year Total | $8,396,500 | $17,515,500 | +$9,119,000 upfront-weighted |

Read on its own, the 3-year comparison looks unfavorable to the to-be model — this is expected and is the standard shape of a platform re-architecture: cost front-loads into build years while benefit accrues in run-rate savings and avoided future cost that a 3-year window does not fully capture. Two adjustments matter for an honest comparison:

**1. The as-is model is not a stable baseline — it is forced to spend $1.3M in Year 2 on ISO 20022 retrofit regardless of whether Paivo builds the orchestration hub, because the regulatory deadline applies either way.** That cost is unavoidable and should be read as a mandatory floor under the as-is line, not a discretionary program cost avoided by doing nothing.

**2. Run-rate cost inverts by Year 3.** By Year 3, to-be run cost ($1,626,500) is meaningfully below the as-is Year 3 run cost ($2,497,500) and the gap widens every subsequent year because new rail onboarding cost is 4x cheaper on the to-be platform ($45K vs. $180K per rail) and PCI-DSS audit scope cost is fixed at 3 systems instead of growing with every new integration.

## Payback Period Calculation

Comparing the two models year-over-year rather than cumulatively:

- Year 1 delta: To-Be costs $10,362,500 vs. As-Is $2,233,500 → To-Be is **$8,129,000 more expensive** in Year 1.
- Year 2 delta: To-Be costs $5,526,500 vs. As-Is $3,665,500 (which includes the forced ISO 20022 retrofit) → To-Be is **$1,861,000 more expensive** in Year 2.
- Year 3 delta: To-Be costs $1,626,500 vs. As-Is $2,497,500 → To-Be is **$871,000 cheaper** in Year 3.

Cumulative gap after Year 3: $8,129,000 + $1,861,000 − $871,000 = **$9,119,000 cumulative extra spend** through Year 3 (matches the TCO delta above, as expected).

Extending the run-rate trend line beyond the 3-year window (holding Year 3 run costs roughly flat, since both models plateau operationally): the as-is model's annual cost keeps climbing as rail count grows (~$132K in added maintenance cost per new rail cohort per year), while the to-be model's annual cost stays roughly flat. At the Year 3 run-rate gap of ~$871K/year and assuming it widens modestly as rail count grows, **cumulative payback (to-be spend fully recovered against as-is) is projected at approximately Year 6–7** of platform life, which is standard for infrastructure re-platforming and is evaluated by the Board against a 7-10 year platform lifespan expectation, not a 3-year ROI bar.

## Non-Financial Benefits Not Fully Captured Above

- Regulatory deadline risk: as-is retrofit approach has materially higher execution risk of missing the deadline because it requires 20 separate, sequential integration changes rather than one shared layer.
- Reduced breach blast radius from PCI-DSS scope reduction (11 systems → 3) is a risk-cost avoidance not monetized in this table.
- Sales enablement value of 2-3 week rail onboarding vs. 10-14 weeks is directional support for the cross-border expansion business case (out of scope for this program but a stated downstream beneficiary).

---
*Fictional case study — see [README](../README.md) for full disclaimer.*
