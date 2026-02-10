# YieldLoop — Referral Program Specification

**Applies To:** YieldLoop v1.x  
**Status:** Canonical / Production-Ready  
**Author:** Todd Koletsky  

---

## 1. Purpose

The YieldLoop Referral Program is designed to:
- encourage high-quality, long-term user onboarding
- reward real participation without cash payouts
- avoid yield promises, revenue sharing, or MLM-style incentives

All referral incentives are **usage-based**, **time-gated**, and **non-withdrawable**.

---

## 2. Core Principles (Hard Rules)

- No upfront bonuses
- No cash payouts
- No rewards without real usage
- No impact on vault accounting, NAV, or LOOP mechanics
- No guaranteed value

The referral program must remain **boring, delayed, and conditional**.

---

## 3. Referral Definitions

- **Referrer:** An existing YieldLoop user who refers new users
- **Referred User:** A new YieldLoop user who joins via a referral
- **Valid Referral:** A referred user who meets all qualification requirements defined below

Each referral is tracked **independently**.

---

## 4. Referral Qualification Requirements

To count as a valid referral, a referred user must:

1. Create a YieldLoop vault
2. Deposit funds normally (no deposit credits or discounts)
3. Approve a trading strategy
4. Keep the vault open and active for **90 consecutive days**

Notes:
- No profit is required
- No minimum trading frequency is required
- Market conditions do not affect referral validity

If a referred user closes their vault before 90 days, that referral does **not** count and must be replaced.

---

## 5. Referrer Reward — Deposit Credit

### 5.1 Eligibility

A referrer becomes eligible for a reward once:
- **10 valid referrals** have each completed 90 days

Each set of 10 referrals is evaluated independently.

---

### 5.2 Reward Grant

When eligibility is met:
- the referrer receives a **$100 non-withdrawable deposit credit**
- the credit is issued automatically
- the credit is visible in the UI

---

### 5.3 Deposit Credit Rules

- Credit applies only to the **next deposit**
- Minimum deposit required to use credit: **$100**
- Credit is applied at deposit time and **burned on use**
- Credit cannot be withdrawn, transferred, or split
- Credit does not appear in any vault ledger
- Credit does not affect NAV, profit, or LOOP accounting
- Credit expires **90 days after issuance** if unused
- Credits are **stackable** across multiple completed referral sets

---

## 6. Referred User Incentive — Fee Discount

### 6.1 Discount Terms

Referred users receive:
- **20% off performance fees** for their first **180 days**

The discount:
- applies only to performance fees on **realized profit**
- does not apply to losses
- does not apply to deposits, withdrawals, or emergency exits
- does not roll over or extend
- expires automatically after 180 days

If no profit is realized during the discount period, no benefit is granted.

---

### 6.2 Enforcement

- The discount is applied automatically at settlement
- No user action or claim is required
- After 180 days, all fees revert to standard protocol rates

---

## 7. Abuse Prevention

The protocol reserves the right to:
- disqualify self-referrals
- disqualify wallet clusters under common control
- invalidate referrals or rewards resulting from manipulation or abuse

No KYC is required.
Pattern-based enforcement is sufficient.

---

## 8. Governance and Control

Referral program parameters, including:
- referral count requirements
- reward amounts
- discount percentages
- eligibility windows

Are:
- configurable by admin
- subject to governance timelock
- bounded by hard-coded maximums

The referral program:
- may be paused or modified
- may be disabled entirely
- does not affect core protocol operation

---

## 9. Design Intent (Non-Negotiable)

The referral program is intentionally:
- delayed
- capped
- non-cash
- usage-bound
- non-promissory

It exists to reward **participation**, not speculation.

If the referral program ever resembles yield, income, or guaranteed value,
the design has failed.

---
END OF DOCUMENT
