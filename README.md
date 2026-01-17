# Order Fulfillment Pipeline

A production-oriented backend project focused on **asynchronous processing,
failure handling, idempotency, retries, dead-letter queues, and eventual consistency**.

This repository is intentionally minimal in features and maximal in correctness.
Its purpose is to demonstrate how a backend system behaves **when things go wrong**.

---

## Motivation

Order fulfillment is not a CRUD problem.

It is an **asynchronous, failure-prone workflow** involving:
- non-atomic steps
- external dependencies
- retries and partial progress
- duplicated or delayed messages

Most systems fail not because of missing features,
but because failure paths were not designed explicitly.

This project exists to model those failure paths correctly.

---

## Problem Definition

Given an `OrderCreated` event, the system must attempt to fulfill the order
through a sequence of asynchronous steps.

Each step:
- may fail
- may timeout
- may be retried
- may be executed more than once

The system must guarantee:
- no duplicated side effects
- no corrupted order state
- no infinite retry loops
- controlled degradation under failure

---

## High-Level Flow

1. An `OrderCreated` event is emitted
2. A fulfillment consumer processes the event asynchronously
3. The workflow progresses through multiple steps
4. Failures are classified as retryable or non-retryable
5. Retryable failures are retried with bounded attempts
6. Non-recoverable messages are sent to a Dead Letter Queue
7. The system eventually converges to a consistent final state

---

## Core Design Decisions

### Asynchronous by Default
Fulfillment is decoupled from request/response boundaries.
No synchronous guarantees are assumed.

### Idempotency as a First-Class Concern
All handlers are designed to be idempotent.
Duplicate messages must not produce duplicate effects.

### Explicit Retry Policy
Retries are intentional, bounded, and observable.
Blind retries are considered a system failure.

### Dead Letter Queue (DLQ)
Poison messages are isolated to prevent cascading failures.
DLQ is a recovery mechanism, not an error dump.

### Eventual Consistency
Immediate consistency is not required.
The system guarantees convergence to a valid state over time.

---

## What This Project Does NOT Do

- No microservices explosion
- No framework-driven abstractions
- No event sourcing
- No synchronous orchestration
- No UI or presentation concerns

The focus is strictly on **backend correctness under failure**.

---

## Why This Matters

In real production systems:
- networks fail
- messages are duplicated
- dependencies are unreliable

A backend engineer is defined not by how systems behave when everything works,
but by how they behave **when everything breaks**.

This repository demonstrates that mindset.

---

## Intended Audience

This project is intended for backend engineers and technical reviewers
interested in how asynchronous systems behave under failure conditions.

It is designed to be read before being run.

The primary artifact is the reasoning behind the design,
not the amount of code produced.

 
