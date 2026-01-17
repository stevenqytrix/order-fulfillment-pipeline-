# FulfillOrder – Architecture Test Matrix

This document defines the **architecture-level test matrix**
for the `FulfillOrder` application use case.

It specifies **what must be testable and observable**
to ensure that the architecture is correctly implemented.

This is **not** a test implementation.
It is a **verification contract**.

---

## Purpose

The Architecture Test Matrix exists to:

- make architectural rules verifiable
- prevent behavioral regressions
- align tests with architectural intent
- ensure outcomes match design documents
- decouple testing from infrastructure

If a behavior cannot be tested using this matrix,
the architecture is incomplete or incorrectly implemented.

---

## Scope

This matrix validates:

- application flow correctness
- outcome determinism
- failure semantics
- idempotency guarantees
- boundary enforcement

It does **not** test:

- infrastructure reliability
- performance characteristics
- framework integration
- delivery guarantees

---

## Test Axes

Each test case is defined by:

- Initial Fulfillment State
- Input Invocation
- Domain Decision Result
- Persistence Result
- Expected Outcome
- Expected Failure Type (if any)
- Architectural Reference

---

## Architecture Test Matrix

| # | Initial State | Domain Decision | Persistence Result | Expected Outcome | Failure Type | Reference |
|---|---------------|-----------------|--------------------|------------------|--------------|-----------|
| 1 | NotExisting | Transition → Fulfilled | Success | FulfillmentCompleted | — | FLOW.md §3, DECISION_TABLE.md |
| 2 | NotExisting | Transition → Failed | Success | FulfillmentFailed | TerminalFailure | FAILURES.md |
| 3 | NotExisting | Transition → Pending | Success | Pending | TransientFailure | FAILURES.md |
| 4 | NotExisting | Invalid Transition | — | FulfillmentFailed | InvalidState | DECISION_TABLE.md |
| 5 | Pending | Transition → Fulfilled | Success | FulfillmentCompleted | — | FLOW.md |
| 6 | Pending | Transition → Pending | Success | Pending | TransientFailure | FAILURES.md |
| 7 | Pending | Transition → Failed | Success | FulfillmentFailed | TerminalFailure | FAILURES.md |
| 8 | Pending | Invalid Transition | — | FulfillmentFailed | InvalidState | DECISION_TABLE.md |
| 9 | Fulfilled | Any | — | NoOp / AlreadyFulfilled | — | OUTCOME.md |
|10 | Failed (Terminal) | Any | — | NoOp / AlreadyFailed | TerminalFailure | OUTCOME.md |
|11 | Any | Any | Persistence Error | Pending | TransientFailure | DECISION_TABLE.md |
|12 | Any | Any | Success | Deterministic Outcome | — | CONTRACT.md |

---

## Mandatory Architectural Assertions

Every compliant implementation MUST ensure that:

- each row maps to exactly one Outcome
- Outcomes are deterministic under repeated execution
- persistence precedes side effects
- side effects do not influence Outcomes
- idempotent inputs produce stable results
- no exception alters the Outcome Model

Violation of any assertion is an **architectural defect**.

---

## Test Strategy Implications

This matrix implies that:

- tests are written at the use-case boundary
- domain logic is tested independently
- infrastructure is mocked or substituted
- retries are driven by Failure Types, not errors
- NoOp paths are explicitly tested

---

## Change Policy

This matrix may change **only if**:

- a new Outcome is introduced
- a new Failure Type is defined
- domain states change meaning

Infrastructure changes must **never** require changes
to this matrix.

---

## Architect Note

If a test passes but contradicts this matrix,
the test is wrong.

If code passes tests but violates this matrix,
the code is wrong.

This matrix is the **final arbiter** of correctness.
