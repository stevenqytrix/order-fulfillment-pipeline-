# FulfillmentSideEffects

This port defines the **side-effect boundary**
required by the FulfillOrder use case.

It represents all effects that must happen
*after* a fulfillment state transition is decided.

No delivery mechanism or infrastructure is assumed.

---

## Responsibility

The FulfillmentSideEffects port is responsible for:

- emitting domain-relevant fulfillment outcomes
- triggering external reactions to fulfillment completion
- ensuring side effects are consistent with the final state

It executes **effects**, not decisions.

---

## When It Is Invoked

Side effects are invoked **only after**:

- the domain has validated the state transition
- the fulfillment state has been persisted successfully

Side effects must never influence domain decisions.

---

## Expected Effects

Conceptually, side effects may include:

- emitting a "Fulfilled" event
- emitting a "FulfillmentFailed" event
- notifying downstream systems
- publishing messages or signals

The use case does not care *how* these are implemented.

---

## Idempotency Expectation

Side effects **may be executed more than once**.

Implementations must tolerate duplicate invocations
without producing duplicated external effects.

Idempotency is required at this boundary.

---

## Explicit Non-Responsibilities

This port does NOT define:

- messaging technology
- delivery guarantees
- retry policies
- transactional coupling with persistence
- infrastructure configuration

Those concerns belong to adapters.

---

## Design Constraint

This contract must remain valid even if:

- the messaging system changes
- delivery semantics evolve
- infrastructure is replaced

Only the **meaning of the effect** is stable.

Any implementation that violates this contract
is considered incorrect.

