# FulfillOrder – Architectural Boundaries

This document defines the **strict responsibility boundaries**
between the Application layer and its Adapters.

It exists to **prevent architectural drift**
and to guarantee long-term system correctness.

If a behavior violates these boundaries,
the behavior is incorrect by definition.

---

## Core Principle

> **The Application decides *what* happened.  
> Adapters decide *how* it is exposed or delivered.**

This rule is absolute.

---

## Application Layer Responsibilities

The Application layer **MUST**:

- orchestrate the FulfillOrder use case
- load and persist fulfillment state through ports
- invoke domain logic for state transitions
- determine the final Outcome
- classify failures using semantic Failure Types
- execute side effects only via abstract ports
- remain deterministic under repeated execution

The Application layer is the **single source of truth**
for fulfillment meaning.

---

## Application Layer Non-Responsibilities

The Application layer **MUST NOT**:

- handle retries or retry scheduling
- acknowledge or reject messages
- interpret infrastructure errors
- perform logging or monitoring decisions
- map Outcomes to transport-level responses
- throw or propagate technical exceptions
- depend on messaging, HTTP, databases, or frameworks

Any infrastructure concern inside the Application layer
is a design violation.

---

## Adapter Responsibilities

Adapters **MUST**:

- invoke the Application use case
- pass inputs exactly as received
- handle retries, acknowledgments, and delivery semantics
- translate Outcomes into protocol-specific responses
- ensure idempotent behavior at the infrastructure level
- respect Application-defined semantics

Adapters are **interpreters**, never decision-makers.

---

## Adapter Non-Responsibilities

Adapters **MUST NOT**:

- reinterpret or override Outcomes
- reclassify Failure Types
- introduce business rules
- decide retry vs stop behavior independently
- short-circuit execution paths
- infer hidden meaning from technical errors

If an adapter needs to “decide”,
the decision belongs in the Application layer.

---

## Outcome Interpretation Rules

- Every invocation returns exactly one Outcome
- Outcomes are final and authoritative
- Adapters must treat Outcomes as immutable facts
- A NoOp is a successful, valid result
- Failure Types define retry semantics — adapters do not

Adapters may **react** to Outcomes,
but must never **reinterpret** them.

---

## Failure Semantics Ownership

- Failure meaning is defined in `FAILURES.md`
- Retry vs stop is derived exclusively from Failure Type
- Infrastructure errors are mapped, never exposed
- No adapter may invent new failure categories

Failure semantics are **application-level knowledge**.

---

## Enforcement Rule

If at any point:

- an adapter contains business logic
- the application handles infrastructure logic
- Outcomes are inferred instead of returned
- retries are decided outside semantic failures

**The architecture has been violated.**

The correct fix is to move logic,
not to add exceptions.

---

## Final Rule

> If future code contradicts this document,
> **the code is wrong, not the boundary.**
