# YieldLoop — Fees, LOOP, Redemption & Incentives  
**How Money Moves Through the System**

**Author:** Todd Koletsky  
**Date:** February 9, 2026  
**Version:** v1.0 — Economic & Incentive Specification

---

## Document Purpose & Authority

This document defines **all economic flows** within YieldLoop.

It explains:
- When and how fees are charged
- How discounts are applied
- How incentives are funded and distributed
- How LOOP functions (and does not function)
- How redemptions and emergency exits work

If a question involves **money**, it is answered here.

This document is authoritative and non-optional.

---

## 1. Core Economic Philosophy

YieldLoop follows three non-negotiable economic principles:

1. **No profit → no fee**
2. **No pooling of user losses**
3. **No reflexive token dependency**

Fees exist to:
- Sustain the system
- Fund operations
- Align incentives

They do not exist to extract rent from failure.

---

## 2. Performance Fees

### 2.1 Fee Trigger

Performance fees are charged **only** on **realized gains** at settlement.

- If a cycle ends at a loss → **no fee**
- If a cycle breaks even → **no fee**
- Fees are never charged mid-cycle

---

### 2.2 Fee Assessment Timing

Fees are:
- Calculated at settlement
- Applied atomically
- Transparent to the user

There are no hidden deductions.

---

### 2.3 Fee Configuration

- Base performance fee is configurable by governance
- Fee discounts may apply (see Section 3)
- Fees are always bounded

Fee changes cannot be retroactive.

---

## 3. Fee Discounts

### 3.1 Discount Sources

Discounts may be granted through:
- Genesis NFTs
- Whitelisted insider wallets

Discounts:
- Reduce the performance fee only
- Do not affect execution behavior
- Do not increase risk exposure

---

### 3.2 Discount Tiers

**Basic Genesis NFT**
- Duration: 12 months
- Discount: **10%**
- Non-renewable
- Non-upgradable

**Lifetime Genesis NFT**
- Duration: Lifetime
- Discount: **50%**
- Hard cap on discount level

Discounts cannot stack.

---

## 4. Incentive Programs (Non-Yield)

Incentives are **not yield**.

They are:
- Budgeted distributions
- Marketing and alignment expenses
- Independent of vault performance

No incentive implies future returns.

---

## 5. Marketing-Funded Incentives (Public)

### 5.1 Incentive Pool

A portion of the **marketing budget** is allocated to Genesis incentives.

This allocation is:
- Explicit
- Bounded
- Non-compounding

---

### 5.2 Incentive Rates

**Basic Genesis NFT**
- Incentive: **0.25%** of the marketing budget
- Duration: 12 months

**Lifetime Genesis NFT**
- Incentive: **0.75%** of the marketing budget
- Duration: Lifetime

These incentives:
- Are distributed proportionally
- Do not depend on vault performance
- Do not affect other users

---

### 5.3 Distribution Method

- Incentives are distributed on a scheduled basis
- Distribution is proportional to eligible balances
- Distribution records are auditable

---

## 6. Insider Alignment Incentives (Private)

### 6.1 Funding Source

Insider incentives are funded **exclusively** from the **operations/dev budget**.

They are **not** funded from marketing.

---

### 6.2 Mirrored Incentive Amount

The insider incentive pool equals:
- The **exact dollar amount** corresponding to the **0.25% marketing kicker**
- Calculated each period
- Paid from ops/dev funds

This is a fixed-dollar mirror, **not a percentage**.

---

### 6.3 Eligible Wallets

- Up to **20 insider wallets**
- Must maintain ≥ **$1,000**
- Must remain active
- Non-transferable
- Non-stackable

Add-on family/friend wallets:
- Must maintain ≥ $1,000
- Must remain active
- Are globally capped
- Do not receive incentives unless explicitly approved

---

## 7. LOOP Token (Purpose-Built Receipt)

### 7.1 What LOOP Is

LOOP is a **non-tradable, protocol-internal receipt token**.

It exists to:
- Represent deferred claims
- Support internal accounting
- Enable controlled redemption paths

LOOP is **not**:
- A speculative asset
- A governance token
- A yield-bearing token
- Tradable on DEXs

---

### 7.2 LOOP Issuance

LOOP may be minted:
- As a receipt for specific protocol actions
- When explicitly backed
- Under predefined rules

There is no discretionary minting.

---

### 7.3 LOOP Redemption

LOOP may be redeemed:
- Through protocol-defined paths
- On schedule
- Subject to availability

Redemption is never guaranteed to be instant.

---

## 8. Standard Redemptions (USDT)

Users may always opt for:
- Standard USDT settlement at cycle end

This path:
- Is primary
- Does not require LOOP
- Is the default for most users

---

## 9. Emergency Withdrawals

### 9.1 Emergency Conditions

Emergency withdrawals may be enabled during:
- Execution failure
- Liquidity events
- System-level emergencies

---

### 9.2 Emergency Mechanics

- Positions are unwound if possible
- Assets are returned as-is
- Losses may occur
- Fees may still apply if gains were realized

Emergency withdrawals prioritize **access**, not optimization.

---

## 10. Transparency & Auditability

All economic actions are:
- Logged
- Verifiable
- Non-reversible

There are no hidden subsidy paths.

---

## 11. Economic Non-Goals

YieldLoop does not:
- Advertise APY
- Subsidize losses
- Guarantee returns
- Inflate incentives to attract deposits
- Depend on token price appreciation

---

## 12. Summary

YieldLoop’s economics are intentionally restrained.

- Fees are earned, not assumed
- Incentives are budgeted, not promised
- Tokens are functional, not speculative
- Withdrawals are respected

This document defines **how money behaves** inside the system.

Proceed next to:

**Document 6 — Genesis NFTs & Early Access Programs**
