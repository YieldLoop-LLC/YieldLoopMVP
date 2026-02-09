# YieldLoop — System Architecture & Execution Engines  
**Vault Brains, Execution Logic, and Safety Systems**

**Author:** Todd Koletsky  
**Date:** February 9, 2026  
**Version:** v1.0 — Implementation-Grade Specification

---

## Document Purpose & Authority

This document defines **how YieldLoop actually works**.

It specifies:
- System components
- Execution engines
- Vault “brains”
- Decision logic
- Safety rails
- Emergency behavior
- Liveness guarantees

This document is written for:
- Core developers
- Smart contract engineers
- Auditors
- Infrastructure and ops engineers

This document assumes full familiarity with **Document 2 — Core Whitepaper**.

If there is a conflict:
- **Document 2 defines what must happen**
- **This document defines how it happens**

---

## 1. High-Level System Architecture

YieldLoop is composed of **four primary layers**:

1. Vault Layer (User-facing state)
2. Brain Layer (Decision logic)
3. Execution Layer (Action engines)
4. Safety & Control Layer (Overrides and guards)

Each layer is modular and loosely coupled.

No single layer may:
- Unilaterally move funds
- Override safety rules
- Skip explicit state transitions

---

## 2. Vault Layer (Execution Containers)

### 2.1 Vault Structure

Each vault maintains:
- Asset balances
- Strategy configuration
- Accounting state
- Execution permissions
- Lifecycle state
- Safety flags

Vaults are:
- Individually deployed or logically isolated
- Immutable in identity
- Mutable only through valid state transitions

---

### 2.2 Vault State Machine

Canonical states include:

- `CREATED`
- `FUNDED`
- `PROPOSED`
- `ACTIVE`
- `SETTLEMENT_PENDING`
- `SETTLED`
- `WITHDRAWABLE`
- `EMERGENCY_PENDING`
- `EMERGENCY_EXIT`
- `CLOSED`

State transitions:
- Are explicit
- Are logged
- Cannot be skipped

Backward transitions are prohibited except during emergency unwind.

---

## 3. Brain Layer (“Vault Brains”)

### 3.1 Brain Definition

A **Vault Brain** is a deterministic logic module that:
- Evaluates market conditions
- Selects strategies
- Enforces risk classification
- Generates strategy proposals
- Signals execution eligibility

Brains **do not** execute trades.

---

### 3.2 Brain Inputs

Brains consume:
- Vault configuration
- Asset type
- Risk tier
- Market data (prices, liquidity, spreads)
- System health signals
- Governance constraints

Brains are agnostic to chain and venue.

---

### 3.3 Brain Outputs

Brains may output:
- Strategy proposal
- No-op recommendation
- Pause recommendation
- Emergency signal

“No action” is always a valid output.

---

## 4. Strategy Proposal Engine

### 4.1 Proposal Generation

A proposal must include:
- Strategy type (e.g. arbitrage, yield, index)
- Execution constraints
- Risk classification
- Expected behavior description
- Lock and unlock conditions
- Abort conditions

Proposals are immutable once generated.

---

### 4.2 User Approval Gate

Execution requires:
- Explicit user approval
- Signed or on-chain confirmation
- Acknowledgment of constraints

No execution may begin without approval.

---

## 5. Execution Layer

### 5.1 Execution Engines

Execution engines are specialized modules that:
- Receive approved proposals
- Execute within defined bounds
- Report results back to the vault

Examples:
- Arbitrage Engine
- Yield Deployment Engine
- Index Rebalancer
- Liquidity Rotation Engine

Engines **cannot** modify rules.

---

### 5.2 Execution Rules

Execution engines must:
- Respect slippage limits
- Respect liquidity minimums
- Respect gas ceilings
- Respect time constraints

Violations abort execution.

---

### 5.3 Opportunistic Execution

Execution is:
- Event-driven
- Conditional
- Non-continuous

The system may wait indefinitely.

---

## 6. Safety & Guardrail System

### 6.1 Global Safety Invariants

The system must always enforce:
- Capital non-socialization
- Vault isolation
- Bounded loss per vault
- No forced liquidation across vaults

---

### 6.2 Guardrails (Non-Exhaustive)

Guardrails include:
- Max drawdown per vault
- Max gas cost per action
- Slippage caps
- Liquidity depth checks
- Price sanity checks
- Venue availability checks

Guardrails override strategy intent.

---

## 7. Emergency System

### 7.1 Emergency Modes

Two emergency modes exist:

**Soft Emergency**
- Vault-level
- Orderly unwind
- Attempts best-effort exit

**Hard Emergency**
- System-level
- Trading halted
- FIFO withdrawal processing

---

### 7.2 Emergency Triggers

Triggers may include:
- Engine malfunction
- Oracle failure
- Liquidity collapse
- Governance activation

Triggers are conservative and irreversible until cleared.

---

## 8. Emergency Withdrawal Flow

1. User requests emergency exit
2. Vault enters `EMERGENCY_PENDING`
3. Active positions are unwound if possible
4. Assets are returned as-is
5. Vault enters `CLOSED`

Losses may occur.

---

## 9. Accounting & Reporting

### 9.1 Execution Reporting

Each execution must report:
- Actions taken
- Costs incurred
- Slippage experienced
- Resulting balances

Reports are immutable.

---

### 9.2 Settlement Interface

At settlement:
- Execution reports are aggregated
- Profit/loss is finalized
- Fees are calculated
- Incentives are applied

---

## 10. Liveness Guarantees

The system guarantees:
- Vaults cannot be trapped indefinitely
- Emergency paths remain available
- Governance can halt execution

YieldLoop prioritizes **exit over optimization**.

---

## 11. Upgrade & Extensibility Model

### 11.1 Modular Engines

New engines may be added if they:
- Respect existing vault interfaces
- Enforce isolation
- Pass safety audits

---

### 11.2 Backward Compatibility

New logic must not:
- Change historical behavior
- Invalidate existing vaults
- Alter settled outcomes

---

## 12. Non-Goals (Explicit)

This architecture does not:
- Maximize trade frequency
- Optimize for speed over safety
- Hide complexity behind abstraction
- Allow discretionary overrides

---

## 13. Summary

YieldLoop’s architecture is deliberately constrained.

Brains decide.  
Engines execute.  
Guardrails override.  
Users approve.  
Vaults isolate.

Everything else is noise.

Proceed next to:

**Document 4 — User Experience & Dashboard Guide**
