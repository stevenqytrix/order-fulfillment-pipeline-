# FulfillOrder – Architecture Test Strategy

This document defines the **architectural testing strategy**
for the FulfillOrder application use case.

Its purpose is to **prevent semantic and structural regressions**
over time, independently from implementation details.

These tests validate **architecture**, not behavior.

---

## Core Principle

> If architecture is correct, behavior can change safely.  
> If architecture regresses, behavior correctness is meaningless.

---

## What We Test (and Why)

Architectural tests exist to ensure that:

- responsibilities remain correctly separated
- decision logic stays centralized
- infrastructure does not leak into the application
- semantics remain explicit and stable

---

## Invariants to Protect

The following invariants MUST always hold.

### 1. Deterministic Outcomes

- Every FulfillOrder invocation returns exactly one Outcome
- No exceptions escape the use case
- Outcomes fully represent execution result

**Violation example**: throwing an exception instead of returning an Outcome.

---

### 2. Domain Before Persistence

- Domain transition is decided before persistence
- Persistence never influences domain decisions
- Failed persistence never mutates domain state

**Violation example**: checking DB errors inside domain logic.

---

### 3. Persistence Before Side Effects

- Side effects execute only after successful persistence
- Side effects reflect persisted state only
- No side effect can affect state decisions

**Violation example**: emitting events before saving state.

---

### 4. Failure Semantics Are Centralized

- Failure Types are defined only in `FAILURES.md`
- Retry vs stop derives exclusively from Failure Type
- Adapters must not redefine failure meaning

**Violation example**: retry logic based on exception type.

---

### 5. Idempotency Is Guaranteed by Design

- Repeated invocation produces the same Outcome
- Final states short-circuit execution
- No duplicate side effects are observable

**Violation example**: emitting duplicate events on reprocessing.

---

## Adapter Isolation Rules

Application-level tests MUST assert that:

- no adapter code is referenced
- no framework types are imported
- no transport semantics are present
- no infrastructure errors are exposed

Adapters are tested separately.

---

## Test Types Recommended

### Architectural Unit Tests

Validate:
- ordering of steps (domain → persistence → side effects)
- Outcome exhaustiveness
- Failure Type mapping

### Contract Tests

Validate:
- port contracts are respected
- adapters honor Application semantics

### Regression Guards

Snapshot-style tests asserting that:
- Decision Table rows are all reachable
- No execution path bypasses Outcome mapping

---

## What We Explicitly Do NOT Test Here

- HTTP status codes
- message acknowledgment behavior
- retry timing
- infrastructure availability
- logging and monitoring

Those belong to adapter or integration tests.

---

## Enforcement Rule

If a test needs to mock infrastructure
to validate application behavior,
the test is incorrectly scoped.

Move the test or refactor the code.

---

## Final Rule

> Architecture tests are not optional.  
> They are the **last line of defense** against system decay.
