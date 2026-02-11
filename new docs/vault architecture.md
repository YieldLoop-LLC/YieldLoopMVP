# Section II — Vault Architecture

---

## 2.1 Architectural Overview

YieldLoop is a vault-based arbitrage protocol operating on BNB Chain.

Each vault is:

- Isolated
- Non-custodial
- Independently accounted
- Strategy-approved per cycle
- Executed within immutable guardrails

There is no pooled capital model.

There is no cross-vault profit sharing.
There is no loss socialization.
There is no shared deployment engine.

Each vault functions as an independent execution unit.

---

## 2.2 Supported Network and Venues (v1.x)

### Network
- BNB Smart Chain (BSC)

### Supported DEX Venues
- PancakeSwap (v2 pools)
- Biswap (v2 pools)

No additional DEX integrations exist in v1.x.

Adding new venues requires protocol version upgrade.

---

## 2.3 Supported Base Settlement Asset

v1.x settlement asset:

- USDT (primary base asset)

All profit settlement occurs in USDT.

All performance fees are calculated in USDT.

Vault capital may temporarily hold intermediate tokens during execution but must return to USDT at settlement.

No multi-base accounting exists in v1.x.

---

## 2.4 Approved Pair Policy

Pairs must satisfy all of the following:

- Listed on both supported DEXs
- Meet minimum liquidity threshold (see Guardrails)
- Oracle-verifiable pricing available
- No transfer-tax tokens
- No rebasing tokens
- No reflection tokens
- No fee-on-transfer tokens

Pair types allowed:

- Stable / Stable
- Blue-chip / Stable
- High-liquidity Major / Stable

Speculative low-liquidity tokens are excluded.

Pair whitelist is enforced at contract level.

---

## 2.5 Vault Lifecycle

### Creation

Vault is created when:

- Initial deposit ≥ $500 USDT equivalent
- Wallet address assigned as owner
- Vault state set to FUNDED_IDLE

### Deposits

- Minimum initial deposit: $500
- Minimum subsequent deposit: $100

Deposits increase principal.
Deposits do not alter guardrails.
Deposits do not reset immutable parameters.

---

## 2.6 Vault State Machine

States:

- UNINITIALIZED
- FUNDED_IDLE
- STRATEGY_PROPOSED
- STRATEGY_APPROVED
- EXECUTING
- SETTLEMENT_PENDING
- SETTLED
- PAUSED
- EMERGENCY_UNWIND

No state may bypass validation.

All transitions are event-emitting and deterministic.

---

## 2.7 Strategy Generation

Strategy proposal logic evaluates:

- Cross-DEX price discrepancy
- Oracle-validated reference pricing
- Liquidity depth
- Slippage projection
- Gas cost estimation
- Net profit threshold validation
- Deployment percentage constraint

Strategy proposal includes:

- Asset pair
- Route path (max 2 hops)
- Capital deployment amount
- Estimated net profit
- Execution guardrail confirmation

User must approve strategy before execution begins.

No auto-trade without approval.

---

## 2.8 Execution Constraints (Immutable)

The following parameters are hardcoded in v1.x:

- minNetProfitUSDT = 3.00
- maxSlippageBps = 50 (0.50%)
- priceImpactCapBps = 20 (0.20%)
- maxTradeSizePctOfPoolBps = 20 (0.20%)
- maxCapitalDeploymentPct = 80%
- minPoolLiquidityUSDT = 250,000
- maxRouteHops = 2
- maxOracleDeviationBps = 100 (1.00%)
- maxOracleAgeSeconds = 120

All conditions must be satisfied prior to execution.

If any constraint fails, trade is aborted.

No override mechanism exists.

---

## 2.9 Capital Deployment Model

Maximum capital deployed per execution cycle:

80% of vault capital.

Remaining 20% remains idle as:

- Liquidity buffer
- Gas reserve
- Safety buffer

No leverage.
No borrowing.
No margin.

Deployment is spot-only.

---

## 2.10 Profit Recognition and Settlement

Profit is recognized only when:

- All positions fully unwind
- Vault balance returns to USDT
- Net delta > 0 after gas

If net delta ≤ 0:
- No performance fee applied.

If net delta > 0:
- 20% performance fee applied to realized profit only.

Principal is never touched.

No unrealized PnL is used in accounting.

---

## 2.11 Soft Vault Cap

Vaults may be subject to a soft operational cap to preserve liquidity efficiency.

When vault reaches cap:

- Additional deposits rejected
- Profits may compound
- User may create additional vault

No forced withdrawals occur.

---

## 2.12 Emergency Controls

### Pause Trigger Conditions

Vault enters PAUSED state if:

- Oracle failure
- Excess deviation
- DEX router failure
- Liquidity collapse below threshold
- Guardrail violation detected

### Emergency Unwind

Emergency unwind:

- Closes all open positions
- Converts holdings to USDT
- Returns vault to FUNDED_IDLE

Emergency functions cannot increase exposure.

---

## 2.13 Deterministic Doctrine

Vault behavior is deterministic.

There is:

- No parameter mutation
- No governance-based risk escalation
- No multisig guardrail adjustment
- No live tuning

Any structural change requires new protocol deployment.

---

End of Section II
