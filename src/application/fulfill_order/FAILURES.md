# FulfillOrder – Failure Types

This document defines the **semantic failure types**
used by the `FulfillOrder` application use case.

Failures are **domain-meaningful outcomes**,
not technical errors or exceptions.

They describe *why* fulfillment did not complete,
and *how the system must react*.

---

## Design Principle

Failure types:

- are **explicit**
- are **stable**
- drive **control flow**
- determine **retry vs stop**
- are independent from infrastructure or technology

They are part of the **application contract**.

---

## Failure Categories

### 1. TerminalFailure

A **TerminalFailure** represents an **irreversible outcome**.

#### Characteristics

- The fulfillment can never succeed
- Re-execution will always lead to the same result
- The state is final and persisted
- No retry must change the outcome

#### Examples

- Order permanently rejected
- Business rule violation that cannot be corrected
- External dependency definitively refused fulfillment

#### System Behavior

- Persist final failure state
- Execute failure side effects
- Do **not** retry
- Future invocations return the same outcome

---

### 2. TransientFailure

A **TransientFailure** represents a **temporary inability**
to complete fulfillment.

#### Characteristics

- Fulfillment may succeed in the future
- The state is not final
- Re-execution is allowed and expected

#### Examples

- Temporary external system unavailability
- Rate limits
- Network or timeout-related conditions (abstracted)

#### System Behavior

- Persist intermediate or pending state
- Execute optional side effects
- Allow retries
- Remain idempotent under repetition

---

### 3. NoOp / AlreadyFulfilled

A **NoOp** represents a **valid invocation**
that produces **no state change**.

#### Characteristics

- The order is already fulfilled
- The requested transition has already been applied
- The system is already in the correct final state

#### Examples

- Duplicate message
- Replayed event
- Concurrent invocation after completion

#### System Behavior

- Do not modify state
- Do not re-execute side effects (or do so idempotently)
- Return a deterministic fulfilled outcome

This is **not an error**.

---

### 4. InvalidState

An **InvalidState** represents an **illegal transition attempt**
according to domain rules.

#### Characteristics

- The current state does not allow fulfillment
- The transition is rejected by the domain
- The outcome is deterministic

#### Examples

- Attempting fulfillment from a cancelled state
- State corruption detected by domain invariants

#### System Behavior

- Treat as a terminal failure
- Persist failure if appropriate
- Do not retry

---

## Non-Failure Outcomes

Not all non-success outcomes are failures.

The following are **explicitly not failures**:

- AlreadyFulfilled
- Idempotent replays
- No state change due to duplicate invocation

These are **expected system behavior**.

---

## Explicit Non-Responsibilities

Failure types do NOT define:

- exception hierarchies
- error codes
- logging strategy
- retry mechanisms
- infrastructure responses

Those concerns belong to outer layers.

---

## Invariants

- Every failure is intentional and meaningful
- Retry decisions are driven by failure type
- Failure semantics never depend on infrastructure
- New failure types must preserve existing contracts

Any implementation that treats failures
as generic errors is considered incorrect.
 
