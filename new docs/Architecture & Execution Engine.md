# YieldLoop — Architecture & Execution Engines  
## Internal System Design and Execution Flow

**Author:** Todd Koletsky  
**Date:** February 9, 2026  
**Status:** Canonical Implementation Reference  

---

## 0. Purpose and Scope

This document defines **how YieldLoop is implemented internally**.

It explains:
- Core system components
- Execution engines
- Vault “brains”
- State machines
- Control flow
- Safety interlocks

If Document 2 defines **what the system is**,  
this document defines **how it behaves under the hood**.

This document is implementation-authoritative but **not economic-authoritative**.

---

## 1. Architectural Overview

YieldLoop is composed of four primary layers:

1. **Vault Layer** — isolated user containers  
2. **Control Layer (Vault Brains)** — per-vault logic and state  
3. **Execution Layer** — trading and strategy engines  
4. **Safety & Coordination Layer** — guardrails, liveness, emergency control  

No layer may bypass another.

---

## 2. Vault Layer

### 2.1 Vault Contracts

Each vault:
- Is instantiated per user
- Holds only that user’s assets
- Maintains its own accounting
- Exposes a constrained execution interface

Vaults do **not**:
- Call other vaults
- Share balances
- Share margin
- Share execution context

---

### 2.2 Vault Interfaces

Vaults expose only:
- deposit
- withdraw
- approve strategy
- query state
- emergency exit request

Vaults do not:
- decide strategy
- fetch market data
- execute trades directly

---

## 3. Vault Brains (Control Layer)

### 3.1 Definition

A **Vault Brain** is the logical controller for a vault.

It:
- Maintains vault state
- Enforces lifecycle rules
- Validates approvals
- Authorizes execution

Each vault has exactly one active brain.

---

### 3.2 Brain Responsibilities

Vault Brains are responsible for:
- State transitions
- Strategy authorization
- Execution permissioning
- Guardrail enforcement
- Emergency escalation

Brains do **not**:
- Execute trades
- Hold funds
- Generate incentives

---

### 3.3 Brain State Machine

Each brain operates a deterministic state machine:

- `IDLE`
- `READY`
- `ACTIVE`
- `SETTLING`
- `SETTLED`
- `EMERGENCY`

Transitions are:
- Explicit
- Logged
- Non-reentrant

Invalid transitions must revert.

---

## 4. Execution Layer

### 4.1 Execution Engines

Execution Engines are stateless components that:
- Receive authorized instructions
- Perform market interactions
- Return execution results

They operate **only** when:
- Authorized by a Vault Brain
- Within an active cycle
- Within guardrail limits

---

### 4.2 Execution Characteristics

Execution is:
- Opportunistic
- Conditional
- Abortable
- Non-continuous

The absence of execution is a valid outcome.

---

### 4.3 Strategy Abstraction

Strategies are abstracted as:
- A set of permitted actions
- Constraint definitions
- Exit conditions

Execution engines never decide:
- when to trade
- what risk to take

They only perform **authorized actions**.

---

## 5. Market Interaction

### 5.1 External Dependencies

Execution engines may interact with:
- DEXs
- Liquidity venues
- Oracles
- Price feeds

All external inputs are treated as:
- Untrusted
- Bounded
- Validated

---

### 5.2 Validation Layer

Before execution:
- Price sanity checks
- Liquidity checks
- Slippage bounds
- Gas cost ceilings

Failure of any check:
- Aborts execution
- Does not change vault state

---

## 6. Safety & Guardrail Layer

### 6.1 Guardrail Categories

Guardrails include:
- Capital exposure limits
- Market depth constraints
- Slippage thresholds
- Time-based aborts
- Oracle deviation bounds

Guardrails are:
- Configurable
- Conservative
- Enforced pre-execution

---

### 6.2 Guardrail Enforcement

Guardrails:
- Cannot be bypassed by execution engines
- Cannot be bypassed by user approval
- Must be enforced by the Vault Brain

Guardrail violations result in:
- Execution abort
- Optional escalation

---

## 7. Cycle Coordination

### 7.1 Cycle Controller

A global Cycle Controller:
- Defines cycle windows
- Signals start and end
- Coordinates settlement timing

Vault Brains subscribe to cycle events but remain isolated.

---

### 7.2 Cycle Isolation

- Cycles do not share execution context
- A vault cannot span multiple cycles simultaneously
- Settlement must complete before the next cycle begins

---

## 8. Settlement Engine

### 8.1 Settlement Responsibilities

The Settlement Engine:
- Closes or marks positions
- Computes P/L
- Signals fee calculation
- Finalizes vault state

Settlement is:
- Deterministic
- Atomic per vault
- Non-reversible

---

### 8.2 Settlement Failure Handling

If settlement fails:
- Vault enters `EMERGENCY` state
- No new execution is permitted
- User exits are prioritized

---

## 9. Emergency Control Plane

### 9.1 Emergency Triggers

Emergency state may be triggered by:
- Repeated execution failures
- Oracle failures
- Contract anomalies
- Governance intervention

---

### 9.2 Emergency Behavior

In emergency state:
- Execution engines are disabled
- Deposits may be paused
- Withdrawals remain available
- Vaults unwind conservatively

Emergency mode favors:
- determinism
- access
- containment

---

## 10. Liveness and Fail-Safe Design

YieldLoop is designed to:
- Fail closed
- Preserve exit paths
- Avoid deadlocks

No component may:
- Permanently trap a vault
- Require external intervention to exit

---

## 11. Observability and Logging

All critical actions are:
- Logged
- Timestamped
- Attributable

This includes:
- State transitions
- Execution attempts
- Guardrail violations
- Emergency triggers

---

## 12. Non-Goals (Implementation)

The architecture does **not**:
- Optimize for maximum throughput
- Optimize for lowest latency
- Prioritize execution frequency
- Allow discretionary overrides

Correctness > speed.

---

## 13. Relationship to Other Documents

- Economic flows → Document 4
- Genesis and access → Document 5
- Risk philosophy → Document 6
- System evolution → Document 7

This document defines **how the system runs**, not why.

---

## 14. Summary

YieldLoop’s architecture is built around:
- isolation
- explicit authorization
- constrained execution
- deterministic settlement
- survivability under stress

Every component exists to reduce the chance that one failure becomes many.

---

**End of Architecture & Execution Engines Document**
