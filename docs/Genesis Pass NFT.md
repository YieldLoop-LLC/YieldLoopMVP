# YieldLoop Genesis Pass Program  
**Access • Discounts • Deposit Credits (Non-Withdrawable)**

**Version:** 1.0  
**Status:** Fundraising-Ready / Grant-Safe Specification  
**Applies To:** YieldLoop v1.x  
**Author:** Todd Koletsky  

---

## Overview

The **YieldLoop Genesis Pass** is a **time-bound access pass** designed to support early protocol development and user onboarding without introducing emissions, revenue sharing, or yield promises.

The Genesis Pass provides:
- **Capped performance-fee discounts**
- **Non-withdrawable deposit credits** (usage subsidy)
- **Early access and community participation**

The Genesis Pass is **not**:
- an investment contract  
- a yield product  
- a revenue share  
- a dividend or payout mechanism  

All benefits are optional, usage-based, and may be zero.

---

## Core Design Principles

- **Boring is good**
- **No promises**
- **No emissions**
- **No withdrawable rewards**
- **No pooled ownership**
- **No protocol liabilities created**
- **Everything expires**

---

# Table of Contents

1. **Purpose and Scope**  
   1.1 What the Genesis Pass Is  
   1.2 What the Genesis Pass Is Not  

2. **Genesis Pass Structure**  
   2.1 Time-Bound Access Terms (1–5 Years)  
   2.2 Pass Lifecycle and Expiration  

3. **Pricing Model**  
   3.1 Dynamic Loyalty Pricing Schedule  
   3.2 Renewal and Top-Up Rules  

4. **Fee Discount Benefits**  
   4.1 Eligible Fees  
   4.2 Discount Rates and Caps  
   4.3 Discount Stacking Rules  
   4.4 Discount Enforcement Timing  

5. **Deposit Credit Program (Usage Subsidy)**  
   5.1 Purpose of Deposit Credits  
   5.2 Funding Source (Marketing Budget Allocation)  
   5.3 Pro-Rata Allocation Method  
   5.4 Monthly Credit Cap and Discretion  
   5.5 Credit Application Rules  
   5.6 Burn-on-Use Requirement  
   5.7 **90-Day Credit Expiry Policy**  
   5.8 Non-Withdrawable / Non-Transferable Enforcement  

6. **User Eligibility and Conditions**  
   6.1 Active Pass Requirement  
   6.2 Vault Creation Use-Case Only  
   6.3 No Impact on Vault Guardrails  

7. **Non-Promise and Risk Disclosures**  
   7.1 No Guaranteed Credits  
   7.2 Zero-Credit Months  
   7.3 Program Modification Rights  

8. **Protocol Safeguards**  
   8.1 Credit Liability Limits  
   8.2 Abuse Prevention  
   8.3 Emergency Pause Rights  

9. **Governance and Administration**  
   9.1 Admin Controls  
   9.2 Timelock and Transparency  

10. **Future Expansion (Non-Binding)**  
    10.1 Upgrade Path to Genesis NFT v2  
    10.2 Vault-Bound Loyalty Systems  

11. **Public-Facing Summary (UI / Docs)**  

---

---

## 1. Purpose and Scope

### 1.1 What the Genesis Pass Is
The **YieldLoop Genesis Pass** is a **time-bound access pass** that provides optional protocol benefits to early supporters without creating emissions, yield promises, or revenue sharing.

The Genesis Pass exists to:
- Encourage early participation
- Reduce friction for vault creation via **non-withdrawable deposit credits**
- Provide **capped performance-fee discounts**
- Create a simple, auditable, grant-safe fundraising mechanism

All benefits are **usage-based**, **optional**, and **may be zero**.

### 1.2 What the Genesis Pass Is Not
The Genesis Pass is **not**:
- An investment contract
- A yield product
- A dividend or revenue share
- A governance token
- A transferable reward instrument

No benefit is guaranteed. No benefit compounds. No benefit is withdrawable.

---

## 2. Genesis Pass Structure

### 2.1 Time-Bound Access Terms
Genesis Passes are issued with fixed-duration terms:
- **1 Year**
- **2 Years**
- **3 Years**
- **4 Years**
- **5 Years**

Benefits apply **only while the pass is active**.  
Once expired, all benefits cease automatically.

### 2.2 Pass Lifecycle and Expiration
- Pass benefits activate immediately upon issuance.
- Benefits terminate at the end of the pass term.
- Expired passes:
  - receive no discounts
  - receive no deposit credits
  - retain no residual value

There is no grace period after expiration.

---

## 3. Pricing Model

### 3.1 Dynamic Loyalty Pricing Schedule
Genesis Pass pricing follows a **declining loyalty schedule** to reward longer commitments:

| Term | Price |
|-----:|------:|
| 1 Year | $100 |
| 2 Years | $95 |
| 3 Years | $90 |
| 4 Years | $85 |
| 5 Years | $80 |

Pricing is fixed, published in advance, and not subject to market dynamics.

### 3.2 Renewal and Top-Up Rules
- Pass holders may **extend or top-up** an active pass **before expiration**.
- Extensions inherit the **current loyalty pricing** for the selected term.
- Renewals do not stack terms retroactively.
- Top-ups do not reset previously elapsed time.

---

## 4. Fee Discount Benefits

### 4.1 Eligible Fees
Genesis Pass discounts apply **only** to:
- **YieldLoop performance fees on realized profits**

Discounts do **not** apply to:
- principal
- deposits
- withdrawals
- losses
- emergency exits
- any non-performance fees introduced later

### 4.2 Discount Rates and Caps
- Genesis Pass provides up to **20% off performance fees**
- Discounts are **stackable with other protocol discounts**
- **Hard Cap:** total discounts may not exceed **50% of the base performance fee**

### 4.3 Discount Enforcement Timing
- Discounts are applied at **settlement time** only
- Discounts do not alter:
  - execution priority
  - guardrails
  - safety checks
  - trade frequency

### 4.4 No Preferential Treatment
Genesis Pass holders receive **no execution advantage** and no bypass of protocol safety mechanisms.

---

## 5. Deposit Credit Program (Usage Subsidy)

### 5.1 Purpose of Deposit Credits
Deposit credits exist solely to **reduce the USDT required** to create a new YieldLoop vault.

Credits are:
- **non-withdrawable**
- **non-transferable**
- **non-compounding**
- **burned on use**

### 5.2 Funding Source
Deposit credits may be issued from a **protocol-controlled marketing budget allocation**.

This allocation:
- is discretionary
- may be zero in any month
- creates no obligation or entitlement

### 5.3 Allocation Method
When issued, deposit credits are allocated:
- **pro-rata across active Genesis Passes**
- subject to a **monthly issuance cap**

Allocation does not depend on:
- vault size
- trading activity
- historical usage

### 5.4 Credit Application Rules
- Credits may only be applied **at vault creation**
- Credits reduce the required USDT deposit
- Example:
  - Minimum deposit: $250
  - Available credit: $30
  - User deposits $220 USDT
  - Vault principal = $250

### 5.5 Burn-on-Use Requirement
- Credits are **irreversibly burned** when applied
- Unused credits cannot be reused once burned

### 5.6 **90-Day Expiry Policy**
- Issued credits **expire 90 days** after issuance
- Expired credits are automatically invalidated
- Expiry prevents hoarding and aligns incentives with active usage

### 5.7 Non-Promise Statement
Deposit credits:
- may be zero
- may be paused
- may be discontinued

No expectation of issuance should be assumed.

---

---

## 6. User Eligibility and Conditions

### 6.1 Active Pass Requirement
Genesis Pass benefits apply **only while the pass is active**.

To be eligible for:
- fee discounts
- deposit credits
- access benefits

the user must hold a **non-expired Genesis Pass** at the time of use.

Expired passes confer no residual rights.

### 6.2 Vault Creation Use-Case Only
Deposit credits may be used **only** to reduce the USDT required to create a **new YieldLoop vault**.

Credits may **not** be used for:
- adding capital to an existing vault
- covering losses
- offsetting withdrawals
- paying protocol fees directly
- LOOP redemption
- any non-vault action

### 6.3 No Impact on Vault Guardrails
Genesis Pass benefits must **not**:
- modify vault execution logic
- bypass risk limits
- change strategy selection
- alter safety thresholds
- affect emergency behavior

All vaults operate under identical guardrails regardless of pass ownership.

---

## 7. Non-Promise and Risk Disclosures

### 7.1 No Guaranteed Benefits
All Genesis Pass benefits:
- are optional
- are usage-based
- may be zero
- may be paused or discontinued

Holding a Genesis Pass does **not** guarantee:
- deposit credits
- fee reductions in every cycle
- profitability
- protocol uptime
- continued program availability

### 7.2 Zero-Credit Months
The protocol may issue **no deposit credits** in any given month due to:
- budget constraints
- market conditions
- strategic decisions
- system pauses

Zero-credit months do not constitute a breach of terms.

### 7.3 No Financial Representation
The Genesis Pass must never be marketed or represented as:
- an investment
- a profit-sharing mechanism
- a yield-bearing product
- a claim on protocol revenue

Benefits exist solely to reduce usage friction.

---

## 8. Protocol Safeguards

### 8.1 Credit Liability Limits
To prevent runaway liabilities:
- monthly credit issuance is **explicitly capped**
- unused credits expire after 90 days
- credits are burned on use
- credits cannot accumulate indefinitely

The protocol is never required to issue credits.

### 8.2 Abuse Prevention
The system must prevent:
- credit reuse
- credit transfer
- flash-use behavior
- fee manipulation
- vault gaming

Credits and discounts are enforced at **deterministic checkpoints** only.

### 8.3 Emergency Pause Rights
The protocol may pause:
- Genesis Pass issuance
- deposit credit issuance
- fee discount application

Emergency pauses:
- do not move user funds
- do not invalidate existing vaults
- do not retroactively remove applied discounts

---

## 9. Governance and Administration

### 9.1 Admin Controls
Administrative actions affecting the Genesis Pass include:
- pausing issuance
- adjusting monthly credit caps (within published bounds)
- disabling credit issuance
- updating documentation language

All actions must be:
- transparent
- auditable
- executed via multisig

### 9.2 Timelock and Transparency
Non-emergency changes must:
- be subject to a **timelock**
- be publicly announced
- include clear rationale

Emergency actions may bypass timelock **only** to preserve protocol safety.

---

---

## 10. Future Expansion (Non-Binding)

### 10.1 Upgrade Path to Genesis NFT v2
The Genesis Pass program is intentionally simple and time-bound.  
Future versions of YieldLoop **may** introduce more advanced loyalty mechanisms, including NFT-based systems.

Any such upgrade:
- will be **opt-in**
- will not retroactively alter Genesis Pass terms
- will be documented in a separate specification
- will not convert Genesis Passes into financial instruments

The Genesis Pass is **not** a placeholder token and carries no implied upgrade rights.

### 10.2 Vault-Bound Loyalty Systems
Future loyalty systems may introduce:
- vault-bound benefits
- per-vault discount logic
- non-emissive incentive structures

These features are explicitly **out of scope** for Genesis Pass v1.

---

## 11. Public-Facing Summary (UI / Docs)

### What Is the YieldLoop Genesis Pass?
The YieldLoop Genesis Pass is an optional, time-limited access pass for early users.

It provides:
- **Fee discounts on realized profits** (capped)
- **Deposit credits** that reduce the cost of creating a vault
- Early access and community participation

### What It Is Not
The Genesis Pass is not:
- an investment
- a yield product
- a profit share
- a dividend
- a governance right

### How Deposit Credits Work
- Credits may be issued from the protocol’s marketing budget
- Credits reduce the USDT needed to create a vault
- Credits cannot be withdrawn or transferred
- Credits expire after **90 days**
- Credits may be **zero** in any month

### Plain-English Rule
If YieldLoop does not generate profits or chooses not to issue credits, **nothing breaks**.

The system simply continues operating.

---

### Design Intent
YieldLoop is designed to be:
- boring
- transparent
- survivable

The Genesis Pass exists to reduce friction — not to promise outcomes.

---

*End of Genesis Pass Specification v1.0*
