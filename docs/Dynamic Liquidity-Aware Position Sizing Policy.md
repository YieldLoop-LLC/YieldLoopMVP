# YieldLoop — Dynamic Liquidity-Aware Position Sizing Policy
Version: 1.0  
Status: Core Risk Control Module  
Scope: Trading Engine Enforcement Layer  

---

# 1. Purpose

This policy defines how YieldLoop dynamically sizes trades relative to real-time liquidity depth in order to:

- Preserve structural trading edge
- Prevent self-induced slippage compression
- Avoid becoming the liquidity it attempts to harvest
- Ensure long-term scalability of the system
- Protect expectancy across AUM growth cycles

This module is mandatory for sustainable operation.

---

# 2. Core Principle

No trade may exceed a bounded percentage of real-time executable liquidity within a defined price impact window.

Edge must remain positive **after**:

- Slippage
- Gas
- DEX fees
- Price impact
- Adverse selection risk

If net expectancy ≤ 0 → trade is not executed.

---

# 3. Definitions

Let:

- `AUM_total` = Total deployed capital across all active vaults
- `AUM_vault` = Capital allocated within a single vault
- `D_x` = Available liquidity depth within X% price impact window
- `X` = Maximum allowable price impact window (e.g., 0.25%–0.50%)
- `T_proposed` = Proposed trade size
- `MIF` = Max Impact Factor (percentage of D_x allowed per trade)
- `GMIF` = Global Max Impact Factor (aggregate limit across vaults)
- `S_expected` = Estimated slippage from trade size
- `G_cost` = Gas cost (converted to USDT equivalent)
- `F_dex` = DEX trading fee
- `Spread_raw` = Observed cross-DEX spread (arbitrage case)
- `Edge_net` = Final net profitability after all costs

---

# 4. Liquidity Window Calculation

For each trade candidate:

1. Determine liquidity depth within defined impact window:

   D_x = Liquidity available within X% price movement.

2. Set Maximum Trade Size:

   T_max = D_x × MIF

Constraint:
   T_proposed ≤ T_max

If T_proposed > T_max → T_proposed = T_max

---

# 5. Multi-Vault Aggregation Constraint

To prevent cumulative impact:

Let:

T_aggregate = Sum of concurrent trade sizes across vaults on same pair

Constraint:
T_aggregate ≤ D_x × GMIF

GMIF must be strictly less than 1.0.

If aggregate constraint exceeded:
- Queue or throttle additional vault executions
- Or reduce per-vault proportional allocation

---

# 6. Arbitrage Execution Constraint

For arbitrage trades:

Edge_net = Spread_raw − S_expected − G_cost − F_dex

Trade executes only if:

Edge_net ≥ MinimumEdgeThreshold

Where:

MinimumEdgeThreshold must exceed:
- Historical execution variance
- MEV risk buffer
- Latency risk buffer

If not satisfied → No trade.

---

# 7. Volatility Harvesting Constraint

For rebalancing trades:

Trade only if:

- Rebalance deviation exceeds defined threshold
- Estimated slippage ≤ RebalanceImpactLimit
- Post-trade allocation remains within asset cap

RebalanceImpactLimit must be tighter than arbitrage impact limits.

---

# 8. Liquidity Compression Response

If liquidity depth shrinks due to:

- Market stress
- Liquidity withdrawal
- Volatility spike

Then automatically:

- Reduce MIF
- Reduce GMIF
- Increase USDT buffer allocation
- Increase MinimumEdgeThreshold

This ensures dynamic contraction under stress.

---

# 9. AUM Scaling Behavior

As AUM_total increases:

- Per-vault trade sizes increase proportionally
- Liquidity constraint limits effective size
- Return per dollar compresses gradually
- Edge degrades softly, not catastrophically

System must never override liquidity constraints to preserve optics.

---

# 10. Drawdown Integration

If per-asset drawdown threshold triggers:

- Reduce exposure percentage
- Increase stable allocation
- Tighten liquidity impact factor

Liquidity-aware sizing integrates with deterministic risk controls.

---

# 11. Hard Invariants

The following rules are immutable:

1. No trade may exceed liquidity-based maximum.
2. No arbitrage trade executes with negative expected net edge.
3. Aggregate exposure may not exceed GMIF constraint.
4. Governance may tighten limits but may not loosen beyond predefined safe range.
5. Liquidity constraints override return targets.

---

# 12. Governance Control Parameters

Governance may adjust within bounded ranges:

- X (impact window)
- MIF (max impact factor)
- GMIF (global impact factor)
- MinimumEdgeThreshold

Governance may NOT:

- Disable liquidity-aware enforcement
- Override trade-size caps
- Force execution of negative-edge trades

All changes emit on-chain events.

---

# 13. Failure Mode Protection

If:

- Liquidity oracle fails
- Pool depth data unavailable
- Execution simulation fails

System behavior defaults to:

- Trade rejection
- Stable allocation increase
- Arbitrage suspension

Safety > execution.

---

# 14. Expected Outcome

This module ensures:

- AUM growth does not erase structural inefficiency
- Edge degrades gradually at scale
- Market stress automatically reduces risk
- System sustainability extends across volatility regimes

---

# 15. Strategic Philosophy

YieldLoop does not pursue maximum throughput.

YieldLoop preserves edge.

If liquidity constraints reduce returns from 2.0% to 1.1% monthly at scale:

This is acceptable.

Edge preservation is superior to short-term performance optics.

---

# End of Policy
