# Section III — Guardrails and Risk Controls

---

## 3.1 Guardrail Philosophy

YieldLoop v1.x operates under fixed, immutable guardrails.

Guardrails are embedded directly in contract logic and cannot be modified post-deployment.

There is:

- No multisig parameter adjustment
- No governance tuning
- No risk expansion vote
- No runtime mutation of constraints

Guardrails define the maximum risk envelope of the protocol.

If market conditions change beyond this envelope, execution halts rather than adapts.

---

## 3.2 Execution Preconditions

Before any trade execution, all of the following must evaluate as TRUE:

1. Oracle validation passes
2. Liquidity threshold satisfied
3. Slippage projection within bounds
4. Price impact within bounds
5. Net profit threshold exceeded
6. Deployment percentage within cap
7. Route hop count ≤ maximum
8. Trade size ≤ pool percentage cap
9. Vault not in PAUSED state
10. No active emergency condition

If any check fails, execution is aborted.

No partial validation is permitted.

---

## 3.3 Liquidity Constraints

Minimum liquidity requirement:

minPoolLiquidityUSDT = 250,000

Pool liquidity must meet or exceed this threshold on both DEX venues.

Liquidity check occurs:

- At strategy generation
- Immediately prior to execution

If liquidity drops below threshold between proposal and execution, execution aborts.

This prevents:

- Low-liquidity manipulation
- Thin book exposure
- Spread illusion from shallow pools

---

## 3.4 Slippage and Price Impact Limits

Slippage cap:

maxSlippageBps = 50 (0.50%)

Price impact cap:

priceImpactCapBps = 20 (0.20%)

Both must be satisfied independently.

Slippage refers to execution deviation from expected output.

Price impact refers to trade size relative to pool depth.

Both caps are immutable.

---

## 3.5 Trade Size Constraints

Trade size limit:

maxTradeSizePctOfPoolBps = 20 (0.20%)

This prevents:

- Self-compression of arbitrage spread
- Excessive liquidity consumption
- Visible large-trade signaling

Deployment cap:

maxCapitalDeploymentPct = 80%

Remaining 20% remains idle.

No override permitted.

---

## 3.6 Net Profit Threshold

Minimum required net profit per execution:

minNetProfitUSDT = 3.00

Net profit is calculated after:

- Estimated gas cost
- Slippage modeling
- Execution fee assumptions

If projected net profit < threshold, strategy is not proposed.

This avoids micro-arbitrage noise and gas bleed.

---

## 3.7 Oracle Requirements

Oracle validation parameters:

maxOracleDeviationBps = 100 (1.00%)
maxOracleAgeSeconds = 120


Oracle data must:

- Be within 1% deviation of DEX reference
- Be no older than 120 seconds

If oracle fails:

- Vault enters PAUSED state
- No execution occurs

Oracle data is used for sanity validation only.
It does not determine trade pricing.

---

## 3.8 Route Constraints

Maximum route hops:

maxRouteHops = 2

No multi-hop routing beyond this limit.

This reduces:

- Complexity risk
- Gas unpredictability
- Multi-pool exposure
- Failure surface area

---

## 3.9 MEV and Mempool Assumptions

YieldLoop operates in public mempool conditions.

The protocol assumes:

- Transactions are visible prior to inclusion
- MEV actors may attempt back-running
- Spread compression may occur before confirmation

Mitigation comes from:

- Small trade sizing
- Conservative slippage caps
- Low deployment percentage
- No leverage

YieldLoop does not rely on private relay or flash-bundling in v1.x.

If MEV compression eliminates projected spread prior to execution, trade fails guardrail validation.

---

## 3.10 Volatility and Surge Conditions

YieldLoop does not modify guardrails during volatility.

Surge performance occurs only if:

- Market spreads widen
- Arbitrage frequency increases
- Liquidity dislocations persist within guardrail bounds

The protocol does not:

- Lower profit threshold
- Increase slippage cap
- Increase deployment percentage

Volatility expands opportunity.  
It does not expand risk envelope.

---

## 3.11 Failure Modes

Primary failure scenarios:

- DEX router outage
- Oracle outage
- Liquidity collapse
- Gas spike invalidating profitability
- Spread compression due to MEV

Failure response:

- Execution abort
- Vault enters PAUSED state
- No capital loss beyond attempted gas

System does not compound error states.

---

## 3.12 Emergency Doctrine

Emergency actions are limited to:

- Pause vault
- Unwind to base asset
- Prevent further execution

Emergency controls cannot:

- Increase exposure
- Override guardrails
- Force deployment
- Alter risk parameters

Emergency logic prioritizes capital preservation over opportunity capture.

---

## 3.13 Version Upgrade Policy

If guardrail changes are required due to structural market evolution:

- A new protocol version must be deployed
- Existing vaults remain bound to v1.x
- Migration must be voluntary
- No live mutation permitted

YieldLoop prefers version isolation over dynamic modification.

---

End of Section III
