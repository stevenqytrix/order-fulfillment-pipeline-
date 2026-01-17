# Consumer Layer

This module represents the **message consumption boundary**
of the order fulfillment pipeline.

It is responsible for receiving external events and invoking
the appropriate application use cases.

It contains **no business logic**.

---

## Responsibility

The consumer is responsible for:

- receiving order-related events from a messaging system
- extracting the required input data
- invoking the `FulfillOrder` use case
- reporting the outcome through infrastructure mechanisms

The consumer defines **how messages enter the system**, not how
fulfillment is decided.

---

## Delivery Semantics

The consumer operates under the following assumptions:

- messages may be delivered **more than once**
- message ordering is **not guaranteed**
- delivery semantics are **at-least-once**

Duplicate or out-of-order messages are expected
and must be tolerated.

---

## Failure Handling

If the invocation of the `FulfillOrder` use case fails:

- the message may be retried by the messaging system
- transient failures may result in re-delivery
- terminal failures may be routed to a dead-letter queue

Retry and routing policies are **external** to this module.

---

## Interaction with Application Layer

The consumer:

- delegates all business decisions to the application layer
- treats the `FulfillOrder` use case as idempotent
- does not attempt to deduplicate messages itself

Idempotency is enforced downstream.

---

## Explicit Non-Responsibilities

This module does NOT:

- guarantee exactly-once delivery
- enforce idempotency
- contain business rules
- manage retries explicitly
- define infrastructure configuration

Those concerns belong to outer layers.
