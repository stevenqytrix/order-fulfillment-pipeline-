# Fulfillment Domain

This module defines the **business rules and invariants**
governing the order fulfillment lifecycle.

It contains **no technical concerns**, no infrastructure assumptions,
and no knowledge of messaging systems, retries, or queues.

The domain expresses *what must always be true*,
not *how the system is implemented*.

---

## Domain Responsibility

The fulfillment domain answers a single question:

> Under which conditions can an order be considered fulfilled?

It does **not**:
- trigger fulfillment
- manage retries
- interact with external systems
- handle delivery mechanics

Those responsibilities belong to other layers.

---

## Core Concept

**Fulfillment** represents the business act of completing an order.

It is modeled as a **stateful process** with strict invariants,
not as a sequence of technical steps.

---

## Allowed States

A fulfillment instance may be in one of the following states:

- `PENDING` — fulfillment has not completed yet
- `FULFILLED` — fulfillment completed successfully
- `FAILED` — fulfillment reached a terminal failure

State transitions are **explicit and irreversible**.

---

## Core Invariants

The following rules must **always** hold:

- An order can be fulfilled **at most once**
- Fulfillment must be **idempotent by definition**
- Duplicate attempts must not produce duplicate effects
- A failed fulfillment must leave the system in a consistent state
- Partial progress must never corrupt the final outcome

Any implementation violating these rules is considered incorrect,
regardless of technical correctness.

---

## Failure Semantics

Failures are a **first-class domain concept**, not exceptional cases.

The domain distinguishes between:
- **transient failures** — the fulfillment outcome is still undecided
- **terminal failures** — the fulfillment outcome is final and failed

The domain defines *what failure means*.
Other layers decide *how to react*.

---

## Explicit Non-Responsibilities

This domain intentionally excludes:

- retry policies
- scheduling logic
- messaging semantics
- dead-letter queue handling
- infrastructure-level concerns

These exclusions are deliberate and enforced
to preserve domain purity and reasoning clarity.

---

## Design Philosophy

The fulfillment domain is designed to be:

- deterministic
- side-effect free
- implementation-agnostic
- stable under change

It must remain valid even if:
- the messaging system changes
- retries are reconfigured
- infrastructure is replaced

Only the **business rules** are permanent.
 
