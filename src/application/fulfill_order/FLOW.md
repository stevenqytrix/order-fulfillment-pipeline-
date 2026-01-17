# FulfillOrder – Application Flow

This document defines the **logical execution flow**
of the `FulfillOrder` application use case.

It describes **what happens and in which order**,
without specifying implementation details.

This sequence is **inviolable**.

---

## Preconditions

- The use case may be invoked multiple times
- Inputs may be duplicated, delayed, or out of order
- Idempotency must be preserved across executions

---

## Execution Flow

### Step 1 — Load Fulfillment State

- Load the current fulfillment state for the given order
- If no fulfillment exists, treat it as initial state
- No side effects occur at this stage

---

### Step 2 — Decide State Transition (Domain)

- Delegate transition decision to the domain
- Domain validates invariants and allowed transitions
- One of the following is produced:
  - successful transition
  - terminal failure
  - transient failure

No persistence or side effects occur here.

---

### Step 3 — Persist State Transition

- Persist the resulting fulfillment state
- Persistence must be idempotent
- If persistence fails, the flow stops immediately

This step establishes the **source of truth**.

---

### Step 4 — Execute Side Effects

- Invoke side effects **only after** successful persistence
- Side effects reflect the final persisted state
- Side effects must tolerate duplicate execution

Side effects must never influence state decisions.

---

### Step 5 — Return Outcome

- Return an explicit outcome to the caller:
  - fulfilled
  - failed (terminal)
  - pending (transient)

The outcome is observable and deterministic.

---

## Invariants

- Domain decisions always precede persistence
- Persistence always precedes side effects
- Side effects never affect domain or persistence
- The flow remains correct under repeated execution
 
