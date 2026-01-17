# FulfillOrder – Outcome Model

This document defines the **Outcome Model**
returned by the `FulfillOrder` application use case.

An Outcome represents the **complete semantic result**
of an invocation.

The use case always returns **exactly one Outcome**.

---

## Design Principle

Outcomes are:

- explicit
- exhaustive
- deterministic
- free of technical concerns

They replace exceptions and implicit control flow.

---

## Outcome Categories

### 1. FulfillmentCompleted

Represents a **successful fulfillment**.

#### Characteristics

- The order is fulfilled
- The state transition is final and persisted
- Side effects have been executed or scheduled
- The outcome is immutable

#### Semantic Meaning

> The system is in its final desired state.

---

### 2. FulfillmentFailed

Represents a **failed fulfillment attempt**.

This outcome always contains a **Failure Type**
as defined in `FAILURES.md`.

#### Characteristics

- The failure is meaningful and classified
- The state reflects the failure
- Retry behavior is derived from failure semantics

#### Possible Failure Types

- TerminalFailure
- TransientFailure
- InvalidState

#### Semantic Meaning

> Fulfillment could not be completed for a known reason.

---

### 3. NoOp / AlreadyFulfilled

Represents a **valid invocation with no effect**.

#### Characteristics

- The order is already fulfilled
- No state transition occurred
- No new side effects are required

#### Semantic Meaning

> The system was already correct.

This is **not a failure**.

---

## Outcome Guarantees

For any invocation:

- exactly one Outcome is returned
- the Outcome reflects persisted state
- repeated invocations return the same Outcome
- no Outcome depends on infrastructure behavior

---

## Explicit Non-Responsibilities

The Outcome Model does NOT define:

- HTTP responses
- message acknowledgments
- retry scheduling
- logging or monitoring
- exception handling

Those concerns belong to adapters.

---

## Invariants

- Every possible execution path maps to one Outcome
- Outcomes never throw or propagate errors
- Failure semantics are preserved end-to-end
- Adapters must not reinterpret Outcomes

Any implementation that mixes Outcomes with
exceptions or implicit errors is incorrect.
 
