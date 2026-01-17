# FulfillOrder – Adapter Guidelines

This document defines **mandatory rules**
for all adapters implementing ports
used by the `FulfillOrder` application use case.

Adapters are **replaceable details**.
Their behavior must be strictly constrained.

This document is **normative**, not advisory.

---

## Purpose

The Adapter Guidelines exist to:

- prevent architectural drift
- eliminate adapter-level interpretation
- enforce semantic correctness
- guarantee portability and testability
- align infrastructure with application intent

If an adapter violates these rules,
it is **architecturally incorrect**.

---

## General Rules (Apply to ALL Adapters)

All adapters MUST:

- implement exactly one port
- depend only on port interfaces and domain types
- never reference application flow logic
- never introduce business rules
- never emit Outcomes directly
- never throw exceptions across the port boundary

Adapters must translate **technical reality**
into **port-level semantics**, nothing more.

---

## FulfillmentRepository Adapter Rules

A FulfillmentRepository adapter MUST:

- provide idempotent persistence
- tolerate repeated save attempts
- return the same persisted state for the same input
- never perform side effects
- never decide transitions
- never infer retry or failure semantics

The repository guarantees **state consistency**,
not delivery, retries, or correctness.

Persistence errors must be surfaced only as:
- success
- failure (technical)

Semantic interpretation belongs to the application layer.

---

## FulfillmentSideEffects Adapter Rules

A FulfillmentSideEffects adapter MUST:

- execute effects based on persisted state
- tolerate duplicate invocation
- be safe under retries
- never influence persistence
- never influence domain decisions
- never return control-flow signals

Side effects are **consequences**, not decisions.

If an effect fails, the adapter may retry internally,
but must not alter application Outcomes.

---

## Consumer / Inbound Adapter Rules

Inbound adapters (e.g. message consumers, HTTP handlers) MUST:

- invoke the use case exactly once per input
- pass inputs without enrichment or inference
- interpret Outcomes without modification
- derive acknowledgments or responses from Outcomes
- never retry based on exceptions
- never inspect infrastructure failures directly

Inbound adapters react to **Outcomes**, not errors.

---

## Explicitly Forbidden Behavior

Adapters MUST NOT:

- introduce conditional branching on domain meaning
- map technical errors directly to retries
- short-circuit the application flow
- bypass ports
- mutate domain state
- log or swallow semantic failures silently

Any such behavior is a **design violation**.

---

## Testing Implications

Adapters MUST be testable by:

- substituting ports with test doubles
- simulating idempotent calls
- verifying no semantic interpretation exists
- asserting behavior under repeated invocation

If an adapter requires knowledge of the use case flow
to be tested, it is incorrectly designed.

---

## Change Policy

This document changes **only if**:

- a port contract changes
- a new port is introduced

Infrastructure evolution must not require
changes to these rules.

---

## Architect Note

If an adapter implementation requires
reading application documents to understand behavior,
the adapter is doing too much.

Adapters execute.
Applications decide.
Domains define meaning.
