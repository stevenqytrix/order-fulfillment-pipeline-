# FulfillOrder – Reference Implementation Skeleton

This directory contains a **minimal reference skeleton**
for the `FulfillOrder` application use case.

Its purpose is to demonstrate **how the previously defined
architecture translates into code structure**,
without introducing business logic or infrastructure choices.

This is **not a full implementation**.

---

## Design Intent

This skeleton:

- mirrors the application flow defined in `FLOW.md`
- respects the input/output contract in `CONTRACT.md`
- enforces the Outcome Model in `OUTCOME.md`
- follows the Decision Table in `DECISION_TABLE.md`
- preserves all architectural boundaries

All logic decisions remain **external to this skeleton**.

---

## What This Skeleton Contains

- a use case entry point
- explicit dependency injection via ports
- a single execution method
- placeholders for domain, persistence, and side effects

---

## What This Skeleton Does NOT Contain

- business rules
- retry logic
- exception handling
- framework annotations
- infrastructure code
- conditional decision logic

Any future implementation must **respect the documents**
defined alongside this skeleton.

If code contradicts the architecture,  
**the code is wrong**.
