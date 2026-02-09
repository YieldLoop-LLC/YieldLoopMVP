# YieldLoop — Core Whitepaper  
**Automated Vault Trading Protocol**

**Author:** Todd Koletsky  
**Date:** February 9, 2026  
**Version:** v1.0 — Canonical Specification

---

## Document Authority & Scope

This document defines the **canonical ruleset** of the YieldLoop protocol.

It specifies:
- System invariants
- User-facing guarantees
- Economic rules
- Vault behavior
- Incentive boundaries
- Failure assumptions

It intentionally **does not** contain:
- Low-level execution algorithms
- Smart contract function definitions
- Chain-specific implementation details

Those live in **Document 3**.

If a developer understands this document, they should:
- Know what must be built
- Know what must never happen
- Know where flexibility exists
- Know where it does not

---

## 1. System Overview

### 1.1 Protocol Description

YieldLoop is a **self-custody, vault-isolated automated trading protocol**.

Users deposit assets into **individually isolated vaults** that:
- Execute strategies proposed by the system
- Operate on fixed cycles
- Settle discretely
- Never share balances with other users

There is **no pooled user capital**.

---

### 1.2 System Objectives

YieldLoop is designed to:
- Generate realized profit through disciplined execution
- Avoid reflexive token dynamics
- Remain solvent under adverse conditions
- Favor predictability over optimization

The protocol is allowed to underperform in exchange for survival.

---

## 2. Custody & Isolation Model

### 2.1 Self-Custody Principle

At all times:
- User assets remain on-chain
- Assets are attributed to a single vault
- No vault may borrow from another vault
- No vault may cover losses of another vault

There is no socialized risk.

---

### 2.2 Isolation Guarantees

The following are **hard invariants**:
- A vault failure cannot cascade
- A vault liquidation cannot drain shared liquidity
- Emergency exits are per-vault, not global by default

Global actions require explicit system-wide emergency states.

---

## 3. Vault Definition & State Model

### 3.1 Vault Definition

A vault is a **stateful execution container** with:
- Asset balances
- Strategy parameters
- Execution permissions
- Accounting state
- Lifecycle state

Vaults are immutable in identity but mutable in state.

---

### 3.2 Vault States (Abstract)

At minimum, a vault must support:
- `CREATED`
- `FUNDED`
- `PROPOSED`
- `ACTIVE`
- `SETTLING`
- `SETTLED`
- `WITHDRAWABLE`
- `EMERGENCY_PENDING`
- `CLOSED`

State transitions are **one-way where possible**.

---

### 3.3 Vault Lifecycle (Canonical)

1. Vault creation
2. Initial deposit
3. Strategy proposal generated
4. Explicit user approval
5. Execution cycle begins
6. Strategy executes opportunistically
7. Cycle ends
8. Settlement occurs
9. User chooses:
   - Withdraw
   - Continue
   - Reconfigure (if allowed)

No silent transitions exist.

---

## 4. Deposit Rules

### 4.1 Minimums

- Initial deposit per wallet: **$1,000**
- Subsequent deposits: **$100 minimum**

These rules exist to:
- Reduce spam
- Improve UX clarity
- Align with traditional finance norms

---

### 4.2 Deposit Timing

Deposits:
- Are accepted only when vault is not actively settling
- May be queued if a cycle boundary is near
- Never interrupt active execution

Deposits do not retroactively affect performance.

---

## 5. Strategy Proposal Model

### 5.1 Proposal Requirement

Before execution:
- The system generates a strategy proposal
- The user must explicitly approve it
- No execution occurs without approval

---

### 5.2 Proposal Contents (Abstract)

A proposal must include:
- Strategy category
- Asset exposure summary
- Risk classification
- Expected behavior (not returns)
- Lock conditions
- Exit conditions

Proposals describe **behavior**, not outcome.

---

## 6. Execution Behavior (Rules, Not Code)

### 6.1 Opportunistic Execution

Execution is:
- Conditional
- Non-continuous
- Environment-dependent

The system may choose **not to trade**.

---

### 6.2 Safety-First Priority

Execution must always respect:
- Capital preservation constraints
- Liquidity thresholds
- Slippage caps
- Gas cost sanity checks

No strategy may override safety rails.

---

## 7. Settlement & Accounting

### 7.1 Settlement Timing

Settlement occurs:
- At fixed cycle boundaries
- Or during emergency unwind

Settlement is atomic per vault.

---

### 7.2 Accounting Guarantees

At settlement:
- Profit or loss is finalized
- Performance fees are calculated
- Incentives are applied
- Vault balance is updated

No mid-cycle fee extraction exists.

---

## 8. Fees (Structural Rules)

### 8.1 Performance Fee Model

- Fees apply only to **realized gains**
- Losses do not incur fees
- Fees are assessed at settlement

Exact percentages are configurable but bounded.

---

### 8.2 Discounts & Eligibility

Discounts may apply based on:
- Genesis NFT ownership
- Whitelisted status

Discounts:
- Reduce fees
- Do not alter execution
- Do not change risk exposure

---

## 9. Incentive Programs (High-Level Rules)

### 9.1 Marketing-Funded Incentives

Incentives:
- Are budgeted expenses
- Are not yield
- Are not guaranteed
- Do not affect vault execution

---

### 9.2 Lifetime Genesis Incentives

Lifetime Genesis holders may receive:
- Fee discounts
- Marketing-funded incentives

These are:
- Explicit
- Capped
- Non-compounding

---

## 10. Emergency Behavior

### 10.1 Emergency Principles

Emergencies prioritize:
1. Capital access
2. Orderly unwind
3. System survival

Profit is irrelevant during emergency.

---

### 10.2 Emergency Triggers (Abstract)

Triggers may include:
- Execution failure
- Liquidity collapse
- Oracle failure
- Governance action

Triggers are conservative by design.

---

## 11. Risk Model (Explicit)

YieldLoop assumes:
- Markets can fail
- Liquidity can vanish
- Contracts can behave unexpectedly

The protocol mitigates risk via:
- Isolation
- Cycles
- Explicit consent
- Bounded incentives

There is no insurance layer.

---

## 12. Governance & Change Control

### 12.1 Stability Bias

Core protocol rules:
- Change slowly
- Change deliberately
- Prefer addenda over edits

---

### 12.2 Expansion Philosophy

New features:
- Are added via documented extensions
- Must not invalidate prior behavior
- Must preserve vault isolation

---

## 13. Non-Goals (Explicit)

YieldLoop will not:
- Optimize for max APY
- Chase trends
- Hide complexity behind marketing
- Socialize losses
- Promise outcomes

---

## 14. Summary

YieldLoop is a **constrained system** by design.

Its power comes from:
- What it refuses to do
- What it makes explicit
- What it isolates
- What it delays

This document defines the **rules of the machine**.

Implementation details follow in:

**Document 3 — System Architecture & Execution Engines**
