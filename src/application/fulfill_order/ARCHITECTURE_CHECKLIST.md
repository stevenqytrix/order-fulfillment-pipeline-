# FulfillOrder – Architecture Checklist

This document is an **anti-regression checklist**
for the `FulfillOrder` application use case.

It exists to **protect architectural intent** over time.

Any change to this use case **must be validated**
against this checklist.

If a rule is violated, the implementation is wrong.

---

## Purpose

This checklist ensures that:

- architectural boundaries remain intact
- business meaning is not leaked into infrastructure
- retry and failure semantics stay explicit
- complexity does not silently creep in

It is **not optional**.

---

## General Principles

- The application layer **orchestrates**, it does not decide business rules
- The domain **decides**, it does not perform side effects
- Infrastructure **executes**, it does not define meaning
- Every execution path is **explicit and classified**

---

## Use Case Structure

- [ ] The use case contains **no infrastructure code**
- [ ] The use case contains **no delivery logic**
- [ ] The use case contains **no retry policy**
- [ ] The use case depends **only on ports**
- [ ] All dependencies point **inward**

---

## Flow Integrity (FLOW.md)

- [ ] The execution order follows `FLOW.md` exactly
- [ ] Domain decisions happen **before** persistence
- [ ] Persistence happens **before** side effects
- [ ] Side effects never influence decisions
- [ ] Final states short-circuit execution

---

## Input / Output Contract (CONTRACT.md)

- [ ] Inputs are treated as **potentially duplicated**
- [ ] Inputs are treated as **out-of-order**
- [ ] Outputs are **deterministic**
- [ ] Repeated invocations return the same Outcome
- [ ] No Outcome depends on infrastructure behavior

---

## Outcome Model (OUTCOME.md)

- [ ] Every execution returns **exactly one Outcome**
- [ ] Outcomes replace exceptions
- [ ] Outcomes are explicit and exhaustive
- [ ] No Outcome is ambiguous
- [ ] Adapters must not reinterpret Outcomes

---

## Failure Semantics (FAILURES.md)

- [ ] Failures are **semantic**, not technical
- [ ] Terminal failures are irreversible
- [ ] Transient failures are retryable
- [ ] InvalidState represents a bug or corrupted input
- [ ] Failure types are never inferred implicitly

---

## Decision Consistency (DECISION_TABLE.md)

- [ ] Every execution path maps to **one table row**
- [ ] Retry vs stop behavior is explicit
- [ ] Persistence failure is always retryable
- [ ] No conditional logic contradicts the table
- [ ] The table is the **single source of truth**

---

## Ports and Boundaries

- [ ] Repositories guarantee **state consistency**
- [ ] Side effects tolerate **duplicate execution**
- [ ] Consumers assume **at-least-once delivery**
- [ ] Idempotency is enforced **downstream**
- [ ] No adapter leaks concerns upward

---

## Forbidden Patterns

The following are **not allowed**:

- [ ] throwing exceptions for control flow
- [ ] implicit retries
- [ ] hidden side effects
- [ ] business rules in adapters
- [ ] infrastructure-driven decisions

---

## Change Discipline

This checklist must be revisited if:

- business meaning changes
- new domain states are introduced
- new Outcomes are added

Infrastructure changes **must not** require changes here.

---

## Architect Rule

If future code contradicts:

- `FLOW.md`
- `CONTRACT.md`
- `OUTCOME.md`
- `FAILURES.md`
- `DECISION_TABLE.md`
- **or this checklist**

👉 **the code is wrong, not the documents.**

This file is the final guardrail.
 
