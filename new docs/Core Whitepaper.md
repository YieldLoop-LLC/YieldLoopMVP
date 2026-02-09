# YieldLoop  
## Core Whitepaper — System Specification

**Author:** Todd Koletsky  
**Date:** February 9, 2026  
**Document Role:** Normative / Canonical  

---

## 1. Purpose

This document defines the **canonical behavior** of the YieldLoop system.

It specifies:
- system structure
- vault model
- execution constraints
- cycle mechanics
- settlement rules
- safety guarantees

This document is **authoritative**.  
All other documents are explanatory, derivative, or additive.

If any external description conflicts with this document, **this document governs**.

---

## 2. System Overview

YieldLoop is an automated trading system built on:
- isolated user vaults
- fixed execution cycles
- deterministic settlement
- explicit exit paths

The system does not pool user funds, socialize losses, or rely on reflexive dynamics.

---

## 3. Vault Model

### 3.1 Vault Isolation

Each user interacts with a **dedicated vault**.

A vault:
- holds only one user’s assets
- maintains independent accounting
- cannot access other vaults
- cannot be accessed by other vaults

There is no shared balance, margin, or liquidity layer.

---

### 3.2 Vault Capabilities

A vault may:
- accept deposits
- approve strategies
- authorize execution
- settle positions
- process withdrawals

A vault may not:
- initiate execution independently
- bypass control logic
- alter global parameters

---

## 4. Vault Control (Brains)

Each vault is governed by a **Vault Brain**.

The Vault Brain:
- enforces lifecycle state
- validates user approvals
- authorizes execution
- enforces guardrails
- coordinates settlement

The Vault Brain does not:
- execute trades
- hold assets
- set economic policy

---

## 5. Lifecycle States

Each vault operates under a deterministic state machine:

- `IDLE`
- `READY`
- `ACTIVE`
- `SETTLING`
- `SETTLED`
- `EMERGENCY`

State transitions are:
- explicit
- logged
- non-reentrant

Invalid transitions revert.

---

## 6. Cycles

### 6.1 Cycle Definition

YieldLoop operates in **fixed-duration cycles**.

A cycle:
- defines execution windows
- bounds risk exposure
- enforces accounting finality

No vault may participate in more than one cycle simultaneously.

---

### 6.2 Cycle Events

Each cycle includes:
- activation
- execution window
- closure
- settlement

Execution outside an active cycle is prohibited.

---

## 7. Execution Model

### 7.1 Execution Authorization

Execution may occur only when:
- the vault is in `ACTIVE` state
- the strategy is approved
- guardrails are satisfied

Execution engines are **stateless** and **permissioned**.

---

### 7.2 Execution Characteristics

Execution is:
- opportunistic
- conditional
- abortable
- non-continuous

The absence of execution is a valid outcome.

---

## 8. Strategies

Strategies are defined as:
- permitted action sets
- constraints
- exit conditions

Strategies do not:
- guarantee profit
- imply execution frequency
- bypass guardrails

---

## 9. Market Interaction

External market inputs (DEXs, oracles, feeds) are treated as:
- untrusted
- bounded
- validated

All interactions are subject to:
- slippage limits
- liquidity checks
- price sanity bounds
- gas constraints

Failure of any check aborts execution without state mutation.

---

## 10. Settlement

### 10.1 Settlement Trigger

Settlement occurs:
- at cycle end
- upon emergency exit
- upon explicit close conditions

---

### 10.2 Settlement Rules

At settlement:
- all positions are closed or marked
- realized P/L is finalized
- performance fees (if any) are applied
- settlement output is selected

Settlement is:
- atomic per vault
- deterministic
- irreversible

---

## 11. Fees

YieldLoop charges **performance fees only**.

- Fees apply only to realized profit
- No profit → no fee
- Fees are calculated at settlement

Fee rates and allocations are defined in the **Performance Fee Addendum**.

---

## 12. Settlement Outputs

Users may select:
- stablecoin settlement
- LOOP settlement (optional)

Settlement selection does not alter:
- execution behavior
- fee calculation
- vault rules

---

## 13. LOOP (System Receipt Mechanism)

LOOP is an optional receipt mechanism representing a claim on realized system value.

LOOP:
- is backed by realized profit
- is governed by a ratcheting floor
- does not dilute retroactively
- is not required to use the system

Detailed mechanics are defined in the **LOOP Redemption Model**.

---

## 14. Safety and Emergency Handling

### 14.1 Emergency Conditions

Emergency states may be triggered by:
- execution failure
- oracle failure
- market anomalies
- governance intervention

---

### 14.2 Emergency Behavior

In emergency:
- execution is disabled
- deposits may be paused
- withdrawals remain available
- conservative unwinds are attempted

The system prioritizes:
- access
- determinism
- containment

---

## 15. Liveness Guarantees

The system is designed to:
- fail closed
- preserve exit paths
- avoid deadlock
- prevent permanent fund lockup

No component may require discretionary intervention to allow exit.

---

## 16. Governance and Change Control

System changes occur via:
- governance
- timelocked updates
- formal addenda

Silent or retroactive changes are prohibited.

---

## 17. Non-Goals

YieldLoop does not:
- guarantee returns
- provide capital protection
- optimize for maximum activity
- rely on user behavior for stability

---

## 18. Summary

YieldLoop is defined by:
- isolated vaults
- explicit authorization
- bounded execution
- deterministic settlement
- guaranteed exits

This document defines **what the system is**.

---

**End of Core Whitepaper**
