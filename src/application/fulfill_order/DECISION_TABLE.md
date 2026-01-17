# FulfillOrder – Decision Table

This document defines the **decision matrix**
used by the `FulfillOrder` application use case.

It maps **input conditions** and **domain decisions**
to **Outcomes** and **Failure Types**.

No control flow logic is implemented here.  
This table is the **single source of truth** for decision reasoning.

---

## Purpose

The Decision Table exists to:

- make fulfillment decisions explicit
- separate **retry** vs **stop** semantics
- avoid conditional logic scattered across code
- ensure exhaustiveness of outcomes

Every execution path **must map to exactly one row**.

---

## Decision Axes

Decisions are derived from the combination of:

- **Current Fulfillment State**
- **Domain Transition Result**
- **Persistence Result**

Infrastructure concerns are explicitly excluded.

---

## Decision Table

| Current State        | Domain Decision Result        | Persistence Result | Outcome Returned              | Failure Type        | Notes                          |
|---------------------|-------------------------------|--------------------|-------------------------------|---------------------|--------------------------------|
| NotExisting         | Transition → Fulfilled        | Success            | FulfillmentCompleted          | —                   | Happy path                     |
| NotExisting         | Transition → Failed           | Success            | FulfillmentFailed             | TerminalFailure     | Irreversible domain failure    |
| NotExisting         | Transition → Pending          | Success            | Pending (Transient)           | TransientFailure    | Retry allowed                  |
| NotExisting         | Invalid Transition            | —                  | FulfillmentFailed             | InvalidState        | Domain invariant violation     |
| Pending             | Transition → Fulfilled        | Success            | FulfillmentCompleted          | —                   | Normal continuation            |
| Pending             | Transition → Pending          | Success            | Pending (Transient)           | TransientFailure    | Still retryable                |
| Pending             | Transition → Failed           | Success            | FulfillmentFailed             | TerminalFailure     | Final failure                  |
| Pending             | Invalid Transition            | —                  | FulfillmentFailed             | InvalidState        | Corrupted flow                 |
| Fulfilled            | Any                           | —                  | NoOp / AlreadyFulfilled       | —                   | Idempotent no-op               |
| Failed (Terminal)    | Any                           | —                  | NoOp / AlreadyFailed          | TerminalFailure     | Final state                    |
| Any                  | Any                           | Persistence Error  | Pending (Transient)           | TransientFailure    | Persistence is retryable       |

---

## Interpretation Rules

- Domain decisions **always precede** persistence
- Persistence failure **always results in a retryable outcome**
- Final states **short-circuit execution**
- `NoOp` is a valid and successful outcome
- `InvalidState` indicates a bug or corrupted input

---

## Design Guarantees

This table guarantees that:

- all execution paths are covered
- retry semantics are explicit
- no implicit decisions exist in code
- outcomes are predictable and testable

Any behavior **not representable in this table**
is considered a **design error**.

---

## Change Policy

This table changes **only if**:

- business meaning changes
- new domain states are introduced

Infrastructure changes **must not affect** this table.

---

## Architect Note

If future code contradicts this table,  
**the code is wrong — not the table**.
