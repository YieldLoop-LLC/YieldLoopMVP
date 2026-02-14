# Appendix A — Formal Economic Definitions

This appendix defines core economic variables and deterministic calculations used throughout the YieldLoop protocol.

All variables refer to on-chain state unless otherwise noted.

---

## A.1 Core Variables

S  = Total circulating LOOP supply  
F  = Current LOOP redemption floor price (USDT per LOOP)  
RR = Reward Reserve balance (USDT)  
RP = Ratchet Pool balance (USDT)  
SV = System Vault balance (USDT)  
L  = Redemption liability  

Redemption liability:

L = S × F

Coverage ratio:

Coverage = RR / L

Surplus per token:

SurplusPerToken = RP / S

---

## A.2 Performance Fee Calculation

Let:

P = Realized profit for vault cycle (USDT)  

If reward = USDT:

Fee = P × 0.15 × (1 − NFT_Discount)
NetToUser = P − Fee

If reward = LOOP:

Fee = P × 0.10 × (1 − NFT_Discount)
NetProfit = P − Fee
MintedLOOP = NetProfit / F

Equivalent USDT equal to NetProfit must be transferred to Reward Reserve.

---

## A.3 NFT Discount

Genesis Lite discount:

NFT_Discount = 0.20

Genesis Lifetime discount:

NFT_Discount = 0.50

Highest discount applies.
Discounts are not stackable.

---

## A.4 Buffer Requirement

Target coverage:

TargetCoverage ∈ [1.08 , 1.20]
ImmutableMinimumCoverage = 1.05

Required reserve to satisfy target:

RequiredRR = TargetCoverage × L

Buffer shortfall:

BufferShortfall = max(0 , RequiredRR − RR)

---

## A.5 Ratchet Eligibility

Ratchet may execute only if:

Coverage ≥ TargetCoverage
AND
UsableRP > 0
AND
CooldownElapsed = true

Where:

UsableRP = RP − BufferShortfall

If UsableRP ≤ 0, ratchet cannot execute.

---

## A.6 Maximum Permanent Floor Increase

Maximum floor increase supported by current surplus:

MaxPermanentIncrease = UsableRP / S

---

## A.7 Smoothing Function

Smoothing factor:

SmoothingFactor ∈ [0.05 , 0.30]

Proposed floor increase:

ΔF_raw = MaxPermanentIncrease × SmoothingFactor

Per-event cap:

ΔF_cap = min(ΔF_raw , 0.03 × F)

Final floor increase:

ΔF = ΔF_cap

---

## A.8 Ratchet Funding Transfer

When ΔF is applied:

Required transfer from RP to RR:

TransferAmount = S × ΔF

New balances:

RP_new = RP − TransferAmount
RR_new = RR + TransferAmount
F_new = F + ΔF

---

## A.9 Redemption

User redeems R LOOP:

USDT_out = R × F

Post redemption:

S_new = S − R
RR_new = RR − USDT_out

If RR insufficient:

Redemption enters FIFO queue until liquidity available.

---

## A.10 System Vault Profit Routing

Let:

SVP = Realized System Vault profit

Allocation:

RR_allocation ∈ [60% , 90%]
RP_allocation = 100% − RR_allocation

Distribution:

RR_new = RR + (SVP × RR_allocation)
RP_new = RP + (SVP × RP_allocation)

System Vault may not distribute funds elsewhere.

---

## A.11 Vault Capital Cap

Per vault:

VaultCap ∈ [25,000 , 50,000] USDT
HardMaximum = 50,000 USDT

Deposits exceeding cap require creation of new vault.

---

## A.12 Daily Governance Adjustment Limits

Coverage change per 24h:

≤ 0.02

Smoothing factor change per 24h:

≤ 0.05

Cooldown adjustment per 24h:

≤ 12 hours

System Vault split adjustment per 24h:

≤ 10%

These limits are contract enforced.

---

# Appendix B — Economic Invariant Summary

1. LOOP minting always increases Reward Reserve.
2. Floor increase always transfers capital from Ratchet Pool to Reward Reserve.
3. Floor cannot decrease.
4. Principal never funds reserves.
5. Redemption never mints new LOOP.
6. Governance cannot withdraw reserve capital.
7. System Vault profits strengthen reserves only.

YieldLoop operates under deterministic capital conservation principles.

