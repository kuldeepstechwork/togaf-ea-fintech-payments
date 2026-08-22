# ADR-004: Idempotency and Retry Strategy for Cross-Rail Transactions

## Status
Accepted

## Context

Paivo's as-is architecture has inconsistent, per-integration idempotency handling — some rails de-duplicate on merchant order ID, others do not de-duplicate at all — which has produced at least two documented double-debit incidents in the past 18 months. Architecture principle 4 mandates idempotency "by design, not by convention." This ADR defines the specific mechanism the orchestration platform uses to guarantee it.

## Decision

Every state-changing orchestration API requires a **caller-supplied idempotency key**, validated and stored centrally by the orchestration core in the idempotency key store (a dedicated, low-latency key-value store with TTL). A retry with the same idempotency key returns the original operation's result without re-executing it against the underlying rail; a request with a new idempotency key but otherwise identical parameters is treated as a genuinely new transaction. Adapters are additionally required to implement rail-side de-duplication where the rail's own protocol supports it (defense in depth), but the platform-level guarantee does not depend on adapters getting this right — it is enforced centrally regardless.

## Alternatives Considered

**1. Idempotency enforced only at the adapter level (each adapter responsible for its own de-duplication against its specific rail's semantics).** This is closer to what the as-is architecture already does, informally and inconsistently, so it would have required the least new central infrastructure. It was rejected because it is precisely the pattern that produced the current double-debit incidents — relying on N independently-implemented adapters to each correctly handle idempotency does not provide a platform-level guarantee, only a probabilistic one dependent on each adapter team's rigor. It also does not protect against a caller (e.g., the checkout application, or the orchestration core itself during an internal retry) sending duplicate requests before the adapter is even invoked.

**2. Idempotency enforced via database unique-constraint on transaction attributes (e.g., merchant ID + order ID + amount) rather than an explicit caller-supplied key.** This avoids requiring callers to generate and manage idempotency keys. It was rejected because legitimate use cases exist where a merchant genuinely wants to submit two transactions with identical amount and near-identical timing (e.g., two separate orders that happen to be for the same value) — a naive uniqueness constraint on transaction attributes would incorrectly reject or collapse these as duplicates. An explicit idempotency key, supplied by the caller and semantically meaning "this specific attempt," avoids this false-positive class entirely and is consistent with idempotency-key patterns used broadly in payments APIs.

**3. Selected: explicit caller-supplied idempotency key, enforced centrally in the orchestration core, with adapter-level de-duplication as defense in depth.** Requires client integration work (callers must generate and consistently supply idempotency keys) and central infrastructure (the key store), but is the only option that provides an unambiguous, platform-enforced guarantee without false-positive risk.

## Consequences

**Positive:** Idempotency is guaranteed by the platform for every rail uniformly, closing the gap that caused the as-is double-debit incidents. New rails inherit this guarantee automatically via the standard adapter interface rather than needing to reimplement it.

**Negative (accepted trade-off):** Every caller of the orchestration API (checkout application, merchant dashboard for manual actions, internal retry logic) must correctly generate and consistently reuse idempotency keys across retries — a caller that generates a new key on every retry defeats the guarantee, so this is documented as a mandatory integration requirement and covered by client-side integration testing guidance, not something the platform can fully protect against if a caller is non-compliant. The idempotency key store itself becomes a Tier-0-adjacent dependency (its unavailability blocks new transaction initiation), requiring it to be provisioned with the same multi-region resilience as the orchestration core.

## Stakeholders Affected
ARB (decision owner), Platform Engineering (build and operate the key store), Rail Adapter teams (defense-in-depth implementation), Checkout/Dashboard client teams (correct idempotency key usage), Head of Risk & Compliance (double-debit incident risk owner).

---
*Fictional case study — see [README](../README.md) for full disclaimer.*
