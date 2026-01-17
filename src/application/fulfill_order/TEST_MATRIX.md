# FulfillOrder – Test Matrix

This document defines the **architectural test matrix**
for the `FulfillOrder` application use case.

It translates the **Decision Table** into **mandatory test cases**
that any implementation must satisfy.

The purpose is to ensure:
- semantic correctness
- deterministic behavior
- absence of architectural regressions

---

## How to Read This Matrix

- **Each row** corresponds to one execution path
- **Each row** must be covered by at least one test
- Tests validate **architecture and semantics**, not infrastructure

If a row cannot be tested, the implementation is incorrect.

---

## Test Dimensions

Each test must assert:

- **Outcome** returned
- **Failure Type** (if any)
- **Retry vs Stop** semantics
- **Idempotency** guarantees
- **Side Effects** execution rules

---

## Test Matrix

| # | Initial State        | Domain Decision        | Persistence Result | Expected Outcome            | Failure Type       | Retry | Idempotent | Side Effects |
|---|----------------------|------------------------|--------------------|-----------------------------|--------------------|-------|------------|--------------|
| 1 | NotExisting          | Fulfilled              | Success            | FulfillmentCompleted        | —                  | No    | Yes        | Yes (once)   |
| 2 | NotExisting          | Failed                 | Success            | FulfillmentFailed           | TerminalFailure    | No    | Yes        | Yes          |
| 3 | NotExisting          | Pending                | Success            | Pending (Transient)         | TransientFailure   | Yes   | Yes        | Optional     |
| 4 | NotExisting          | InvalidTransition      | —                  | FulfillmentFailed           | InvalidState       | No    | Yes        | No           |
| 5 | Pending              | Fulfilled              | Success            | FulfillmentCompleted        | —                  | No    | Yes        | Yes (once)   |
| 6 | Pending              | Pending                | Success            | Pending (Transient)         | TransientFailure   | Yes   | Yes        | Optional     |
| 7 | Pending              | Failed                 | Success            | FulfillmentFailed           | TerminalFailure    | No    | Yes        | Yes          |
| 8 | Pending              | InvalidTransition      | —                  | FulfillmentFailed           | InvalidState       | No    | Yes        | No           |
| 9 | Fulfilled            | Any                    | —                  | NoOp / AlreadyFulfilled     | —                  | No    | Yes        | No           |
|10 | Failed (Terminal)    | Any                    | —                  | NoOp / AlreadyFailed        | TerminalFailure    | No    | Yes        | No           |
|11 | Any                  | Any                    | PersistenceError   | Pending (Transient)         | TransientFailure   | Yes   | Yes        | No           |

---

## Mandatory Assertions per Test

Each test MUST explicitly assert that:

- exactly **one Outcome** is returned
- no exceptions escape the use case
- retry semantics match Failure Type
- repeated execution returns the same Outcome
- side effects are not duplicated

---

## Side Effects Rules

- Side effects execute **only after successful persistence**
- Side effects must be idempotent
- No side effects on NoOp or InvalidState
- Optional side effects must not affect Outcomes

---

## What These Tests Do NOT Cover

These tests intentionally exclude:

- transport protocols
- HTTP status codes
- message acknowledgment logic
- retry timing
- infrastructure availability

Those concerns belong to adapter or integration tests.

---

## Enforcement Rule

If any test in this matrix fails:

- the implementation is incorrect
- the fix must happen in code
- documents must NOT be changed to “fit” the code

---

## Architect Rule

> If code passes all functional tests
> but fails this matrix,
> **the code is wrong**.

This matrix is non-negotiable.
