# Ratchet Engine Specification (Canonical)

**Applies To:** YieldLoop v1.x  
**Status:** Canonical / Production-Ready  
**Purpose:** Protocol floor stabilization and monotonic floor advancement  
**Scope:** Protocol-level floor policy only (not market price control)

---

## 1. Design Intent

The Ratchet Engine defines a **monotonic protocol floor** for the YieldLoop receipt token (LOOP or successor),
funded exclusively by realized protocol fees.

The Ratchet Engine is designed to:
- Never decrease the declared protocol floor
- Never overspend its funding source
- Advance only when mathematically solvent
- Remain boring, slow, and non-gameable
- Pause safely when conditions are not met

The Ratchet Engine **does not**:
- Guarantee market price appreciation
- Defend the market chart
- Create profit
- Operate continuously
- Accelerate in response to demand or sentiment

---

## 2. Funding Source

### 2.1 Ratchet Pool

- A dedicated on-chain **Ratchet Pool**, denominated in USDT
- Funded by **55% of protocol performance fees**
- Performance fees are collected **only on realized profit**

No other source may be treated as primary ratchet funding.

---

## 3. Definitions

- **R**: Ratchet Pool balance (USDT)
- **S**: Circulating token supply included in floor coverage
- **F**: Current protocol floor price (USDT per token)
- **Δ**: Ratchet step size (initially $0.0001)
- **C_target**: Target coverage ratio (bounded, governance-configurable)
- **M**: Safety margin multiplier (bounded, governance-configurable)
- **Cooldown**: Minimum time between ratchet steps
- **lastClickTime**: Timestamp of most recent ratchet step

---

## 4. Hard Rules (Non-Negotiable)

1. The protocol floor **never decreases**
2. Only **one ratchet step** may occur per cooldown window
3. Ratchet steps may **never chain** or batch
4. Ratchet execution may **never bypass cooldown**
5. No manual, emergency, or administrative overrides exist
6. If conditions are not met, the ratchet **does nothing**
7. Market price behavior is irrelevant to ratchet eligibility

---

## 5. Step Size

- Initial step size: **Δ = $0.0001**
- Step size may change only via governance timelock
- Step size changes:
  - Must be bounded
  - Apply only to future steps
  - Cannot retroactively alter the floor

---

## 6. Solvency Gate (Click Condition)

A ratchet step is permitted **only if** all conditions below are met:

1. **Cooldown satisfied**  
   `currentTime ≥ lastClickTime + Cooldown`

2. **Solvency condition satisfied**

R ≥ S × (F + Δ) × C_target × M

If either condition fails, no ratchet occurs.

---

## 7. Ratchet Execution

When a ratchet step executes:

1. Floor price is updated:

F := F + Δ

2. `lastClickTime` is updated
3. A `RatchetClicked` event is emitted
4. Excess solvency remains unused

Unused solvency **does not** accelerate future ratchets.

---

## 8. Cooldown Logic

### 8.1 Defaults and Bounds

- Default cooldown: **24 hours**
- Minimum cooldown: **12 hours**
- Maximum cooldown: **72 hours**

Cooldown is:
- Deterministic
- Parameterized
- Timelock-governed
- Never manually overridden

---

### 8.2 Simple Dynamic Cooldown

Cooldown adjusts automatically based on reserve behavior and ratchet frequency.

Inputs:
- **growth30d** = (R_now − R_30d_ago) / max(R_30d_ago, ε)
- **clicks7d** = ratchet steps executed in the last 7 days

Rules (evaluated at each eligibility check):

1. Start at **24 hours**
2. If `clicks7d ≥ 3` → set cooldown to **72 hours**
3. Else if `growth30d < threshold_low` → set cooldown to **48 hours**
4. Else if `no click in 14 days AND growth30d ≥ threshold_high` → set cooldown to **12 hours**
5. Otherwise → retain **24 hours**

No price, volume, or sentiment inputs are used.

---

## 9. Floor Support Spending (Throttle)

The Ratchet Pool may optionally support the floor via buy-and-burn actions,
subject to strict spending limits.

- Spending is **capped per epoch**
- Spending may be **partial or queued**
- Spending may pause automatically under stress
- Floor policy remains valid even if support actions pause

The protocol never spends faster than defined limits.

---

## 10. Supply Definition (S)

The circulating supply used in solvency calculations **must be explicitly defined**.

Recommended exclusions:
- Team-held tokens
- Treasury-held tokens
- Locked or time-vested tokens
- Protocol-owned balances

Supply definition is immutable or timelock-governed.

---

## 11. Failure and Stress Behavior

Under adverse conditions:
- Ratchet steps pause
- Cooldown may extend automatically
- Spending throttles tighten
- Floor remains unchanged (monotonic)

The system does **not** attempt recovery actions.

---

## 12. Governance Constraints

Governance may adjust only:
- Step size (Δ) within bounds
- Coverage target (C_target) within bounds
- Safety margin (M) within bounds
- Cooldown bounds within bounds

All changes:
- Require timelock
- Emit events
- Apply prospectively only

Governance **cannot**:
- Force a ratchet step
- Reduce the floor
- Bypass solvency rules
- Override cooldown

---

## 13. Events

- `RatchetClicked(F, R, S)`
- `CooldownUpdated(newCooldown)`
- `RatchetPaused(reason)`

---

## 14. Summary

The Ratchet Engine is not a growth engine.  
It is a **credibility engine**.

It advances slowly, pauses often, and survives indefinitely.

If it ever feels exciting, it is misconfigured.

