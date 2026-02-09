# YieldLoop Accounting & Value Integrity Specification
**Version:** v1.2  
**Status:** Canonical / Non-Negotiable  
**Audience:** Auditors, Core Developers, Reviewers  
**Chain:** BNB Chain (v1.2)

---

## 1. Purpose

This document defines the **only valid accounting model** for YieldLoop.

Its goals are to ensure:

- Profit is never overstated
- Losses are never hidden
- Fees are never extracted from unrealized value
- LOOP cannot be created without real economic backing
- All balances are reconcilable at all times

If a number cannot be derived from this specification, it **does not exist**.

---

## 2. Core Accounting Principles

YieldLoop accounting is governed by the following principles:

- **Realized-profit-only**: unrealized P&L is ignored
- **Settlement-based**: balances update only after closeout
- **USDT-denominated**: all profit is measured in USDT
- **Loss-first recognition**: losses immediately reduce principal
- **Fee-on-profit-only**: fees apply only to net positive outcomes
- **Ledger isolation**: every vault maintains its own balances

No accounting shortcuts, projections, or smoothing are permitted.

---

## 3. Canonical Ledger Fields (Per Vault)

Each vault maintains the following ledger fields.

### 3.1 Principal Ledger

| Field | Description |
|-----|------------|
| principalDepositedUSDT | Total USDT-equivalent deposited |
| principalCurrentUSDT | Current principal after losses |
| principalLocked | Boolean flag during execution |

Losses always reduce `principalCurrentUSDT`.

---

### 3.2 Profit Ledger

| Field | Description |
|-----|------------|
| realizedProfitUSDT | Net realized profit (pre-fee) |
| withdrawableProfitUSDT | Profit available for USDT withdrawal |
| loopAllocatedUSDT | Profit allocated to LOOP minting |

These fields are updated **only during settlement**.

---

### 3.3 Fee Ledger

| Field | Description |
|-----|------------|
| performanceFeeUSDT | Fee amount extracted from profit |
| feePendingDistribution | Fee awaiting routing |

Fees may never exceed realized profit.

---

## 4. Settlement Cycle (Authoritative)

Settlement occurs after a vault completes an execution cycle and all positions are closed.

### 4.1 Settlement Preconditions

Settlement may occur only if:

- No open positions exist
- All trades are finalized
- Vault state is `SETTLEMENT_PENDING`

---

### 4.2 Settlement Algorithm

Let:

- `finalBalanceUSDT` = vault balance after closeout  
- `startingBalanceUSDT` = balance before execution  

Compute:

deltaUSDT = finalBalanceUSDT - startingBalanceUSDT

#### Case A — Loss or Break-Even

If:

deltaUSDT ≤ 0

Then:
- `principalCurrentUSDT += deltaUSDT`
- `realizedProfitUSDT += 0`
- `performanceFeeUSDT = 0`
- No LOOP may be minted
- No profit withdrawal available

Losses are final and unrecoverable.

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

## 5. Performance Fee Rules

### 5.1 Applicability

- Performance fees apply **only** when `realizedProfitUSDT > 0`
- Fees apply per settlement cycle
- Fees never apply to principal
- Fees never apply to losses

### 5.2 Bounds

Performance fee rate is governance-configurable **within immutable bounds**:

5% ≤ performanceFeeRate ≤ 20%

Attempts to exceed bounds must revert.

---

## 6. Profit Distribution Paths

Users select their profit distribution preference **before execution**.

Valid options:

- **USDT Withdrawal**
- **LOOP Receipt Token**
- **Mixed Distribution** (percentage-based, optional)

---

### 6.1 USDT Profit Path

If user selects USDT:

withdrawableProfitUSDT += netProfitUSDT

User may withdraw this amount at any time when vault is idle.

---

### 6.2 LOOP Receipt Token Path (Canonical)

If user selects LOOP:

1. `netProfitUSDT` is transferred into the **LOOP Redemption Pool**
2. `loopAllocatedUSDT += netProfitUSDT`
3. LOOP is minted:

loopMintAmount = netProfitUSDT

Minting is atomic and must revert on failure.

---

## 7. LOOP Receipt Token Accounting

### 7.1 LOOP Definition

LOOP is a **receipt token** representing a claim on realized profit.

It is not:
- an emission
- a yield promise
- a governance token
- a stablecoin

---

### 7.2 Minting Invariant

LOOP may be minted **only** if all conditions are met:

- `realizedProfitUSDT > 0`
- Fees have been deducted
- Equivalent USDT is deposited into Redemption Pool
- User has selected LOOP payout

Any other mint attempt must revert.

---

### 7.3 Redemption and Burn

When a user redeems LOOP:

1. LOOP is burned
2. Equivalent USDT is transferred from Redemption Pool
3. Redemption must be 1:1

Partial redemptions are permitted.

---

### 7.4 Solvency Invariant (Hard Rule)

At all times:

Total LOOP Supply ≤ RedemptionPoolUSDTBalance

This invariant is **absolute**.

No admin, governance action, or upgrade may violate it.

---

## 8. Redemption Pool Rules

- Holds only USDT
- Receives USDT only from realized profit
- Cannot be borrowed from
- Cannot be used for execution
- Cannot be drained by admin
- Cannot be used to back principal

The Redemption Pool exists **solely** to honor LOOP claims.

---

## 9. Loss Handling and Drawdowns

- Losses reduce principal immediately
- No loss smoothing
- No deferred loss recognition
- No recovery trading is allowed to “win back” losses

YieldLoop explicitly accepts drawdowns as real outcomes.

---

## 10. Prohibited Accounting Behaviors

The following are strictly forbidden:

- Mark-to-market profit
- APY projections
- Unrealized profit credit
- Fee extraction on losses
- Minting LOOP from anything other than realized profit
- Backing LOOP with anything other than USDT
- Cross-vault accounting

Any implementation exhibiting these behaviors is invalid.

---

## 11. Required Events (Auditability)

The protocol must emit events for:

- SettlementCompleted
- ProfitRealized
- LossRealized
- FeeAssessed
- LOOPMinted
- LOOPBurned
- LOOPRedeemed

Events must include vault ID, amounts, and timestamps.

---

## 12. Canonical Authority Statement

This document defines the **only valid accounting interpretation** of YieldLoop.

If any UI, strategy, or external explanation conflicts with this specification, **this document prevails**.

---

END OF DOCUMENT
