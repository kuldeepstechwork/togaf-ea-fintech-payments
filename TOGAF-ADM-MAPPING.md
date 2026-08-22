# TOGAF ADM Phase Mapping

This engagement follows the TOGAF Architecture Development Method (ADM) cycle. The table below maps each phase to the folder that holds its artifacts and summarizes what each phase produced for the Paivo Payments orchestration program.

| ADM Phase | Folder | What It Contains |
|---|---|---|
| Preliminary | [`00-preliminary/`](00-preliminary/) | Architecture principles the program is bound by, and how the Architecture Review Board (ARB) and governance model were stood up before scoping began |
| Phase A — Architecture Vision | [`01-phase-a-vision-and-scope/`](01-phase-a-vision-and-scope/) | Problem statement, target-state vision, explicit scope boundaries, stakeholder map with RACI, business case with 3-year TCO, and a CxO-facing executive summary |
| Phase B — Business Architecture | [`02-phase-b-business-architecture/`](02-phase-b-business-architecture/) | As-is and to-be business process/capability narratives with diagrams, and a capability map with maturity ratings |
| Phase C — Information Systems Architecture | [`03-phase-c-information-systems-architecture/`](03-phase-c-information-systems-architecture/) | As-is/to-be data architecture (transaction, ledger, merchant, settlement domains) and application architecture with integration patterns |
| Phase D — Technology Architecture | [`04-phase-d-technology-architecture/`](04-phase-d-technology-architecture/) | The reference architecture for the orchestration hub and ISO 20022 transformation layer, including anti-patterns, and the approved technology standards catalog |
| Phase E — Opportunities & Solutions | [`05-phase-e-opportunities-and-solutions/`](05-phase-e-opportunities-and-solutions/) | Solution building block decomposition, a weighted vendor evaluation, and the prioritized as-is-to-to-be gap analysis |
| Phase F — Migration Planning | [`06-phase-f-migration-planning/`](06-phase-f-migration-planning/) | The phased migration roadmap (Gantt) and named transition architectures between as-is and to-be |
| Phase G — Implementation Governance | [`07-phase-g-implementation-governance/`](07-phase-g-implementation-governance/) | Implementation governance model, compliance checkpoints, and the architecture contract process with a worked example |
| Phase H — Architecture Change Management | [`08-phase-h-change-management/`](08-phase-h-change-management/) | Organizational change impact assessment, training plan, communications plan, and adoption metrics |
| Requirements Management (cross-cutting) | Referenced throughout Phases A–H | Requirements traceability is carried inline within each phase document rather than as a standalone artifact, consistent with TOGAF's treatment of Requirements Management as a continuous activity at the center of the ADM cycle rather than a discrete phase deliverable |
| Cross-phase decisions | [`adrs/`](adrs/) | Five Architecture Decision Records capturing the program's most consequential and hardest-to-reverse technical decisions |

---
*Fictional case study — see [README](README.md) for full disclaimer.*
