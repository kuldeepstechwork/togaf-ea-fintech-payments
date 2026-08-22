# ADR-003: ISO 20022 Transformation Layer Placement — Shared Layer vs. Per-Adapter vs. Embedded in Core

## Status
Accepted

## Context

Paivo must comply with a regulator-mandated ISO 20022 structured-messaging requirement across its payment initiation and status-reporting message types. The architecture must decide where message transformation logic (canonical internal format to/from ISO 20022 and rail-native wire formats) lives, because this decision determines whether future format changes are a bounded, central change or a repeated per-integration effort.

## Decision

Build a **single, shared message transformation layer** positioned between the orchestration core and the rail adapters, as its own independently deployable and versioned service. Adapters and the orchestration core both speak only the canonical internal schema; only the transformation layer has knowledge of external wire formats, including ISO 20022.

## Alternatives Considered

**1. Per-adapter transformation ("fat adapters") — each adapter embeds its own logic to convert to/from ISO 20022 and its rail's native format.** This was seriously considered because it lets adapter teams move independently with no shared-layer dependency, shortening any single adapter's initial build time by an estimated 15-20% (no need to integrate with a separate service). It was rejected because it reproduces, one layer down, exactly the problem this program exists to eliminate: a future ISO 20022 schema revision (which the technology standards catalog notes as a recurring, regulator-driven event) would require touching every adapter individually again, at a cost that scales with rail count rather than being paid once.

**2. Embedded in the orchestration core — transformation logic lives inside the core service itself rather than as a separate layer.** This was considered as a simplification (one fewer service to operate, no inter-service hop for transformation). It was rejected because it violates architecture principle 2 (rail-agnostic core): message format changes are driven by external regulatory and PSP release schedules Paivo does not control, and coupling the core's release cycle to those external schedules would mean routing, idempotency, and ledger-write logic — the platform's most critical and stable code — would need to be redeployed and re-validated every time a message format changes, unnecessarily increasing the blast radius of a low-risk category of change.

**3. Selected: standalone shared transformation layer.** Adds one additional network hop and one additional service to operate and version, but isolates message-format volatility completely from both the core and the adapters, and gives Paivo exactly one place to update for any future structured-messaging requirement — the same argument architecture principle 6 makes explicitly.

## Consequences

**Positive:** ISO 20022 schema updates are a single, centrally-owned, centrally-tested change. New rails do not need to independently implement ISO 20022 support — they inherit it automatically by emitting the canonical schema the transformation layer already knows how to convert. Compliance evidence for auditors is concentrated in one component's test suite rather than scattered across N adapters.

**Negative (accepted trade-off):** Added latency from the extra network hop, measured in staging at approximately 8-12ms per transaction, absorbed within the orchestration core's overall latency budget. Adapter teams have an external dependency (the transformation layer's release cadence and API stability) they must coordinate with, rather than having full autonomy over their own message handling — mitigated by strict interface versioning and a contract-testing gate on the transformation layer's canonical schema interface, mirroring the adapter contract-testing discipline.

## Stakeholders Affected
ARB (decision owner), Head of Risk & Compliance (regulatory compliance owner), Platform Engineering (build and ongoing operation), Rail Adapter teams (consumers of the transformation layer's interface).

---
*Fictional case study — see [README](../README.md) for full disclaimer.*
