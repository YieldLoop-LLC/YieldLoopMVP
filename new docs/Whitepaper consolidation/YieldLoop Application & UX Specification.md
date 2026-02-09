# YieldLoop Application & UX Specification
**Version:** v1.2  
**Status:** Canonical / Build Contract  
**Audience:** Frontend Developers, Backend Developers, Indexers  
**Network:** BNB Chain (v1.2)

---

## 1. Purpose

This document defines the **application-layer requirements** for YieldLoop.

Its goals are to ensure:

- The UI never contradicts protocol behavior
- Users are never shown false or projected data
- Application flows mirror the vault state machine
- Emergency behavior is visible and understandable
- No UI path enables illegal protocol actions

If a screen, action, or display is not defined here, **it must not exist**.

---

## 2. UX Design Principles (Hard Rules)

The YieldLoop application must obey the following rules:

- No APY, APR, or projected yield
- No mark-to-market P&L
- No “estimated” or “expected” profit display
- No hidden automation
- No execution without explicit user approval
- No UI actions that bypass state machine rules
- No implied guarantees of profit
- No mixing of principal and profit balances

The UI reflects reality.  
It does not speculate, predict, or reassure.

---

## 3. Application Architecture Overview

The YieldLoop application consists of:

- Frontend DApp (user-facing)
- Backend services (non-custodial helpers)
- Indexer / event listener
- Admin console (restricted)

All accounting, execution, and enforcement occurs **on-chain**.  
Off-chain services may **observe**, **queue**, or **relay**, but never decide.

---

## 4. Core Screens (User-Facing)

### 4.1 Wallet Connection

- User must connect a self-custody wallet
- No deposits without wallet connection
- Network must be BNB Chain
- Wrong network must block actions

---

### 4.2 Dashboard (Overview)

Displays **current state only**:

- Vault list
- Vault state (enum value)
- Principal deposited
- Principal current
- Withdrawable USDT profit
- LOOP balance (if any)
- Vault status flags (Paused / Emergency)

**Must not display:**
- Unrealized P&L
- Active trade profit
- APY or projections

---

### 4.3 Vault Creation

User flow:

1. Select base asset
2. Deposit amount (≥ $250 USDT equivalent)
3. Confirm deposit
4. Vault enters `DEPOSIT_PENDING`

UI must:
- Block deposits below minimum
- Warn about loss risk
- Confirm vault isolation

---

### 4.4 Vault Detail View

Displays:

- Vault ID
- Current state
- Base asset
- Principal deposited
- Principal current
- Profit balances
- LOOP balance
- Last settlement timestamp
- Active strategy hash (if any)

---

## 5. Strategy Proposal & Approval UX

### 5.1 Strategy Proposal Display

When a strategy is proposed, UI must show:

- Strategy type
- Allowed venues
- Max trade size
- Slippage limit
- Price impact limit
- Liquidity requirement
- Expiration time

UI must explicitly state:
> “This strategy will not execute without your approval.”

---

### 5.2 Strategy Approval

- User must explicitly approve or reject
- Approval is hash-bound
- Expired strategies must be rejected automatically
- Approval moves vault to `STRATEGY_APPROVED`

UI must not auto-approve under any condition.

---

## 6. Execution Visibility

During execution:

- Vault state updates must be shown
- UI may show “Executing” or “Holding Position”
- UI must not show live profit or loss
- UI must not show trade-level details unless settled

Execution is intentionally opaque in real time.

---

## 7. Settlement & Profit Display

After settlement:

UI may display:

- Realized profit (USDT)
- Performance fee assessed
- Net profit
- Updated principal
- Updated LOOP balance (if applicable)

UI must clearly label:
- “Realized”
- “Settled”
- “Withdrawable”

---

## 8. Profit Withdrawal UX

### 8.1 USDT Withdrawal

- Available only when vault is idle
- Withdraws from `withdrawableProfitUSDT`
- Cannot exceed available balance

UI must:
- Show withdrawal amount
- Show remaining balance
- Confirm transaction explicitly

---

### 8.2 LOOP Redemption

- LOOP balance displayed separately
- Redemption burns LOOP
- Pays USDT 1:1 from Redemption Pool

UI must display:
- Current LOOP supply
- Redemption pool balance
- Coverage ratio (Pool / Supply)

If coverage approaches 1.0, UI must warn that minting may pause.

---

## 9. Emergency Withdrawal UX

### 9.1 Emergency Action Entry

UI must clearly label emergency withdrawal as:

> “USDT-only. Positions may be closed at a loss.”

---

### 9.2 Emergency States Display

UI must display when vault is in:

- CLOSE_PENDING
- CLOSE_PENDING_PARTIAL
- EMERGENCY_QUEUE_* states

UI must explain:
- Why vault is queued
- What condition is blocking progress
- That losses are possible

---

### 9.3 Global Emergency Mode

If global emergency flag is active:

- Disable deposits
- Disable strategy approval
- Show FIFO queue position (if applicable)
- Display system-wide notice

---

## 10. Admin Console (Restricted)

Admin UI must:

- Require multisig authentication
- Display queued timelock actions
- Display emergency controls
- Emit visible events for all actions

Admin UI **must not**:
- Access individual vault funds
- Override accounting
- Mint LOOP
- Bypass timelock for non-emergency actions

---

## 11. Backend & Indexer Responsibilities

Off-chain services may:

- Index vault states
- Index events
- Queue execution attempts
- Monitor liveness conditions
- Retry failed transactions

Off-chain services must not:

- Decide execution outcomes
- Modify balances
- Override state machine
- Perform accounting

---

## 12. Error Handling & Messaging

UI must provide explicit error messages for:

- Invalid state actions
- Strategy expiration
- Insufficient liquidity
- Oracle failure
- Gas failure
- Emergency pauses

Generic “Something went wrong” messages are not acceptable.

---

## 13. Prohibited UX Patterns

The following are forbidden:

- Yield projections
- “Earn X%” language
- Countdown timers implying profit
- Auto-compounding without approval
- Hidden retries
- Silent failure masking

Any UI exhibiting these patterns is invalid.

---

## 14. Canonical Authority Statement

This document defines the **only permitted application-layer behavior** for YieldLoop.

If any UI behavior conflicts with:
- Document 1 (Protocol Specification), or
- Document 2 (Accounting & Value Integrity),

then this document must defer.

---

END OF DOCUMENT
