# YieldLoop Protocol Specification
**Version:** v1.2  
**Status:** Canonical / Source of Truth  
**Audience:** Auditors, Core Developers, Reviewers  
**Network:** BNB Chain (v1.2)

---

## 1. Purpose and Design Philosophy

YieldLoop is an automated, vault-based trading protocol designed to generate **realized trading profit** while eliminating common DeFi failure modes such as pooled risk, mark-to-market accounting, reflexive emissions, and forced recovery behavior.

The protocol is intentionally conservative.

Core principles:

- Realized-profit-only accounting
- Vault-level isolation (no pooled funds)
- Deterministic execution via state machines
- User-approved strategy execution only
- Fail-closed behavior under unsafe conditions
- Optional receipt token (LOOP) representing realized profit
- No leverage, no emissions, no projected yield

YieldLoop prioritizes durability, auditability, and honesty over growth or yield maximization.

---

## 2. Core System Invariants (Hard Rules)

The following invariants are **absolute** and must be enforced at the contract level:

- User funds are never pooled
- Each vault is isolated and independent
- Trading may occur only in explicitly permitted states
- Profit is credited only after settlement
- Profit is always denominated in USDT
- Performance fees apply only to realized profit
- No fees are charged on losses
- Principal may only be withdrawn after closeout
- LOOP cannot be minted without realized profit
- LOOP must always be solvent against its redemption pool
- Admins cannot seize, redirect, or reassign user funds
- Emergency actions cannot alter accounting outcomes

Any implementation violating these rules is invalid.

---

## 3. Vault Architecture and Isolation Model

### 3.1 Vault Structure

Each YieldLoop vault is an independent on-chain instance with:

- A single owner
- A single base asset
- An isolated balance ledger
- An isolated state machine
- An isolated execution lifecycle

There are no shared balances, no pooled capital, and no cross-vault dependencies.

---

### 3.2 Supported Assets (v1.2)

Vaults may only be funded using the following BEP-20 assets:

- BTCB  
- ETH  
- SOL  
- XRP  
- BNB  
- USDT  

Any attempt to deposit or route through non-whitelisted assets must revert.

---

### 3.3 Minimum Deposit

- **Minimum deposit:** $250 USDT (or equivalent at deposit time)
- Deposits below the minimum must revert
- Minimum applies per vault, not per user

---

## 4. Vault State Machine (Authoritative)

Each vault exists in **exactly one state** at all times.

### 4.1 Primary States

| ID | State |
|----|------|
| 0 | VAULT_CREATED |
| 1 | DEPOSIT_PENDING |
| 2 | FUNDED_IDLE |
| 3 | STRATEGY_PROPOSED |
| 4 | STRATEGY_APPROVED |
| 5 | EXECUTING |
| 6 | HOLDING_POSITION |
| 7 | SETTLEMENT_PENDING |
| 8 | PROFIT_CREDITED |
| 9 | WITHDRAW_PROFIT_PENDING |
| 10 | CLOSE_REQUESTED |
| 11 | CLOSE_PENDING |
| 12 | CLOSED |
| 13 | PAUSED |
| 14 | ERROR_LOCK |

---

### 4.2 Emergency & Liveness Substates

These are substates of `CLOSE_PENDING` or `PAUSED`:

- CLOSE_PENDING_PARTIAL  
- EMERGENCY_QUEUE_GAS  
- EMERGENCY_QUEUE_INCLUSION  
- EMERGENCY_QUEUE_ORACLE  
- EMERGENCY_QUEUE_DEX  

These substates do **not** permit trading.

---

### 4.3 Execution Permission Rule

Trades and swaps may occur **only** when:

vault.state ∈ { EXECUTING, CLOSE_PENDING }

All other states must revert trade attempts.

---

### 4.4 Valid State Transitions

#### Deposit Flow
- VAULT_CREATED → DEPOSIT_PENDING → FUNDED_IDLE

#### Strategy Flow
- FUNDED_IDLE → STRATEGY_PROPOSED
- STRATEGY_PROPOSED → STRATEGY_APPROVED
- STRATEGY_PROPOSED → FUNDED_IDLE (rejected or expired)

#### Execution Flow
- STRATEGY_APPROVED → EXECUTING
- EXECUTING → HOLDING_POSITION
- HOLDING_POSITION → EXECUTING
- EXECUTING → SETTLEMENT_PENDING
- SETTLEMENT_PENDING → PROFIT_CREDITED
- PROFIT_CREDITED → FUNDED_IDLE

#### Profit Withdrawal
- PROFIT_CREDITED → WITHDRAW_PROFIT_PENDING → PROFIT_CREDITED

#### Closeout Flow
- FUNDED_IDLE / PROFIT_CREDITED / HOLDING_POSITION → CLOSE_REQUESTED
- CLOSE_REQUESTED → CLOSE_PENDING
- CLOSE_PENDING → CLOSED

#### Failure / Pause
- ANY → PAUSED
- PAUSED → FUNDED_IDLE
- ANY → ERROR_LOCK
- ERROR_LOCK → FUNDED_IDLE (admin recovery only)

Illegal transitions must revert.

---

## 5. Strategy Generation and Approval

### 5.1 Strategy Object

All execution behavior is bound to a **Strategy Object**, which defines:

- Strategy type
- Allowed venues
- Routing constraints
- Trade sizing rules
- Slippage and price impact limits
- Liquidity requirements
- Oracle validation rules
- Gas bounds
- Expiration timestamp

---

### 5.2 User Approval

- Strategies must be explicitly approved by the vault owner
- Strategies are hash-bound
- Execution must revert if the approved hash does not match
- Strategies expire automatically to prevent stale execution

---

## 6. Execution Engine Behavior

### 6.1 Allowed Venues (v1.2)

Execution is limited to:

- PancakeSwap
- BiSwap

Routing to any other venue must revert.

---

### 6.2 Trade Quality Gates

A trade may execute only if **all** conditions pass:

- Expected net profit ≥ minimum threshold
- Slippage ≤ max slippage
- Price impact ≤ max impact
- Pool liquidity ≥ minimum liquidity
- Gas cost ≤ configured ceiling
- Oracle price valid and fresh

Failure of any gate causes the trade to be skipped, not degraded.

---

## 7. Settlement Overview

Settlement occurs only after all positions are closed.

- Unrealized P&L is ignored
- Losses reduce principal
- Profit is computed deterministically
- Fees are assessed only on realized profit

Full settlement math is defined in **Document 2: Accounting & Value Integrity Specification**.

---

## 8. LOOP Receipt Token (Canonical Model)

### 8.1 Definition

LOOP is a **mint-and-burn receipt token** representing realized net trading profit.

LOOP is:
- Not an emission token
- Not inflationary
- Not speculative
- Not required to use YieldLoop

---

### 8.2 Minting Rules

LOOP may be minted only if:

- A trade cycle has closed
- Settlement is complete
- Net realized profit exists
- Performance fees are deducted
- User selected LOOP payout mode
- Equivalent USDT is deposited into the LOOP Redemption Pool

Minting must be atomic and revert on failure.

---

### 8.3 Redemption and Burn

- LOOP may be redeemed at any time
- Redemption burns LOOP
- USDT is paid 1:1 from the Redemption Pool

---

### 8.4 Solvency Invariant

At all times:

Total LOOP Supply ≤ LOOP Redemption Pool USDT Balance

Violation must revert.

---

## 9. Emergency Withdrawal and Liveness Handling

### 9.1 Emergency Withdrawal Rules

- Emergency withdrawals are **USDT-only**
- LOOP payout preference is ignored
- Positions may be closed “as-is”
- Losses may occur
- Performance fees still apply if profit exists

---

### 9.2 Soft Emergency (Per Vault)

- User requests emergency withdrawal
- Vault enters CLOSE_REQUESTED → CLOSE_PENDING
- System attempts orderly unwind
- Partial closeouts permitted
- Dust below $0.50 USDT must not block closure

---

### 9.3 Hard Emergency (Global)

If a global emergency flag is set:

- Trading is disabled
- New deposits are disabled
- Emergency withdrawals enter FIFO queue
- Execution occurs sequentially
- No reordering permitted

---

### 9.4 Liveness Queues

Vaults may enter liveness substates due to:

- Gas spikes
- Transaction inclusion failures
- Oracle invalidation
- DEX routing failure

Vaults remain queued until conditions normalize or admin intervention occurs.

---

### 9.5 Admin Emergency Powers

Admins may immediately:

- Pause trading
- Pause deposits
- Trigger global kill switch

Admins may **never**:

- Move user funds
- Change accounting outcomes
- Mint LOOP
- Bypass settlement rules

---

## 10. Governance and Administrative Controls

### 10.1 Governance Model

- Multisignature Admin Council
- Timelocked execution for sensitive actions
- No token-based voting in v1.2

---

### 10.2 Timelock

- Minimum delay: 72 hours
- Recommended delay: 96 hours

Actions requiring timelock include:

- Fee adjustments (within bounds)
- Strategy enable/disable
- Guardrail changes
- Contract upgrades
- Pause / resume actions

---

### 10.3 Immutable Invariants

The following cannot be changed by governance or upgrade:

- Realized-profit-only accounting
- LOOP mint/burn rules
- Redemption pool solvency
- Vault isolation
- No fees on losses
- No forced liquidation

---

## 11. Security and Trust Assumptions

YieldLoop assumes:

- Smart contracts may fail
- Markets may behave adversarially
- Oracles may become unavailable
- Transactions may be censored or delayed

The protocol responds by **stopping**, not compensating.

YieldLoop makes no guarantees of profit.

---

## 12. Canonical Authority Statement

This document defines the **authoritative behavior** of the YieldLoop protocol.

If any other document, UI, or explanation conflicts with this specification, **this document prevails**.

---

END OF DOCUMENT
