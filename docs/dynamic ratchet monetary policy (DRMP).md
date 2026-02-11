# Appendix I — Dynamic Ratchet Monetary Policy (DRMP)

**Document:** YieldLoop — Automated Vault Trading Protocol + LOOP Rewards (BNB Chain)  
**Version:** v1.2+  
**Appendix:** I  
**Status:** REQUIRED MONETARY POLICY SPECIFICATION (Non-Discretionary, Deterministic)

---

## I.1 Purpose

The Dynamic Ratchet Monetary Policy (DRMP) governs:

- minimum required Ratchet reserve coverage  
- maximum overcapitalization thresholds  
- relief valve activation rules  
- shock protection mechanisms  
- deterministic reserve management logic  

The DRMP ensures:

- the Ratchet pool is never underfunded  
- no discretionary distribution is possible  
- no governance vote can drain the Ratchet  
- system stability overrides incentive distribution  
- monetary policy remains formula-based and predictable  

---

## I.2 Core Invariants

### I.2.1 Coverage Ratio

Define:

- `RatchetPoolUSDT` = total USDT held in the Ratchet reserve pool  
- `LOOPSupply` = total LOOP supply outstanding  
- `LOOPUSDTEquivalent` = `LOOPSupply * 1.00` (because LOOP is defined as a 1 USDT claim unit)

Compute:

`CoverageRatio = RatchetPoolUSDT / LOOPUSDTEquivalent`

### I.2.2 Hard Rule: Never Leave the Ratchet Short

At all times the protocol must enforce:

`CoverageRatio >= DynamicLowerBound`

If `CoverageRatio < DynamicLowerBound`, then:

- Relief Valve MUST be disabled  
- all Ratchet inflows MUST be retained  
- no overflow distribution, crediting, or rerouting may occur  
- any attempted distribution MUST revert  

---

## I.3 Dynamic Coverage Bands

The protocol defines a dynamic band:

- `DynamicLowerBound` = minimum required coverage ratio  
- `DynamicUpperBound` = maximum tolerated coverage ratio before overflow logic is permitted  

### I.3.1 Dynamic Lower Bound Formula (Deterministic)

`DynamicLowerBound = BaseCoverage + (α*V) + (β*R) + (γ*G) + (δ*L)`

Where:

- `BaseCoverage` = baseline reserve requirement (e.g., 1.10 = 110%)  
- `V` = VolatilityFactor  
- `R` = RedemptionVelocityFactor  
- `G` = AUMGrowthFactor  
- `L` = LiquidityStressFactor  
- `α, β, γ, δ` = hardcoded coefficients (timelock-modifiable only)

### I.3.2 Factor Definitions (Implementation-Agnostic)

The exact oracle sources may vary, but the factor outputs must be deterministic.

**VolatilityFactor (`V`)**  
A bounded value representing trailing realized volatility of the protocol’s traded universe (30–90 day window).  
- higher volatility ⇒ higher `V` ⇒ higher `DynamicLowerBound`

**RedemptionVelocityFactor (`R`)**  
A bounded value representing recent redemption pressure (e.g., 7–30 day).  
Example basis: `LOOPRedeemedRecent / LOOPSupply` (smoothed).  
- higher redemptions ⇒ higher `R` ⇒ higher `DynamicLowerBound`

**AUMGrowthFactor (`G`)**  
A bounded value representing recent net AUM growth (e.g., 30 day).  
- faster growth ⇒ higher `G` ⇒ higher `DynamicLowerBound`

**LiquidityStressFactor (`L`)**  
A bounded value representing liquidity depth and slippage stress for relevant venues (PCS/BiSwap) and any LOOP purchase routing (if applicable).  
- lower liquidity / higher slippage ⇒ higher `L` ⇒ higher `DynamicLowerBound`

### I.3.3 Bounding Rules (Required)

Each factor MUST be bounded to prevent extreme oscillation:

- `V ∈ [0, Vmax]`  
- `R ∈ [0, Rmax]`  
- `G ∈ [0, Gmax]`  
- `L ∈ [0, Lmax]`

`Vmax, Rmax, Gmax, Lmax` are hardcoded constants (timelock-modifiable only).

### I.3.4 Smoothing / Cooldown Rules (Required)

To avoid frequent toggling:

- `DynamicLowerBound` MAY update only at a fixed cadence (e.g., hourly or daily)  
- updates MUST be smoothed using a moving average or step limiter  
- a cooldown MUST apply between band changes  

The goal is stability, not twitchy behavior.

---

## I.4 Dynamic Upper Bound

### I.4.1 Upper Bound Definition

`DynamicUpperBound = DynamicLowerBound + BufferBand`

Where:

- `BufferBand` is a hardcoded buffer (e.g., 0.10–0.20)  
- `BufferBand` MUST be non-negative  
- `BufferBand` is timelock-modifiable only  

### I.4.2 Upper Bound Purpose

The upper bound exists to:

- prevent excessive idle capital trapped in Ratchet  
- reduce political pressure to “unlock the bag”  
- allow controlled, rule-based capital efficiency actions  
- ensure the Ratchet remains strong during stress periods  

---

## I.5 Relief Valve Eligibility (Overflow Gate)

Relief Valve logic is permitted only if BOTH are true:

1) `CoverageRatio >= DynamicLowerBound`  
2) `CoverageRatio > DynamicUpperBound`

If either fails:

- Relief Valve MUST be disabled  
- any attempt to route overflow MUST revert  

---

## I.6 Excess Calculation (Residual-Based)

Overflow is not based on the full Ratchet pool. It is based on residual excess above the upper band.

### I.6.1 Excess Amount

`ExcessUSDT = RatchetPoolUSDT - (DynamicUpperBound * LOOPUSDTEquivalent)`

Hard rule:

- if `ExcessUSDT <= 0` then `ExcessUSDT = 0` and no relief action may occur  

### I.6.2 Maximum Eligible Relief

Even when excess exists, only a fraction may be used.

`EligibleReliefUSDT = ExcessUSDT * ReliefCoefficient`

Where:

- `ReliefCoefficient ∈ (0, 1]`  
- default should be conservative (e.g., 0.20–0.40)  
- timelock-modifiable only  

Hard rule:

- the protocol MUST NOT distribute 100% of excess in one action unless explicitly set and timelocked  

---

## I.7 Shock Protection (Anti-Drain Circuit)

The Relief Valve MUST auto-disable if the system detects a shock.

### I.7.1 Shock Triggers (Any)

Relief Valve is disabled for a lock period if any occur:

- `CoverageRatio` drops by more than `ShockDropPct` within `ShockWindowSeconds`  
- redemption velocity exceeds a threshold (`R > R_shock`)  
- oracle health is invalid / stale  
- DEX venue outage / routing failure  
- severe slippage events exceeding a threshold  

### I.7.2 Lock Period

If triggered:

- Relief Valve disabled for `ShockLockSeconds`  
- all Ratchet inflows retained during lock  
- lock expiration is deterministic and time-based  
- manual override is not permitted except via emergency kill switch mechanisms already defined elsewhere  

---

## I.8 Relief Output Mode: Deposit Credit Distribution (Option E)

If the protocol uses relief distribution externally, it MUST be expressed only as **non-withdrawable deposit credit**.

### I.8.1 Deposit Credit Definition

Deposit Credit is:

- non-transferable  
- non-withdrawable  
- not redeemable for USDT  
- not convertible to LOOP  
- not counted as profit  
- not reflected in vault NAV, principal, or withdrawable profit ledgers  
- usable only to offset the user’s future deposit requirement  
- expiry-based (credits expire if not used)  

Deposit Credit is not a payout. It is an onboarding / efficiency mechanism.

### I.8.2 Eligibility Set

Credits may be distributed only to:

- active vaults in allowed states (e.g., FUNDED_IDLE, HOLDING_POSITION, PROFIT_CREDITED)  
- vaults not in PAUSED, ERROR_LOCK, CLOSE_PENDING, or CLOSED  

### I.8.3 Distribution Weighting

Credits must be distributed proportionally by vault principal basis:

`VaultWeight = principalRemainingUSDT / Sum(principalRemainingUSDT across eligible vaults)`

`VaultCreditUSDT = EligibleReliefUSDT * VaultWeight`

### I.8.4 Per-Vault Caps (Required)

To prevent abuse and “free deposits”:

- per-vault credit application cap: `CreditMaxPctOfDeposit` (e.g., 20%)  
- per-vault cumulative credit cap per period: `CreditMaxUSDTPerVault` (hardcoded)  

### I.8.5 Credit Expiry (Required)

Credits must expire:

- `CreditExpirySeconds` (e.g., 180 days)  
- expired credit is deleted and cannot be reclaimed  
- expiry must be deterministic and per-credit  

### I.8.6 Credit Application Rules

When a user makes a deposit:

- credit may offset up to the configured percentage of the deposit amount  
- any remaining credit persists until expiry  
- credit cannot be applied if it would reduce the net deposit below the minimum required deposit for that action  
- credit cannot bypass minimum deposit rules unless explicitly allowed by a separate rule set  

### I.8.7 No Marketing Claims

The protocol and frontend MUST NOT market Deposit Credit as:

- yield  
- rewards  
- profit share  
- dividends  
- guaranteed value  

It must be described only as:

- “deposit credit”  
- “fee-neutral onboarding credit”  
- “system efficiency credit”  

---

## I.9 Determinism and Governance Constraints

### I.9.1 Non-Discretionary Requirement

Relief Valve behavior must be:

- formula-driven  
- deterministic  
- triggered only by the rules above  
- not subject to governance vote at runtime  

### I.9.2 Parameter Change Rules

The following values are timelock-modifiable only:

- `BaseCoverage`  
- `α, β, γ, δ`  
- factor bounds (`Vmax, Rmax, Gmax, Lmax`)  
- `BufferBand`  
- `ReliefCoefficient`  
- shock parameters (`ShockDropPct`, `ShockWindowSeconds`, `ShockLockSeconds`, `R_shock`)  
- credit caps and expiry parameters  

All changes must emit an immutable event:

`MonetaryPolicyChanged(key, oldValue, newValue, editedBy, timestampUTC)`

---

## I.10 Required Events (Auditability)

The protocol must emit:

- `CoverageBandUpdated(dynamicLowerBound, dynamicUpperBound, timestampUTC)`  
- `ReliefValveActivated(excessUSDT, eligibleReliefUSDT, timestampUTC)`  
- `ReliefValveDisabled(reasonCode, timestampUTC)`  
- `DepositCreditDistributed(totalCreditsUSDT, eligibleVaultCount, timestampUTC)`  
- `DepositCreditApplied(vaultId, creditUsedUSDT, remainingCreditUSDT, timestampUTC)`  
- `DepositCreditExpired(vaultId, expiredCreditUSDT, timestampUTC)`  

Reason codes must be standardized for:

- shock trigger  
- oracle invalid  
- DEX outage  
- coverage below bound  
- manual emergency pause  

---

## I.11 Non-Negotiable Rules

- the Ratchet must never be left short due to relief actions  
- relief actions are permitted only on residual excess above the dynamic upper band  
- shock protection must disable relief automatically during stress  
- deposit credit is the only permitted “external” relief form in v1.x if enabled  
- deposit credit must never be withdrawable or convertible into USDT/LOOP  
- no discretionary “we decided to distribute” actions are allowed  
