## Governance and Timelock Specification

### Purpose

This section defines the governance, administrative control model, and timelock protections for YieldLoop.

The goal is to ensure:

- No single actor can unilaterally control the protocol.
- High-risk actions are delayed and observable.
- Users have time to react to proposed changes.
- Core invariants cannot be overridden by governance.

---

### Governance Model Overview

YieldLoop v1 uses a **multisignature-controlled administrative model with timelocked execution**.

Governance consists of:

- A multisig Admin Council.
- A timelock contract governing high-impact actions.
- Hard-coded immutable invariants.

YieldLoop does not use on-chain token voting in v1.

---

### Admin Council (Multisig)

- Default multisig threshold: **3-of-5**
- Temporary bootstrap minimum: **2-of-3** (time-limited)
- Keys must be held by independent individuals.
- No single signer may control more than one key.

The Admin Council cannot directly execute sensitive actions.  
All sensitive actions must pass through timelock.

---

### Timelock

High-impact actions are queued into a timelock before execution.

- Minimum timelock delay: **72 hours**
- Recommended delay: **96 hours**

While queued:

- Action details are publicly visible.
- Users may exit the protocol before execution.

---

### Actions Requiring Timelock

The following actions MUST be timelocked:

- Performance fee adjustments (within hard-coded bounds)
- Fee routing split adjustments (within bounds)
- Token whitelist modifications (within bounds)
- Guardrail threshold adjustments (within bounds)
- Strategy type enable/disable
- Contract upgrades
- Pausing new deposits
- Resuming paused system

---

### Emergency Actions (No Timelock)

The Admin Council may immediately:

- Pause trading execution
- Pause new deposits
- Trigger global kill switch

Emergency actions:

- Cannot move user funds.
- Cannot change fees.
- Cannot mint LOOP.
- Cannot drain reserves.

Emergency actions are reversible only through timelock.

---

### Immutable Invariants (Hard-Coded)

The following can never be changed by admin, governance, or upgrade:

- LOOP minting without realized profit
- LOOP redemption pool solvency invariant
- No admin access to individual vault funds
- No forced liquidation outside defined closeout flow
- No performance fees on losses
- Principal withdrawal only after closeout
- Reserve cannot be primary LOOP backing

---

### Bounded Parameters

Certain parameters are adjustable only within hard-coded ranges:

- Performance fee: 5% – 20%
- Ratchet allocation: 40% – 70% of fee
- Dev/Ops allocation: 15% – 30% of fee
- Marketing allocation: 5% – 15% of fee
- Minimum deposit: $100 – $10,000
- Max slippage: 20 – 75 bps
- Max price impact: 20 – 75 bps
- Max trades per day: 1 – 24

Any attempt to exceed bounds must revert.

---

### Contract Upgrade Policy

- Contracts may be upgradeable in v1.
- All upgrades require timelock.
- Upgrade code must be published before execution.
- Upgrade cannot bypass immutable invariants.

---

### Transparency Requirements

All governance actions must emit events:

- ActionQueued
- ActionExecuted
- EmergencyPause
- EmergencyResume

Users and indexers must be able to monitor governance activity in real time.

---

### Design Intent

Governance exists to maintain system safety and evolve features.

Governance does not exist to extract value.

If YieldLoop governance ever becomes capable of unilateral value extraction, the design has failed.
