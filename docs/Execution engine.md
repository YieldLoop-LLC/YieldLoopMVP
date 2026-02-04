# YieldLoop Execution Engine (v1.2) — Required Implementation Specification

Status: REQUIRED  
Scope: Protocol-level execution and arbitrage engine  
Applies To: YieldLoop v1.2 MVP  
Audience: Smart contract and backend engineers  
Developer must not deviate from this specification.

---

## 1. Purpose

Defines the deterministic behavior of the YieldLoop Execution Engine responsible for:

- Discovering trade opportunities
- Validating opportunities against guardrails
- Executing trades
- Settling results
- Emitting audit-grade events

The engine must be:

- Deterministic  
- Guardrail-first  
- Fail-closed  
- State-machine bound  

The engine does NOT:

- Mint LOOP  
- Handle governance  
- Move funds outside vault accounting rules  

---

## 2. Allowed Venues and Assets

### 2.1 DEX Venues (Hard Lock)

- PancakeSwap (PCS)
- BiSwap

No other venue is permitted in v1.2.

### 2.2 Whitelisted Vault Assets

- BTCB  
- ETH  
- SOL  
- XRP  
- BNB  
- USDT  

Any asset not in this list must be rejected at:

- Deposit
- Routing
- Execution

---

## 3. Engine Strategy Types

Engine must support the following Strategy Types:

| ENUM | Name | Description |
|-----:|------|-------------|
| 1 | SPREAD_CAPTURE | Single-venue spread capture |
| 2 | DEX_ARBITRAGE | PCS ↔ BiSwap arbitrage |

At least one must be implemented.  
Engine must preserve enum structure even if only one is active.

---

## 4. State Machine Binding

### 4.1 Trading Permission

Trades may occur ONLY when vault.state ∈:

- EXECUTING  
- CLOSE_PENDING  

Any attempt to trade in other states must revert.

### 4.2 Execution Entry Preconditions

Execution may begin only if:

- vault.state == STRATEGY_APPROVED  
- strategyHash == approvedStrategyHash  
- block.timestamp ≤ approvalExpiryUTC  
- venues in strategy allow-list ⊆ {PCS, BISWAP}

If any condition fails → revert.

---

## 5. Opportunity Scan Loop

### 5.1 Scheduling

Per-vault parameters:

- quoteScanIntervalSeconds = 60  
- quoteScanRandomJitterSeconds = 10  
- minSecondsBetweenTrades = 900  
- maxTradesPerDayPerVault = 12  

### 5.2 Candidate Generation

For each eligible vault:

1. Load StrategyObject  
2. Build route candidates within:
   - maxHops ≤ 2  
   - allowed intermediates  
   - allowed venues  
3. Generate:

- PCS buy → BiSwap sell  
- BiSwap buy → PCS sell  

4. Quote both legs  
5. Compute ExpectedNetProfitUSDT  

---

## 6. Trade Quality Gate

### 6.1 Core Rule

Execute only if:

ExpectedNetProfitUSDT ≥ minNetProfitUSDT

Default:

minNetProfitUSDT = 3.00 USDT

### 6.2 ExpectedNetProfitUSDT

ExpectedNetProfitUSDT =

grossQuoteOutUSDT  
- grossQuoteInUSDT  
- gasBufferUSDT  
- slippageBuffer  
- mevPenalty  
- executionUncertaintyBuffer  

### 6.3 Buffers

Defaults:

- gasBufferUSDT = 0.50  
- slippageBufferBps = 30  
- mevPenaltyBps = 20  
- executionUncertaintyBufferBps = 15  

### 6.4 Hard Rejection Conditions

Reject if any true:

- slippage > maxSlippageBpsPerSwap (50 bps)  
- priceImpact > maxPriceImpactBps (50 bps)  
- poolLiquidity < minPoolLiquidityUSDT (250,000 USDT)  
- tradeSize < minTradeSizeUSDT (25 USDT)  
- tradeSize > maxTradeSizePctOfPoolBps (20 bps)  

On rejection emit:

ExecutionGuardrailRejected(vaultId, strategyHash, reasonCode)

---

## 7. Execution Mode Selection

### 7.1 Priority Order

1. Atomic DEX_ARBITRAGE  
2. Conditional SPREAD_CAPTURE  
3. Idle

### 7.2 Atomic Arbitrage (Default)

Conditions:

- Valid PCS↔BiSwap route  
- Trade Quality Gate passed  

Behavior:

- Execute buy leg  
- Execute sell leg  
- Entire sequence treated as single trade cycle  

### 7.3 Holding Position (SPREAD_CAPTURE)

Allowed only if:

ExpectedNetProfitUSDT ≥ (2 × minNetProfitUSDT)

and

- oracle healthy  
- volatility within thresholds  

Behavior:

- Enter position  
- vault.state → HOLDING_POSITION  
- Wait for exit signal  

---

## 8. Exit Signals (Holding Position)

Exit signal may trigger when:

- TARGET_SPREAD_REACHED  
- TIME_DECAY_EXIT  
- RISK_EXIT  
- USER_CLOSEOUT  

Upon exit signal:

vault.state → EXECUTING  
exit trade executed  
cycle proceeds to settlement  

---

## 9. Atomicity and Failure Handling

### 9.1 Atomicity

Arbitrage legs must either:

- both succeed  
or  
- entire attempt aborts  

Partial success must not persist.

### 9.2 Retry Rules

- maxTxRetriesPerOpportunity = 1  

If failures exceed threshold:

vault.state → PAUSED  

---

## 10. Settlement

### 10.1 Realized Profit Definition

Profit exists only when:

- position closed  
- all swaps completed  
- output converted to USDT  
- fees applied  

### 10.2 Settlement Flow

EXECUTING  
→ SETTLEMENT_PENDING  
→ PROFIT_CREDITED  

### 10.3 Profit Ledger Rules

- withdrawableProfitUSDT stored as USDT only  
- suspenseProfitUSDT stored as USDT only  
- no unrealized profit credit  

---

## 11. LOOP Interaction

Execution Engine responsibilities:

- route realized profit into:
  - withdrawableProfitUSDT OR  
  - suspenseProfitUSDT  

Engine does NOT:

- mint LOOP  
- buy LOOP  
- distribute LOOP  

---

## 12. Emergency Pauses

Execution must pause if:

- oracle stale  
- oracle deviation  
- slippage breaches  
- DEX outage  
- repeated tx failures  
- gas spike  

When paused:

- trading disabled  
- profit withdrawals allowed  
- closeout allowed when safe  

---

## 13. Required Events

### Execution Layer

- ExecutionStarted(vaultId, strategyHash)  
- ExecutionCompleted(vaultId)  
- ExecutionGuardrailRejected(vaultId, strategyHash, reasonCode)  

### Trade Detail

- TradeDetail(
  vaultId,
  venueIn,
  venueOut,
  tokenIn,
  tokenOut,
  amountIn,
  amountOut,
  gasUsed
)

### Settlement

- SettlementStarted(vaultId)  
- SettlementCompleted(vaultId, realizedProfitUSDT, feeUSDT)  
- ProfitCredited(vaultId, profitUSDT)  
- LedgerSnapshot(vaultId, principalUSDT, withdrawableProfitUSDT, navUSDT)

---

## 14. Prohibited Behavior

Engine must never:

- trade in non-authorized states  
- bypass guardrails  
- execute without approved strategy hash  
- credit unrealized profit  
- mint LOOP  
- route to non-whitelisted venues  

Any violation is protocol-breaking.

---

## 15. Design Intent

The Execution Engine is conservative by design.

It prefers:

- missing opportunities  
over  
- risking catastrophic loss  

Idle is a valid outcome.

Survivability > frequency.
