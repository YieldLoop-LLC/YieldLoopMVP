---

## YieldLoop Performance Fee Addendum (AUM-Scaled)

### Purpose
This addendum defines a **protocol-wide, AUM-scaled performance fee model** designed to:
- reward scale and durability
- align long-term users with protocol growth
- reduce fees as operational efficiency improves
- avoid fixed, permanent fee assumptions

Fee adjustments are **deterministic**, **non-promissory**, and may move **up or down** as conditions change.

---

## Baseline Performance Fees

At launch, YieldLoop applies the following performance fees on **realized net profit only**:

- **USDT payout mode:** **15%**
- **LOOP payout mode:** **10%**

No fees apply to:
- principal
- losses
- withdrawals
- idle periods
- emergency exits

---

## AUM-Scaled Fee Reduction Schedule

Performance fees decrease as **total protocol AUM** increases.

| Total Protocol AUM | USDT Fee | LOOP Fee |
|-------------------:|---------:|---------:|
| <$250k | 15% | 10% |
| $250k – $1M | 14.5% | 9.5% |
| $1M – $5M | 14% | 9% |
| $5M – $10M | 13% | 8% |
| $10M+ | **10%** | **5%** |

---

## AUM Definition (Strict)

For fee-tier purposes, **AUM includes only**:
- active user vault principal
- assets currently deployed or idle within vaults

AUM **excludes**:
- protocol treasuries
- marketing budgets
- deposit credits
- reward pools
- LOOP redemption pools
- admin or governance-held funds

---

## Snapshot and Enforcement Rules

- Fee tier is determined using a **single deterministic AUM snapshot**
- Snapshot timing occurs at:
  - vault cycle start, or
  - profit settlement (implementation-defined, but consistent)
- Fees **do not change mid-cycle**

---

## Dynamic Adjustment Rule

- Fee tiers may **increase or decrease** if protocol AUM rises or falls
- No tier is guaranteed permanently
- No user entitlement is created by prior tiers

---

## Interaction With Other Discounts

Order of operations:
1. Base performance fee determined by AUM tier
2. Genesis Pass or other discounts applied
3. **Hard cap enforced** (e.g. max 50% total discount)

Discounts must never:
- reduce fees below protocol-defined minimums
- bypass settlement logic
- alter execution behavior

---

## Non-Promise Statement

Fee reductions:
- reflect current scale and operating conditions
- are not guaranteed
- may be modified under governance control
- do not represent profit sharing or revenue allocation

---

*End of Fee Addendum*
