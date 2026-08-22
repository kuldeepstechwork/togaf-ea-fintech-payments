# Paivo Payments — Payment Orchestration Platform: Enterprise Architecture Case Study

**Disclaimer:** This is an illustrative TOGAF Enterprise Architecture case study modeling common, publicly known challenges in payment orchestration platforms — not a real engagement. Paivo Payments is an invented name, not affiliated with any real company, and nothing here is based on confidential information from any real employer or client. All figures, vendor names, and technical details are constructed for this exercise.

## Program Overview

This repository is a fictional TOGAF Architecture Development Method (ADM) engagement for **Paivo Payments**, a mid-sized payment orchestration company serving mid-market and enterprise merchants across UPI, card, wallet, and net banking rails. It documents the architecture function's work in re-platforming Paivo from a point-to-point, per-merchant, per-rail integration model to a central payment orchestration hub — driven by a hard regulatory deadline for ISO 20022 structured financial messaging and a business need to enter cross-border payments without multiplying integration cost linearly with every new rail or PSP partner.

## The Business Problem

Paivo's current architecture connects each merchant to each payment rail through a bespoke integration built and maintained independently by rail. That model has three compounding failure modes as the business scales: (1) every new PSP partnership or rail expansion carries a near-fixed integration cost regardless of merchant volume, so unit economics on smaller rails never improve; (2) reconciliation runs as N separate rail-specific processes with no single ledger of truth, which means settlement discrepancies are discovered late and investigated manually; and (3) the point-to-point model has no shared message transformation layer, so meeting the regulator's ISO 20022 structured-messaging mandate would require touching every integration independently rather than once, centrally. Leadership commissioned this EA engagement to define, govern, and sequence the re-architecture that resolves all three.

## How to Read This Repository

Every document here is written in **decision-voice**, not build-voice. The point of this repository is not to show what was implemented — it is to show how an architectural decision was reached: what alternatives were seriously considered, why they were rejected, what trade-off was knowingly accepted, and which stakeholders or governance bodies the decision was routed through. Cost, timeline, and risk figures throughout are invented but built to be internally consistent and plausible for a company at Paivo's scale — they are explicitly illustrative, not real financial data.

The repository follows the TOGAF ADM phase structure end to end, from architecture principles through implementation governance and organizational change management, with five Architecture Decision Records (ADRs) capturing the most consequential individual decisions in the program.

## Contents

- [TOGAF-ADM-MAPPING.md](TOGAF-ADM-MAPPING.md) — how this repository maps to the TOGAF ADM phases
- [00-preliminary/](00-preliminary/) — architecture principles and governance framework setup
- [01-phase-a-vision-and-scope/](01-phase-a-vision-and-scope/) — vision, stakeholders, business case, executive summary
- [02-phase-b-business-architecture/](02-phase-b-business-architecture/) — as-is/to-be business architecture, capability map
- [03-phase-c-information-systems-architecture/](03-phase-c-information-systems-architecture/) — data and application architecture
- [04-phase-d-technology-architecture/](04-phase-d-technology-architecture/) — reference architecture, technology standards
- [05-phase-e-opportunities-and-solutions/](05-phase-e-opportunities-and-solutions/) — solution building blocks, vendor evaluation, gap analysis
- [06-phase-f-migration-planning/](06-phase-f-migration-planning/) — migration roadmap, transition architectures
- [07-phase-g-implementation-governance/](07-phase-g-implementation-governance/) — governance framework, architecture contracts
- [08-phase-h-change-management/](08-phase-h-change-management/) — organizational change management plan
- [adrs/](adrs/) — five Architecture Decision Records for the program's most significant technical decisions

---
*Fictional case study — see disclaimer above for full context.*
