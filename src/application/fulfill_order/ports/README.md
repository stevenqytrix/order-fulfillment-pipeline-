# Application Ports

This directory defines the **application-level ports**
required by the `FulfillOrder` use case.

Ports represent **boundaries**, not implementations.

They describe *what the application needs* in order to execute,
without assuming *how those needs are fulfilled*.

---

## Purpose

The ports in this module allow the application layer to:

- persist fulfillment state safely
- execute side effects after a fulfillment decision
- remain independent from infrastructure concerns

They enforce **inversion of control**.

---

## Defined Ports

### FulfillmentRepository

Responsible for:
- loading fulfillment state
- persisting state transitions
- enforcing idempotency at the persistence boundary

It guarantees **state consistency**, not delivery guarantees.

See: `FulfillmentRepository/`

---

### FulfillmentSideEffects

Responsible for:
- executing effects after a fulfillment decision
- emitting domain-relevant outcomes
- tolerating repeated execution

It executes **effects**, never decisions.

See: `FulfillmentSideEffects/`

---

## Design Rules

All ports must:

- be technology-agnostic
- define explicit contracts
- tolerate duplicate invocation
- remain stable under infrastructure change

Any adapter violating a port contract is incorrect by definition.

---

## What Is Explicitly Excluded

Ports do NOT define:

- infrastructure configuration
- messaging systems
- retry policies
- transactional orchestration
- performance optimizations

Those concerns belong to adapters.

