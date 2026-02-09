# YieldLoop — Core Whitepaper  
## System Specification (Canonical)

**Author:** Todd Koletsky  
**Date:** February 9, 2026  
**Status:** Canonical Source of Truth  

---

## 0. Purpose and Authority

This document defines **what YieldLoop is**, mechanically and unambiguously.

If there is any conflict between:
- marketing material
- a prospectus
- a dashboard label
- a community explanation
- a social post
- a prior draft

**This document governs.**

This document is:
- normative
- build-authoritative
- audit-authoritative

All other YieldLoop documents must be consistent with it or explicitly defer to it.

---

## 1. System Overview

YieldLoop is a **non-custodial, automated trading system** built around **isolated user vaults** and **cycle-based execution**.

Core design principles:
- One user → one vault
- No pooled user funds
- No socialized losses
- Explicit execution boundaries
- Explicit settlement events
- User-controlled exits

YieldLoop does not guarantee profit, yield, or capital preservation.

---

## 2. Custody and Ownership Model

- Users retain ownership of their funds at all times.
- Funds are held on-chain in protocol-controlled contracts.
- YieldLoop does not custody user funds off-chain.
- YieldLoop does not rehypothecate user assets.
- YieldLoop does not use user assets to collateralize other users.

Each vault is logically and accounting-isolated.

---

## 3. Vault Architecture

### 3.1 Vault Definition

A **vault** is a protocol-controlled container that:
- Is associated with a single user wallet
- Holds only that user’s assets
- Executes independently
- Settles independently

Vaults do not share:
- balances
- margin
- collateral
- execution state

---

### 3.2 Vault Invariants

The following invariants must always hold:

- A vault can only execute strategies approved by its owner.
- A vault cannot assume liabilities created by other vaults.
- A vault’s losses are bounded to its own balance.
- A vault may exist in only one lifecycle state at a time.

---

## 4. Supported Assets

- YieldLoop supports a defined set of approved assets.
- Asset eligibility is determined by governance or system configuration.
- Unsupported assets cannot be deposited or traded.

Asset support does not imply:
- price stability
- liquidity guarantees
- execution guarantees

---

## 5. Deposit Rules

### 5.1 Initial Deposit

- A wallet’s first deposit into YieldLoop must be **≥ $1,000** (or equivalent).
- This establishes the initial vault.

### 5.2 Subsequent Deposits

- Subsequent deposits into an existing vault must be **≥ $100**.
- Subsequent deposits do not reset cycle state unless explicitly configured.

Deposits:
- are user-initiated
- are not pooled
- do not guarantee immediate execution

---

## 6. Strategy Proposal and Approval

### 6.1 Strategy Proposal

Before execution begins, the system generates a **strategy proposal** describing:
- strategy category
- expected behavior
- execution constraints
- risk characteristics

Strategy proposals do **not** include:
- profit projections
- APY estimates
- performance guarantees

---

### 6.2 User Approval

Execution requires explicit user approval:
- Approval is wallet-signed
- Approval is vault-specific
- Approval is cycle-specific

Without approval:
- no strategy executes
- the vault remains idle

---

## 7. Execution Model (High-Level)

- Execution occurs only within an active cycle.
- Execution is opportunistic, not continuous.
- The system may execute zero trades during a cycle.
- Execution may pause or abort if guardrails are violated.

Execution behavior is bounded by:
- liquidity constraints
- slippage limits
- gas constraints
- safety checks

---

## 8. Cycle Mechanics

### 8.1 Cycle Definition

A **cycle** is a fixed execution window with:
- a defined start
- a defined end
- a single settlement event

Cycles exist to:
- bound risk
- enforce accounting
- create explicit exit points

---

### 8.2 Cycle States

A vault may exist in one of the following cycle states:
- Idle
- Active
- Settling
- Settled
- Emergency

State transitions are explicit and logged.

---

## 9. Settlement and Accounting

At settlement:
- All open positions are closed or marked
- Profit or loss is realized
- Performance fees (if applicable) are calculated
- Incentives (if applicable) are applied

Settlement is:
- atomic
- final
- non-reversible

---

## 10. Profit, Loss, and Fees (Existence Only)

- Profit and loss are determined per vault.
- Losses are never socialized.
- Performance fees apply **only to realized profit**.
- If no profit exists, no performance fee is charged.

Fee rates, discounts, and incentive programs are defined in a separate document.

---

## 11. Redemption Paths

At settlement, users may choose among available redemption paths, including:
- direct stablecoin withdrawal
- optional protocol receipt mechanisms

Redemption paths:
- are optional
- are explicitly selected
- do not affect vault ownership

No redemption path is mandatory.

---

## 12. Emergency States and Liveness

### 12.1 Emergency Conditions

The system may enter an emergency state due to:
- execution failures
- market dislocations
- oracle failures
- security concerns

---

### 12.2 Emergency Behavior

In an emergency state:
- new execution may be halted
- deposits may be paused
- users retain the ability to request exits

Emergency exits prioritize:
- access
- determinism
- clarity

Not price optimization.

---

## 13. Liveness Guarantees

YieldLoop guarantees:
- users can always request an exit
- vaults cannot be trapped indefinitely
- emergency states are explicit

The system favors **exit over optimization**.

---

## 14. System Non-Goals (Explicit)

YieldLoop does **not**:
- guarantee yield
- guarantee capital preservation
- pool user funds
- hide risk behind projections
- optimize for constant activity
- require token participation

---

## 15. Change Control

This document may be modified only by:
- explicit versioned updates, or
- formal addenda

Silent modification is not permitted.

---

## 16. Summary

YieldLoop is defined by:
- isolation
- explicit consent
- bounded execution
- deterministic settlement
- user-controlled exits

This document defines **what the system is**.

All other documents exist to explain, contextualize, or extend it.

---

**End of Core Whitepaper**
