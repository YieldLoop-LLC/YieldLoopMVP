# YieldLoop Accounting & Value Integrity Specification
**Version:** v1.2  
**Status:** Canonical / Non-Negotiable  
**Audience:** Auditors, Core Developers, Reviewers  
**Network:** BNB Chain (v1.2)

---

## 1. Purpose

This document defines the **only valid accounting model** for the YieldLoop protocol.

Its purpose is to ensure:

- Profits are real, settled, and verifiable
- Losses are recognized immediately and permanently
- Fees are extracted only from realized profit
- Principal is never misrepresented
- LOOP cannot exist without full economic backing
- All balances are reconcilable on-chain at all times

If a value cannot be derived from this specification, **it does not exist**.

---

## 2. Core Accounting Principles

YieldLoop accounting follows these absolute principles:

- **Realized-profit-only** — unrealized P&L is ignored
- **Settlement-based** — balances update only after closeout
- **USDT-denominated** — all profit is measured in USDT
- **Loss-first recognition** — losses immediately reduce principal
- **Fee-on-profit-only** — no fees on losses or principal
- **Vault isolation** — no cross-vault accounting
- **Deterministic math** — no estimates, projections, or smoothing

Any deviation from these principles is invalid.

---

## 3. Canonical Ledger Model (Per Vault)

Each vault maintains an isolated ledger with the following required fields.

---

### 3.1 Principal Ledger

| Field | Description |
|-----|------------|
| principalDepositedUSDT | Total USDT-equivalent deposited |
| principalCurrentUSDT | Current principal after losses |
| principalLocked | True while execution or closeout is in progress |

Rules:
- `principalDepositedUSDT` never decreases
- `principalCurrentUSDT` may only decrease due to realized losses
- Principal cannot be increased by profit

---

### 3.2 Profit Ledger

| Field | Description |
|-----|------------|
| realizedProfitUSDT | Gross realized profit (pre-fee) |
| netProfitUSDT | Profit after performance fee |
| withdrawableProfitUSDT | Profit claimable in USDT |
| loopAllocatedUSDT | Profit allocated to LOOP minting |

These fields update **only during settlement**.

---

### 3.3 Fee Ledger

| Field | Description |
|-----|------------|
| performanceFeeUSDT | Fee assessed on realized profit |
| feePendingDistribution | Fee awaiting routing |

Fees may never exceed realized profit.

---

## 4. Settlement Cycle (Authoritative)

Settlement occurs after all positions are closed and the vault enters `SETTLEMENT_PENDING`.

---

### 4.1 Settlement Preconditions

Settlement may proceed only if:

- No open positions exist
- All swaps are finalized
- Vault state is `SETTLEMENT_PENDING`

Any attempt to settle outside these conditions must revert.

---

### 4.2 Settlement Algorithm

Let:

- `startingBalanceUSDT` = vault balance before execution
- `finalBalanceUSDT` = vault balance after closeout

Compute:

deltaUSDT = finalBalanceUSDT − startingBalanceUSDT

---

#### Case A — Loss or Break-Even

If:

deltaUSDT ≤ 0

Then:

- `principalCurrentUSDT += deltaUSDT`
- `realizedProfitUSDT = 0`
- `performanceFeeUSDT = 0`
- `netProfitUSDT = 0`
- No profit withdrawal
- No LOOP minting

Losses are **final** and **non-recoverable**.

---

#### Case B — Positive Profit

If:

deltaUSDT > 0

Then:

realizedProfitUSDT = deltaUSDT
performanceFeeUSDT = realizedProfitUSDT × performanceFeeRate
netProfitUSDT = realizedProfitUSDT − performanceFeeUSDT

Only `netProfitUSDT` may be distributed.

---

## 5. Performance Fee Model

### 5.1 Fee Rates (Canonical)

Performance fee depends on **user payout selection**:

| Payout Mode | Fee Rate |
|------------|----------|
| USDT | 15% |
| LOOP | 10% |

These rates are protocol-defined for v1.2.

Fees apply:
- Per settlement
- Only on positive realized profit

---

### 5.2 Fee Routing (Of Fee Amount)

Assessed fees are routed as follows:

| Destination | Allocation |
|-----------|------------|
| Dev / Ops | 25% |
| Marketing | 10% |
| Ratchet Reserve | 55% |
| LoopLabs | 10% |

Routing occurs after settlement and before profit distribution.

---

## 6. Profit Distribution Paths

Users select payout preference **before execution**.

Valid paths:

- **USDT Payout**
- **LOOP Receipt Token**
- **Mixed Allocation** (percentage-based, optional)

---

### 6.1 USDT Payout Path

If user selects USDT:

withdrawableProfitUSDT += netProfitUSDT

User may withdraw when vault is idle.

---

### 6.2 LOOP Receipt Token Path (Canonical)

If user selects LOOP:

1. `netProfitUSDT` is transferred to the **LOOP Redemption Pool**
2. `loopAllocatedUSDT += netProfitUSDT`
3. LOOP is minted:

loopMintAmount = netProfitUSDT

Minting must be atomic and revert on failure.

---

## 7. LOOP Receipt Token Accounting

### 7.1 Definition

LOOP is a **mint-and-burn receipt token** representing realized net profit.

It is not:
- a yield promise
- a stablecoin
- an emission
- a governance token

---

### 7.2 Minting Conditions (Hard Rule)

LOOP may be minted **only if all are true**:

- Settlement completed
- `realizedProfitUSDT > 0`
- Fees deducted
- Equivalent USDT deposited into Redemption Pool
- User selected LOOP payout

Any other mint attempt must revert.

---

### 7.3 Redemption and Burn

When LOOP is redeemed:

1. LOOP is burned
2. Equivalent USDT is transferred from Redemption Pool
3. Redemption is always 1:1

Partial redemptions are permitted.

---

### 7.4 Solvency Invariant (Absolute)

At all times:

Total LOOP Supply ≤ RedemptionPoolUSDTBalance

This invariant cannot be bypassed by:
- admin action
- governance vote
- upgrade
- emergency mode

---

## 8. LOOP Redemption Pool Rules

- Holds USDT only
- Receives USDT only from realized profit
- Cannot be borrowed from
- Cannot be used for execution
- Cannot be drained by admin
- Cannot back principal
- Exists solely to honor LOOP claims

---

## 9. Emergency Withdrawal Accounting

### 9.1 Emergency Withdrawal Characteristics

- Always USDT-only
- LOOP payout preference ignored
- Positions may close at loss
- Performance fee applies **only if profit exists**

---

### 9.2 Emergency Settlement Rules

During emergency closeout:

- Settlement math remains identical
- Losses reduce principal
- Profit (if any) follows standard fee rules
- No special accounting shortcuts permitted

Emergency mode **does not alter accounting truth**.

---

## 10. Loss Handling and Drawdowns

- Losses are applied immediately
- No smoothing
- No deferred recognition
- No recovery trading
- No profit offsetting

YieldLoop explicitly accepts drawdowns as real outcomes.

---

## 11. Prohibited Accounting Behaviors

The following are forbidden:

- Mark-to-market accounting
- Unrealized profit credit
- APY projection
- Fee extraction on losses
- Minting LOOP from anything other than realized profit
- Backing LOOP with non-USDT assets
- Cross-vault profit or loss sharing

Any implementation exhibiting these behaviors is invalid.

---

## 12. Required Events (Auditability)

The protocol must emit events for:

- SettlementCompleted
- ProfitRealized
- LossRealized
- PerformanceFeeAssessed
- FeeRouted
- LOOPMinted
- LOOPBurned
- LOOPRedeemed

Events must include vault ID, amounts, and timestamps.

---

## 13. Canonical Authority Statement

This document defines the **only valid accounting interpretation** of YieldLoop.

If any UI, strategy, governance action, or external explanation conflicts with this specification, **this document prevails**.

---

END OF DOCUMENT
