# YieldLoop Ecosystem Overview  
**Version:** v1.2 MVP  
**Network:** BNB Chain (BSC)  
**Model:** Self-Custody, Vault-Isolated, Deterministic Trading Protocol  

---

## 1. What YieldLoop Is

YieldLoop is a self-custody, automated vault-based trading protocol deployed on BNB Chain.

Users deposit USDT into isolated vaults.  
Each vault operates as an independent execution environment with deterministic accounting, strict guardrails, and user-approved rule constraints.

YieldLoop attempts to generate profit only through conservative spread capture and PCS↔BiSwap arbitrage when the expected net outcome clears defined thresholds after costs.

YieldLoop does not promise profits.  
YieldLoop does not guarantee capital preservation.  
YieldLoop does not mint yield.

Profit only exists when positions close.

---

## 2. Core Ecosystem Components

YieldLoop consists of four primary pillars:

- Isolated User Vaults  
- Execution & Strategy Engines  
- Deterministic Settlement & Accounting  
- Optional LOOP Rewards System  

Everything else exists to support these four pillars.

---

## 3. Vault Model

Each deposit creates or activates an isolated vault.

Properties:

- Minimum deposit: $250 USDT  
- Vaults are not pooled  
- Each vault has independent accounting  
- Each vault has its own state machine  
- One user may operate multiple vaults  

Each vault tracks separate internal ledgers:

- Principal remaining  
- Active position capital  
- Unrealized position value (UI only)  
- Withdrawable profit (USDT)  
- Suspense profit (USDT for LOOP rewards)  

This separation prevents:

- Profit mislabeling  
- Double counting  
- Cross-user contamination  
- Withdrawal exploits  

---

## 4. Vault Lifecycle (High Level)

1. Vault Created  
2. Deposit Pending  
3. Funded Idle  
4. Strategy Proposed  
5. Strategy Approved  
6. Executing  
7. Holding Position  
8. Settlement Pending  
9. Profit Credited  
10. Funded Idle (repeat)  
11. Close Requested  
12. Close Pending  
13. Closed  

Trading may only occur in:

- EXECUTING  
- CLOSE_PENDING  

All other states reject execution.

---

## 5. Strategy Object (User-Approved Rulebook)

YieldLoop does not “just trade.”

Before any execution, the protocol generates a Strategy Object that contains:

- Strategy type  
- Allowed DEX venues  
- Allowed tokens  
- Routing constraints  
- Profit thresholds  
- Slippage & impact caps  
- Liquidity minimums  
- Trade sizing limits  
- Cooldowns & daily limits  
- Oracle sanity rules  
- Fee rules  

The Strategy Object is hashed.

The user must approve this hash.

Execution is blocked if:

- Strategy is expired  
- Hash mismatches  
- Strategy not approved  

This binds execution to explicit user-approved constraints.

---

## 6. Allowed Strategy Types (v1.2)

- Spread Capture  
- PCS ↔ BiSwap Arbitrage  

No leverage.  
No lending.  
No derivatives.  
No perps.  

---

## 7. Trade Quality Gate (Survival Layer)

A trade may execute only if:

Expected Net Profit ≥ Minimum Required Net Profit

Expected Net Profit is computed pessimistically and already subtracts:

- Gas buffer  
- Slippage buffer  
- MEV penalty  
- Execution uncertainty  
- Fee impact  

Additional hard blocks:

- Slippage cap  
- Price impact cap  
- Liquidity minimum  
- Route hop limit  
- Oracle sanity checks  

If any check fails, the system does nothing.

Idle is a valid outcome.

---

## 8. Execution Philosophy

YieldLoop is not high-frequency.

The system may:

- Execute multiple trades in a day  
- Execute zero trades for extended periods  

Capital preservation outranks activity.

---

## 9. Settlement & Realized Profit

Profit only exists during settlement.

Settlement occurs only after a position is fully closed and converted to USDT.

Settlement sequence:

1. Snapshot balances  
2. Compute gross result  
3. If gross result ≤ 0 → loss  
4. If gross result > 0 → realized profit  
5. Compute performance fee  
6. Route fees  
7. Credit profit ledgers  
8. Emit ledger snapshot  

NAV changes alone never create profit.

---

## 10. Loss Handling

If a trade cycle results in a loss:

- Loss reduces principal remaining  
- No performance fee charged  

Losses are explicit and visible.

---

## 11. Performance Fees

Fees are charged only on realized profit.

No deposit fees.  
No fees on losses.

Fee routing (as % of fee):

- 25% Dev/Ops  
- 10% Marketing  
- 55% Ratcheting Stability Reserve  
- 10% LoopLabs  

All fees routed in USDT.

---

## 12. Profit Output Options

Each vault selects one payout mode:

### USDT Payout

- Net profit credited to withdrawableProfitUSDT  
- Withdraw anytime  

### LOOP Payout

- Net profit credited to suspenseProfitUSDT (USDT)  
- Converted to LOOP monthly via market buy  
- Distributed to user  

LOOP is never minted in v1.2.

---

## 13. Monthly LOOP Distribution

Once per month:

1. Suspense USDT used to buy LOOP on PCS/BiSwap  
2. LOOP credited to user  
3. Suspense balance reset to zero  

Rewards are funded only by real profits.

---

## 14. Ratcheting Stability Reserve System

55% of all performance fees are routed into a protocol reserve.

Purpose:

- Infrastructure continuity  
- Operational runway  
- Long-term survivability  
- Overcapitalization capacity  

Not insurance.  
Not price floor.  
Not user guarantee.

It represents retained protocol strength.

---

## 15. Engine Architecture (Modular)

YieldLoop uses separate engines:

- Vault Engine  
- Strategy Engine  
- Router Engine  
- Oracle Engine  
- Execution Engine  
- Settlement Engine  
- Fee Routing Engine  
- LOOP Rewards Engine  
- Pause/Kill Switch Engine  
- Telemetry/Event Engine  

No engine may bypass guardrails.

---

## 16. Pause & Safety Model

Vaults or global execution may pause on:

- Oracle stale  
- DEX outage  
- Excess slippage  
- Repeated tx failures  
- Admin emergency  

Paused vaults cannot trade.  
User funds remain intact.

---

## 17. Admin & Configuration

Admin may tune parameters with timelock:

- Thresholds  
- Buffers  
- Cooldowns  
- Limits  

Admin cannot change:

- Chain  
- DEX venues  
- Token whitelist  
- Profit asset (USDT)  
- LOOP minting prohibition  

Core identity remains fixed.

---

## 18. User Experience Contract

Users must always be able to see:

- Vault value  
- Principal remaining  
- Realized profit  
- Withdrawable profit  
- Suspense profit  
- Fees paid  
- Vault status  

Users must approve:

- Configuration  
- Strategy hash  
- Risk acknowledgement  

No hidden behavior.

---

## 19. Big Picture Summary

YieldLoop is a self-custody, vault-isolated, rule-bound execution machine that only converts real closed trading gains into withdrawable USDT or market-purchased LOOP, while accumulating protocol strength via retained fees.

No fake yield.  
No pooled opacity.  
No emission treadmill.

---

## 20. One-Sentence Definition

YieldLoop is an automated, deterministic DeFi protocol where users authorize constrained trading inside isolated vaults and receive profits only when positions close, with optional market-purchased token rewards and built-in protocol sustainability.
