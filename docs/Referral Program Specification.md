# YieldLoop — Referral Program Specification

**Applies To:** YieldLoop v1.x  
**Status:** Canonical / Production-Ready  
**Author:** Todd Koletsky  

---

## Referral Program Overview

If you’re expecting a referral program that pays you cash for tweeting a link, you’re on the wrong website.

The YieldLoop referral program is **delayed, capped, non-cash, and usage-bound**.  
It rewards **participation**, not spam. Try to contain your enthusiasm.

---

## Core Principles (Hard Rules)

These are rules, not suggestions:

- No upfront bonuses  
- No cash payouts  
- No rewards without real usage  
- No impact on vault accounting, NAV, or LOOP mechanics  
- No guaranteed value  

The referral program must remain **boring, delayed, and conditional**.  
If it ever resembles yield, income, or guaranteed value, the design has failed.

---

## Definitions

- **Referrer** — An existing YieldLoop user who refers new users  
- **Referred User** — A new user who joins via a referral  
- **Valid Referral** — A referred user who meets all qualification requirements  

Each referral is tracked **independently**.

---

## Qualification Requirements

To count as a valid referral, a referred user must complete **all four** of the following:

1. Create a YieldLoop vault  
2. Deposit funds normally (no deposit credits or discounts)  
3. Approve a trading strategy  
4. Keep the vault open and active for **90 consecutive days**

### Things That Do *Not* Matter

- No profit is required  
- No minimum trading frequency is required  
- Market conditions do not affect referral validity  

If a referred user closes their vault before 90 days:
- That referral **does not count**
- It must be replaced
- No partial credit
- No exceptions
- No appeals

---

## Referrer Reward — Deposit Credit

### Eligibility

A referrer becomes eligible for a reward once **10 valid referrals** have each completed their 90-day qualification period.

Each set of 10 referrals is evaluated independently.

Yes — that’s ten people who each stuck around for three months.  
This is intentionally difficult.

---

### What You Get

When eligibility is met, the referrer receives:

- **$100 non-withdrawable deposit credit**
- Issued automatically
- Visible in the UI

This credit functions **identically** to the marketing deposit credits distributed to:
- Genesis Lite Pass holders  
- Lifetime Genesis Pass holders  

Same rules. Same restrictions. Same expiration.

---

### Deposit Credit Rules

- Credit applies only to the **next deposit**
- Minimum deposit required to use credit: **$100**
- Credit is applied at deposit time and **burned on use**
- Credit cannot be withdrawn, transferred, or split
- Credit does not appear in any vault ledger
- Credit does not affect NAV, profit, or LOOP accounting
- Credit expires **90 days after issuance** if unused
- Credits are **stackable** across multiple completed referral sets

#### Expiration Handling

- Any **unused or expired referral deposit credit** is permanently reclaimed by the protocol
- Reclaimed credits are routed **one-way** into the **Ratchet Pool**
- This action:
  - Creates **no user claim**
  - Provides **no guarantee**
  - May have **no observable effect**
- The ratchet may pause indefinitely regardless of expired credit inflows

To be clear:
- You cannot cash this out  
- You cannot send it to someone  
- You can only use it to deposit more into your own vault  

That’s it.

---

## Referred User Incentive — Fee Discount

### Discount Terms

Referred users receive:

- **20% off performance fees for the first 180 days**

This is the **same discount rate as the Genesis Lite Pass**.

Discount interaction rules:
- Referral discounts **do not stack** with Genesis Pass discounts
- The **higher applicable discount** is always used
- If you hold a Lifetime Genesis Pass (50% discount), the referral discount is irrelevant

---

### Discount Mechanics

The discount:
- Applies only to performance fees on **realized profit**
- Does not apply to losses
- Does not apply to deposits, withdrawals, or emergency exits
- Does not roll over or extend
- Expires automatically after **180 days**

If no profit is realized during the discount period, no benefit is granted.

- Discount is applied automatically at settlement
- No user action or claim required
- After 180 days, all fees revert to standard protocol rates unless a Genesis Pass discount applies

In plain terms:  
You get a discount on fees you only pay if you made money.  
If you don’t make money, the discount is worth **exactly nothing**.

We told you this was boring.

---

## Abuse Prevention

The protocol reserves the right to:

- Disqualify self-referrals  
- Disqualify wallet clusters under common control  
- Invalidate referrals or rewards resulting from manipulation or abuse  

No KYC is required.  
Pattern-based enforcement is sufficient.

If you’re thinking about gaming this with 10 burner wallets, each holding a vault open for 90 days with real deposits — honestly, at that point you’ve almost earned it.  
But we’ll probably still catch you.

---

## Governance and Control

Referral program parameters — including:

- Referral count requirements  
- Reward amounts  
- Discount percentages  
- Eligibility windows  

Are:
- Configurable by admin
- Subject to governance timelock
- Bounded by hard-coded maximums

The referral program:
- May be paused, modified, or disabled entirely
- Does not affect core protocol operation

As with Genesis Pass parameters:
- Referral terms may be **improved**
- They will **never be made worse** for existing participants

---

## Design Intent (Non-Negotiable)

The referral program is intentionally:

- **Delayed** — 90 days before a referral even counts  
- **Capped** — $100 per 10 valid referrals  
- **Non-cash** — deposit credit only  
- **Usage-bound** — real vaults, real deposits, real time  
- **Non-promissory** — no guaranteed value of any kind  

It exists to reward **participation**, not speculation.

---

## Required Disclosure

The referral program does not constitute income, yield, or a promise of value.

- Deposit credits are non-withdrawable and may expire  
- Expired credits are reclaimed by the protocol and routed to the Ratchet Pool  
- Fee discounts apply only to realized profit and may provide no benefit if no profit is realized  
- Referral discounts do not stack with Genesis Pass discounts — the higher applicable rate is used  
- The program may be modified, paused, or discontinued at any time via governance timelock  

---
END OF DOCUMENT
