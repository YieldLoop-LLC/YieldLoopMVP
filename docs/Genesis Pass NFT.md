# YieldLoop Genesis Pass NFT Program

**Document Version:** v1.2  
**Applies To:** YieldLoop v1.2 MVP  
**Status:** Canonical Specification  
**Last Updated:** [set date]

---

## 1. Purpose

The Genesis Pass NFT Program exists to:

- Fund early YieldLoop development and operations
- Recognize and reward early supporters
- Provide **usability, access, and fee-related benefits**
- Avoid emissions, yield promises, or profit guarantees
- Preserve protocol durability and regulatory resilience

Genesis Pass NFTs are **not investments**, **not yield instruments**, and **not ownership interests**.  
They provide **convenience and access**, not financial outcomes.

---

## 2. What a Genesis Pass Is — and Is Not

### 2.1 What It Is

A Genesis Pass is:
- A non-fungible access credential
- A permanent account-level usability modifier
- A mechanism to reduce friction for committed users
- A way to support protocol development early

Benefits are deterministic, transparent, and bounded.

---

### 2.2 What It Is Not

A Genesis Pass is **not**:
- A promise of profit or yield
- A performance multiplier
- A governance right (unless explicitly defined elsewhere)
- An ownership stake in YieldLoop
- A guarantee of outcomes

Genesis Pass ownership does **not** affect:
- Strategy selection
- Trade execution
- Risk controls
- LOOP minting
- Profit generation

---

## 3. Genesis Pass Tiers

YieldLoop defines two Genesis Pass tiers:

### 3.1 Basic Genesis Pass
- Entry-level Genesis supporter tier
- Permanent and non-expiring
- Provides limited usability and access benefits

### 3.2 Lifetime Genesis Pass
- Highest Genesis supporter tier
- Permanent and non-expiring
- Provides maximum allowable usability and access benefits

Tier enforcement is handled at the contract level.

---

## 4. Vault Creation Rules (Hard Constraint)

### 4.1 Initial Vault Creation (All Users)

Opening a YieldLoop vault requires a **$1,000 minimum initial deposit**.

This requirement:
- Applies to all users
- Cannot be bypassed by Genesis Pass NFTs
- Exists to prevent dust vaults and execution inefficiency
- Mirrors traditional managed investment platforms

Genesis Pass NFTs **do not** reduce the initial vault creation minimum.

---

## 5. Subsequent Deposit Minimums (Usability Benefit)

Once a vault exists, additional deposits are permitted subject to the following minimums:

| User Type | Subsequent Deposit Minimum |
|---------|---------------------------|
| Standard User | $250 |
| Basic Genesis Pass Holder | $175 |
| Lifetime Genesis Pass Holder | $100 |

Rules:
- Reduced minimums apply **only** to subsequent deposits
- They affect deposit convenience only
- They do not modify execution behavior, strategy logic, fees, or accounting
- They do not alter risk or return profiles

---

## 6. Fee-Related Benefits (If Enabled)

Genesis Pass NFTs may provide **bounded fee-related benefits**, subject to protocol configuration.

Possible benefits include:
- Reduced performance fees
- Reduced withdrawal-related fees (if applicable)

Rules:
- Fee discounts are optional and configurable
- Discounts are bounded by governance-defined limits
- Discounts do not apply retroactively
- Discounts do not modify profit allocation logic

Exact fee mechanics are defined in the Performance Fee Addendum.

---

## 7. Platform-Funded Deposit Credits (Non-Cash)

The protocol may, at its discretion, allocate a portion of platform revenue
to Genesis Pass holders in the form of **deposit credits**.

Deposit credits:
- Are denominated in USDT-equivalent value
- Are applied directly to vault deposits
- Reduce required out-of-pocket capital
- Are **not withdrawable**
- Are **not redeemable for cash**
- Do not represent ownership or profit entitlement

Credits may expire if unused, subject to configuration.

Deposit credits function as **platform-funded participation incentives**, not income.

---

## 8. Zero-Benefit Condition (Explicit)

Genesis Pass benefits are **not guaranteed**.

The protocol may, at any time:
- Provide zero deposit credits
- Provide zero fee discounts
- Temporarily disable Genesis-related benefits

Zero-benefit periods:
- Do not constitute a breach
- Do not create an obligation
- Do not imply failure of the Genesis Pass program

Genesis Pass ownership does not entitle the holder to continuous benefits.

---

## 9. Community and Communication Access

Genesis Pass holders may receive access to:
- Private or gated Discord channels
- Early announcements and protocol updates
- Development progress discussions
- Feedback and community discussion spaces

Community access:
- Confers no governance rights by default
- Does not guarantee influence over protocol decisions
- May be modified or revoked as needed

---

## 10. Early Access and Previews (Optional)

Genesis Pass holders may receive:
- Early access to new UI features
- Participation in beta testing
- Preview access to upcoming tools or dashboards

Early access does **not**:
- Affect execution priority
- Change protocol behavior
- Grant financial or strategic advantage

---

## 11. Interaction with LOOP

Genesis Pass NFTs have **no direct interaction** with LOOP.

Specifically:
- NFTs do not mint LOOP
- NFTs do not boost LOOP issuance
- NFTs do not affect LOOP redemption
- NFTs do not modify the LOOP Redemption Pool

LOOP remains:
- A mint-and-burn receipt token
- Backed only by realized trading profit
- Independent of NFT ownership

---

## 12. Transferability and Activation

Genesis Pass NFTs may be transferable unless restricted by contract.

Optional activation rules (if enabled):
- NFT must be held or staked to activate benefits
- Unstaking pauses benefits
- One Genesis Pass may be active per vault

Benefits apply only to the current holder.

---

## 13. Governance, Pausing, and Control

The protocol may, at any time:
- Pause Genesis Pass issuance
- Pause Genesis-related deposit credits
- Pause Genesis-related fee discounts
- Modify Genesis-related access features

Such actions:
- Do not affect user vault balances
- Do not move funds
- Do not retroactively alter applied benefits

All Genesis Pass parameters are:
- Hard-coded or governance-bounded
- Subject to timelock where applicable
- Non-discretionary once applied

Genesis Pass NFTs do **not** grant governance rights unless explicitly stated elsewhere.

---

## 14. Risk Disclosure

Holding a Genesis Pass NFT:
- Does not reduce trading risk
- Does not guarantee profit
- Does not insure deposits
- Does not prevent losses

YieldLoop is a trading protocol. Losses are possible.

Genesis Pass NFTs improve **experience**, not outcomes.

---

## 15. Design Intent (Plain English)

Genesis Pass NFTs exist to:
- Fund development honestly
- Reward early supporters responsibly
- Provide convenience without entitlement
- Avoid hype mechanics
- Avoid Ponzi-shaped incentives

They are intentionally boring.

Boring systems survive.

---

## 16. Non-Negotiable Constraints

The protocol must never:
- Tie NFT ownership to profit guarantees
- Allow NFTs to bypass vault seriousness thresholds
- Modify execution behavior based on NFT tier
- Mint LOOP based on NFT ownership
- Introduce hidden or preferential advantages

Any such change would invalidate the Genesis Pass design.
