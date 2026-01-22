# YieldLoop Whitepaper

**Document Title:** YieldLoop — Automated Vault Trading Protocol + LOOP Rewards (BNB Chain)  
**Version:** v1.2  
**Author:** Todd Koletsky  
**Date:** January 22, 2026  

---

## System Information (v1 Core Spec)

**Network:** BNB Chain (BSC)  
**Custody Model:** Self-Custody (DeFi wallet required)  
**Minimum Deposit:** $250 USDT (BEP-20) per Vault  

**Supported DEX Venues (Routing + Execution):**
- PancakeSwap (PCS)
- BiSwap

**Whitelisted Vault Assets (BEP-20):**
- BTCB
- ETH
- SOL
- XRP
- BNB
- USDT

**Protocol Execution Style:**
- Continuous trading (guardrail-limited)
- Spread capture + PCS↔BiSwap arbitrage (when profitable after costs)

**Profit Options:**
- Withdraw profits anytime (USDT) from realized profit balance
- Or elect LOOP rewards (monthly calendar distribution)

**Compounding Options:**
- Compound All
- Compound 50% / Withdraw 50%
- Withdraw All (no compounding)

**Fee Model (Realized Profit Only):**
- Profit paid in USDT: 15% fee
- Profit paid in LOOP: 10% fee

**Fee Routing Split:**
- 25% Dev/Ops/Bills/Upkeep/Maintenance
- 10% Marketing/Ads/Onboarding/Partnerships
- 55% Ratcheting Stability Reserve System
- 10% LoopLabs

---

## Table of Contents (Main Headings)

0. Disclaimer (Read First)  
1. Executive Summary  
2. Overview (What YieldLoop Is)  
3. Core Principles (Non-Negotiables)  
4. Supported Network, Assets, and DEX Venues  
5. Vault Model (Isolation + Minimum Deposit Rules)  
6. User Controls and Configuration  
7. Trading Engines and Execution Flow  
8. Strategy Logic (Arbitrage + Profit Capture Rules)  
9. Guardrails, Safety Systems, and Failsafes  
10. Profit Settlement, Compounding, and Withdrawals  
11. LOOP Rewards System (Monthly Distribution + Claim Rules)  
12. Fee Model and Fee Routing  
13. Ratcheting Stability Reserve System (“Ratchet Engine”)  
14. Vault Lifecycle State Machine  
15. Governance / Admin Controls (v1 Scope)  
16. Security Model and Risk Disclosures  
17. Roadmap / Future Expansions  
18. Glossary

---

## 0. Disclaimer (Read First)

YieldLoop is a self-custody DeFi protocol deployed on BNB Chain. It is not a bank, broker-dealer, investment fund, financial advisor, or guaranteed-yield product.

YieldLoop does not promise or guarantee:
- profits,
- stability of returns,
- preservation of capital,
- a rising token price,
- a price floor,
- or any future value of LOOP.

All usage of YieldLoop is at the user’s sole risk. Users acknowledge and accept that they may lose part or all of their deposited principal due to market conditions, execution risks, or smart contract risks.

### 0.1 Non-Custodial Use
YieldLoop is non-custodial. Users interact using their own DeFi wallet and authorize all transactions on-chain. YieldLoop does not take custody of user private keys, and it cannot reverse or undo transactions.

### 0.2 Trading Risk
YieldLoop executes trading logic that may result in profit or loss. Market volatility, liquidity shifts, slippage, DEX price impact, and rapid price movements can cause losses. Past performance (if shown) does not predict future results.

### 0.3 Blockchain / Smart Contract Risk
Blockchain systems carry inherent risks including:
- smart contract vulnerabilities,
- oracle failures,
- MEV (front-running, sandwich attacks, back-running),
- DEX liquidity failures,
- network outages or congestion,
- unexpected gas spikes,
- protocol or dependency upgrades.

Audits, testing, and conservative guardrails may reduce risk but cannot eliminate risk.

### 0.4 LOOP Token Risk
LOOP is an optional reward payout asset. Users may elect to receive profits in LOOP on a monthly distribution schedule. LOOP value is market-driven and may fluctuate significantly. YieldLoop does not guarantee that LOOP will retain value, rise in value, or remain liquid.

### 0.5 Fees and System Routing
YieldLoop charges performance fees only on realized profits. Fees are routed to operational categories and system reserve mechanisms as defined in this document. Users acknowledge that these allocations do not create ownership rights, dividends, or guaranteed returns.

### 0.6 No Tax Advice
YieldLoop does not provide tax guidance. Users are responsible for tracking transactions and reporting as required by their jurisdiction.

### 0.7 Acceptance
By depositing into YieldLoop and activating a vault, users confirm they:
- understand the risks,
- accept the protocol rules,
- approve the chosen configuration,
- and accept full responsibility for outcomes.

---

## 1. Executive Summary

YieldLoop is an automated vault-based trading protocol built on **BNB Chain**. Each user deposits **a minimum of $250 USDT** into an **isolated vault** and selects a controlled allocation across a whitelist of major assets. The system routes trades across **PancakeSwap (PCS)** and **BiSwap**, seeking profitable execution through conservative spread capture and cross-venue arbitrage when available.

YieldLoop is designed around three primary goals:

1) **Sustainable Profitability**
   - YieldLoop does not chase hype strategies.
   - It enforces strict trade-quality gates to prevent churn trading and fee/gas bleed.
   - Trades are only executed when the expected net outcome clears the required threshold after fees and costs.

2) **Deterministic Accounting**
   - YieldLoop separates principal, active positions, realized profit, and withdrawable profit.
   - Profit is only recognized when a position closes.
   - No “fake yield” smoothing is used.

3) **System Resilience**
   - A large portion of protocol fees are routed into a **Ratcheting Stability Reserve System**.
   - The reserve is intended to strengthen system continuity, stability, and long-term durability.
   - The reserve does not guarantee token price outcomes or user profits.

### 1.1 Key System Facts (v1.2)

- **Network:** BNB Chain (BSC)
- **Custody:** Self-custody only (wallet required)
- **Minimum Deposit:** $250 USDT (BEP-20) per vault
- **DEX Venues:** PancakeSwap (PCS), BiSwap
- **Whitelisted Assets:**
  - BTCB
  - ETH
  - SOL
  - XRP
  - BNB
  - USDT

### 1.2 User Controls

Users configure each vault by selecting:
- asset weight distribution (must total 100%)
- compounding option:
  - Compound All
  - Compound 50% / Withdraw 50%
  - Withdraw All
- profit payout method:
  - **USDT** (15% performance fee on realized profit)
  - **LOOP** (10% performance fee on realized profit; distributed monthly)

### 1.3 Profit and Withdrawal Model

- YieldLoop operates continuously (bounded by guardrails).
- Profits are produced only when trades close.
- Realized profits can be withdrawn at any time in USDT (if USDT payout is selected).
- If LOOP payout is selected:
  - realized profits are held in suspense
  - LOOP rewards are calculated and distributed monthly (calendar month)
  - LOOP becomes claimable after monthly settlement

### 1.4 Fee Model (Performance Fee Only)

Fees are charged only on realized profits:
- **USDT payout:** 15% of realized profit
- **LOOP payout:** 10% of realized profit

Fee routing allocation:
- 25% Dev/Ops/Bills/Upkeep/Maintenance
- 10% Marketing/Ads/Onboarding/Partnerships
- 55% Ratcheting Stability Reserve System
- 10% LoopLabs

YieldLoop does not charge fees on principal deposits. Users always maintain control of their vault and may close out at their discretion, subject to settlement logic and execution constraints.

---

## 2. Overview (What YieldLoop Is)

YieldLoop is a vault-based automated trading protocol designed for users who want exposure to conservative crypto trading without manually managing trades.

The protocol operates using:
- **isolated user vaults**
- **a limited token whitelist**
- **controlled execution engines**
- **strict guardrails**
- **deterministic settlement rules**
- **optional monthly LOOP rewards**

YieldLoop is not an exchange and not a pooled fund. It is an on-chain automation system where users authorize their own vault configuration and allow the protocol to execute within tightly defined limits.

---

### 2.1 What YieldLoop Does

When a user creates and activates a vault, YieldLoop:
1) accepts the user’s USDT deposit (minimum $250)
2) allocates capital according to user-selected weights
3) executes trades via PCS and BiSwap where profitable
4) realizes profits only when positions close
5) routes profits according to the user’s compounding and payout settings

YieldLoop attempts to generate profit from:
- price dislocations between PCS and BiSwap
- conservative volatility cycles
- spread opportunities that clear gas, slippage, and fee thresholds

YieldLoop avoids “fake yield” designs. There is no APY promise, no smoothing, and no hidden leverage.

---

### 2.2 What YieldLoop Is NOT

To avoid confusion, YieldLoop is NOT:
- a savings account
- a stablecoin bank
- a guaranteed yield product
- a hedge fund
- a broker
- a managed custodial account
- a leveraged trading system
- a perpetual futures platform

YieldLoop is also not designed for gambling tokens, meme coins, or ultra-high-risk pairs. Asset scope is limited intentionally.

---

### 2.3 Why YieldLoop Uses Vault Isolation

Vault isolation is one of the most important design choices.

It ensures:
- each user’s accounting is separate
- fees and profits are calculated deterministically per vault
- users can withdraw profits without affecting other vaults
- strategy behavior can be constrained per vault
- the system avoids pooled-fund dynamics that reduce transparency

Vault isolation also supports:
- clean user performance reporting
- clean monthly LOOP settlement
- safer failure handling during abnormal markets

---

### 2.4 YieldLoop’s Core Advantage

YieldLoop’s advantage is not hype. It is structure.

The protocol is designed to survive long-term by enforcing:
- trade-quality gating
- conservative execution
- strict slippage and liquidity controls
- dynamic safety shutdown logic
- a large reserve allocation (ratchet reserve)

This gives YieldLoop a realistic path to becoming a durable protocol rather than a short-lived DeFi yield experiment.

---

### 2.5 YieldLoop Operating Loop (High-Level)

Each vault runs the same repeating loop:

1) **Assess Market Conditions**
2) **Select Execution Type**
   - spread capture
   - PCS↔BiSwap arbitrage
   - conservative asset rotation (where allowed)
3) **Validate Guardrails**
   - slippage, price impact, liquidity, volatility, gas, drawdown
4) **Execute Trade**
5) **Settle Outcome**
   - if position closes → realized profit occurs
6) **Route Profit**
   - fee
   - compound
   - withdrawable profit or suspense profit (for LOOP)
7) **Repeat**

This loop runs continuously but is bound by strict safety mechanisms and trade-quality gates.

---

## 3. Core Principles (Non-Negotiables)

YieldLoop is built around rules that are intentionally conservative. These principles are not optional design opinions — they are the foundation that makes the system survivable, auditable, and buildable.

---

### 3.1 Self-Custody Only

YieldLoop is **self-custody by design**.

- Users connect their own DeFi wallet.
- Users authorize deposits and withdrawals on-chain.
- YieldLoop does not custody private keys or hold user funds in custodial accounts.
- YieldLoop does not provide password-based “accounts” in v1.

This reduces:
- regulatory load,
- central failure points,
- fraud risk,
- and user trust issues.

---

### 3.2 Vault Isolation (No Pooled Fund Behavior)

Each deposit is treated as its own isolated vault instance.

Vault isolation ensures:
- each vault has its own accounting
- profits and fees are calculated per vault
- one user cannot dilute or harm another user
- performance reporting is clean and attributable

YieldLoop is not a pooled trading fund and does not merge user deposits into a single capital pool.

---

### 3.3 Profit is Only Real When Positions Close

YieldLoop does not use vague “unrealized APY” reporting.

Rules:
- Profit only exists if a position closes
- Unrealized market value changes do not count as profit
- Fees apply only to realized profit

This avoids:
- fake yield math
- manipulated metrics
- user confusion
- accounting disputes

---

### 3.4 Trade-Quality Gate (No Churn Trading)

Churn kills DeFi systems.

YieldLoop executes trades only when the expected outcome is strong enough to justify:
- fees
- slippage
- gas
- price impact
- reserve contribution
- risk buffer

YieldLoop’s core execution rule is simple:

> If the trade edge is not big enough, the system does not trade.

This is enforced by the **Trade Quality Gate Engine**, which exists to prevent:
- over-trading
- micro-profit grinding
- death by gas and fees

---

### 3.5 Guardrails Always Override Strategy

YieldLoop does not “chase profit.”

Every strategy action is subordinate to safety controls.

Guardrails include:
- slippage limits
- price impact limits
- liquidity thresholds
- volatility breakers
- drawdown limits
- gas controls
- cooldowns / throttling
- emergency pause logic

If a guardrail triggers:
- the trade is blocked, reduced, or postponed
- or the vault enters a defensive mode

---

### 3.6 Token Scope is Limited on Purpose

YieldLoop only supports a small whitelist:

- BTCB
- ETH
- SOL
- XRP
- BNB
- USDT

This is not because YieldLoop “can’t” support more.

It is because token sprawl causes:
- exploit surface expansion
- liquidity traps
- bad routing
- rug exposure
- reputational risk

Token limitation is a safety feature.

---

### 3.7 Deterministic Settlement (No Discretionary Games)

YieldLoop must always be able to answer:

- What happened?
- Why did it happen?
- What did the vault hold before and after?
- What profit was realized?
- What fee was charged?
- What was compounded vs withdrawable?
- What portion is held in suspense?

Settlement rules are deterministic and transparent, meaning:
- vault balance movements are measurable
- fees are auditable
- monthly LOOP rewards are calculable

No discretionary “trust me” mechanics.

---

### 3.8 Sustainability > Hype

YieldLoop is not designed for:
- extreme APY claims
- high-leverage gimmicks
- emissions-driven pumps

YieldLoop is designed to survive.

That means:
- conservative defaults
- limited risk options
- strict execution rules
- large reserve allocation
- and ongoing safety-first evolution

---

## 4. Supported Network, Assets, and DEX Venues

YieldLoop v1.2 is intentionally restricted. This is not a limitation — it is a safety design choice.

The system is only as strong as its weakest:
- pool
- token
- router path
- oracle reference
- or execution environment

YieldLoop only supports infrastructure and assets that can sustain repeated execution without collapsing under slippage, liquidity drains, or manipulation.

---

### 4.1 Supported Network

YieldLoop operates exclusively on:

- **BNB Chain (BSC)**

All contracts, vaults, routing, and settlement logic are designed around BNB Chain’s:
- liquidity environment
- transaction cost profile
- DEX ecosystem
- execution latency

No multi-chain routing exists in v1.2.

---

### 4.2 Supported Trading Venues (DEX)

YieldLoop routes and executes trades across the following DEX venues:

- **PancakeSwap (PCS)**
- **BiSwap**

YieldLoop uses these venues because they provide:
- deep liquidity for major pairs
- established routing reliability
- on-chain execution transparency
- compatible token pair coverage

No centralized exchanges (CEX) are used.

---

### 4.3 Supported Token Universe (Whitelist)

YieldLoop vault allocations are limited to the following BEP-20 assets:

- **BTCB**
- **ETH**
- **SOL**
- **XRP**
- **BNB**
- **USDT**

These tokens were selected for:
- relatively high liquidity
- broad market participation
- reduced rug risk
- established routing paths
- practical settlement reliability

YieldLoop does not support:
- low-liquidity pairs
- newly launched tokens
- meme tokens
- reflective / rebasing tokens
- transfer-tax tokens
- tokens with blacklist/whitelist restrictions
- “anti-whale” style tokens
- tokens with broken decimals or abnormal transfer mechanics

---

### 4.4 Quote and Pricing Model

YieldLoop trade routing requires price awareness to compare PCS vs BiSwap.

The routing model uses:
- direct on-chain quote outputs
- slippage estimates
- pool liquidity depth
- expected price impact

YieldLoop does not assume that “best quoted output” is best execution.

It also evaluates:
- probability of negative execution due to MEV
- expected gas overhead
- execution stability in current chain conditions

---

### 4.5 Why YieldLoop Does Not Support Additional Tokens in v1

Adding tokens increases system fragility.

Each new token increases:
- DEX routing complexity
- volatility tail risk
- liquidity failure probability
- oracle disagreement probability
- exploit surface area
- guardrail complexity
- user confusion

YieldLoop will only expand token scope after:
- long-term live execution proof
- measured risk performance
- strong reserve accumulation
- controlled governance processes

Until then, v1.2 remains intentionally narrow and conservative.

---

## 5. Vault Model (Isolation + Minimum Deposit Rules)

YieldLoop operates using isolated user vaults. This vault model is the backbone of correct accounting, profit settlement, and safe withdrawals.

A vault is not a UI concept. It is an enforceable on-chain accounting container with deterministic rules.

---

### 5.1 Minimum Deposit Requirement

Each YieldLoop vault requires:

- **Minimum deposit: $250 USDT (BEP-20)**

Deposits below the threshold are rejected.

This minimum exists to ensure the strategy has enough capital to:
- clear gas cost overhead
- survive slippage variance
- avoid micro-profit churn behavior
- remain economically executable across varying chain conditions

---

### 5.2 Vault Isolation

Each vault is isolated by design:

- Vault balances are not pooled with other vaults
- Vault accounting is deterministic per user vault
- Fees and profit are calculated per vault
- Guardrails evaluate vault-level risk and behavior

This structure ensures:
- clean attribution of profit and performance
- fairness between users
- minimized risk of cross-user contagion
- simplified audits and forensic traceability

---

### 5.3 Vault Ownership

A vault is always bound to the wallet address that created it.

Core properties:
- the user wallet is the vault controller
- profits and withdrawals route only to the vault owner
- YieldLoop cannot redirect vault assets to arbitrary addresses
- vault behavior is constrained to the user’s configured rules and protocol guardrails

---

### 5.4 Vault Components (Ledger Separation)

Each vault must track balances in separate internal buckets:

1) **Principal Balance**
   - original deposited principal remaining
   - excludes realized profit

2) **Active Position Balance**
   - capital currently deployed into open positions

3) **Unrealized Position Value**
   - current market value of open positions
   - does not count as profit

4) **Realized Profit**
   - profit confirmed only upon position close

5) **Withdrawable Profit Balance**
   - portion of realized profit available for immediate withdrawal (USDT path)

6) **Suspense Profit Balance**
   - realized profit held for monthly LOOP distribution (LOOP path)

This separation prevents:
- profit mislabeling
- double counting
- withdrawal exploits
- reward disputes
- false performance reporting

---

### 5.5 Multiple Vaults

YieldLoop supports multiple vaults per user by allowing multiple deposit instances.

Each vault remains:
- independent
- separately configured
- separately settled
- separately reported

YieldLoop v1.2 does not implement “sub-vault baskets” (goal-based compartments) inside a vault. The system supports multiple vaults instead of multiple baskets.

---

### 5.6 Vault Closeout (Principal + Profit)

A user may request closeout of a vault at any time.

Closeout requires:
- unwind/settlement of open positions
- completion of final profit settlement
- routing of remaining principal + realized profits to the user wallet

YieldLoop supports “withdraw profits anytime” only for **realized profits**. Principal withdrawal requires vault closeout or unwind logic.

---

### 5.7 Vault Integrity Rules (Anti-Abuse)

To prevent exploits or accounting corruption, the protocol enforces:

- vault actions must be sequential and state-safe
- no overlapping settlement routines
- anti-reentrancy protections
- withdrawals cannot exceed withdrawable profit ledger
- fee routing and profit settlement must occur before profits are exposed as withdrawable
- suspense profit cannot be withdrawn as USDT while LOOP payout mode is active for the month

These are foundational constraints required for safe execution.

---

## 6. User Controls and Configuration

YieldLoop is automated, but it is not uncontrolled.

Every vault requires explicit configuration chosen by the user, and all execution remains bounded by both:
- user configuration, and
- non-negotiable protocol guardrails

YieldLoop is designed so the user always understands what is being run and why.

---

### 6.1 Configuration Required at Vault Creation

When a user deposits the minimum $250 USDT and creates a vault, they must select:

1) **Asset allocation weights**
2) **Compounding preference**
3) **Profit payout method (USDT or LOOP)**

The vault cannot enter ACTIVE execution until these are completed.

---

### 6.2 Asset Allocation Weights

Users choose weight distribution across the supported whitelist:

- BTCB
- ETH
- SOL
- XRP
- BNB
- USDT

Rules:
- weights must sum to **100%**
- allocations outside the whitelist are rejected
- YieldLoop enforces allocation constraints during execution

YieldLoop uses this allocation as:
- target exposure guidance
- trading boundary constraints
- rebalance reference where applicable

YieldLoop does not allow allocations that contradict safety controls.

---

### 6.3 Compounding Preference

The user selects how realized profit is handled:

#### Option A — Compound All
- 100% of realized profit (after fees) is reinvested into active vault capital

#### Option B — Compound 50% / Withdraw 50%
- 50% of realized profit (after fees) reinvested
- 50% credited to withdrawable profit (or suspense profit for LOOP)

#### Option C — Withdraw All (No Compounding)
- 100% of realized profit (after fees) credited to withdrawable profit (or suspense profit for LOOP)

Compounding applies only to realized profit. Principal is not “compounded.”

---

### 6.4 Profit Payout Method (USDT vs LOOP)

Users select how profits are paid out:

#### Option A — USDT Profit Payout
- realized profits become withdrawable in USDT
- user may withdraw profits at any time
- performance fee: **15% of realized profit**

#### Option B — LOOP Profit Payout
- realized profits are held in suspense
- LOOP is distributed monthly based on realized closed profits
- performance fee: **10% of realized profit**

This option is designed to align users with the protocol’s long-term incentive structure.

---

### 6.5 Configuration Confirmation and Execution Approval

YieldLoop requires user acknowledgement that:
- the system executes trades automatically once active
- profits are not guaranteed
- guardrails may pause trades
- risk exists including loss of capital
- fee model is understood

Vault execution begins only after confirmation.

---

### 6.6 Configuration Changes

YieldLoop allows the user to modify configuration, but changes are restricted:

- changes apply only to new strategy execution cycles
- changes cannot retroactively modify already executed trades
- changes may require a cooldown period for execution integrity

This prevents users from exploiting strategy mechanics mid-cycle or causing execution inconsistencies.

---

### 6.7 User Visibility and Reporting (Required UX Outputs)

Each vault must display at minimum:

- vault value (current)
- principal remaining
- realized profit (lifetime + month)
- withdrawable profit balance
- suspense profit balance (if LOOP)
- fees paid (lifetime + month)
- current allocations
- vault status (ACTIVE / PAUSED / CLOSE REQUESTED / CLOSED)

YieldLoop is designed to be explainable. If the system cannot show what it is doing, it is not acceptable for production.

---

## 7. Trading Engines and Execution Flow

YieldLoop executes trades through a set of deterministic engines. These engines are responsible for safe market interaction, best execution routing, and correct settlement. The protocol is designed to trade only when conditions are favorable and when the expected net outcome clears strict thresholds.

YieldLoop is not a high-frequency trading bot. It is a conservative execution machine built to survive.

---

### 7.1 Engine Architecture (High-Level)

YieldLoop execution is composed of the following engines:

1) **Vault Controller Engine**
2) **Market Data + Quote Engine**
3) **Routing + Best Execution Engine**
4) **Strategy Selection Engine**
5) **Trade Quality Gate Engine**
6) **Execution Engine**
7) **Risk Governor Engine**
8) **Settlement + Ledger Engine**
9) **Fee Routing Engine**
10) **Profit Router Engine**
11) **Monthly LOOP Rewards Engine**
12) **Fail-Safe + Pause Engine**

Each engine has strict responsibilities. No engine may override protocol guardrails.

---

### 7.2 Vault Controller Engine

Purpose:
- Maintains vault configuration and current state
- Enforces allowed actions by vault status
- Coordinates calls into other engines

Inputs:
- vault owner address
- vault configuration (weights, compound, payout method)
- vault balances and ledger buckets
- vault state

Outputs:
- validated execution requests
- state transitions (ACTIVE → PAUSED → CLOSE_REQUESTED → CLOSED)

---

### 7.3 Market Data + Quote Engine

Purpose:
- Obtains trade quotes and expected outputs
- Estimates slippage and price impact
- Provides trade feasibility data to routing and strategy engines

Primary sources:
- PCS router quote
- BiSwap router quote

Outputs:
- quoted output per venue
- expected slippage range
- expected price impact range
- pool liquidity checks

This engine does not decide trades. It provides validated inputs.

---

### 7.4 Routing + Best Execution Engine

Purpose:
- Chooses PCS or BiSwap per trade based on best expected net execution

Decision factors include:
- expected output amount
- slippage estimate
- price impact estimate
- pool depth
- execution reliability (liquidity + routing stability)
- expected MEV exposure (heuristic)
- gas overhead assumptions

Outputs:
- venue selection (PCS or BiSwap)
- route path (pair path)
- max slippage permitted

Routing is constrained by strict venue allowlists and token allowlists.

---

### 7.5 Strategy Selection Engine

Purpose:
- Determines which conservative opportunity type is eligible at the moment

Allowed strategy types in v1.2 include:
- **Spread Capture / Volatility Cycle Capture**
- **PCS↔BiSwap Arbitrage**
- **Controlled Rotation / Rebalance (only within user-selected whitelist weights)**

This engine is not allowed to:
- use leverage
- use borrowing / lending
- short assets
- use perpetuals or derivatives

---

### 7.6 Trade Quality Gate Engine (Critical)

This engine is the survival layer.

Purpose:
- Prevents low-edge churn trading
- Ensures every trade clears profitability hurdles after all known costs

Rule:
A trade may only execute if expected net outcome clears the threshold:

**Expected Net Profit ≥ Minimum Required Net Profit**

Minimum Required Net Profit must include:
- gas buffer
- slippage buffer
- fee impact (15% USDT payout or 10% LOOP payout)
- reserve/risk buffer
- execution uncertainty margin

Trades that fail this gate are rejected with no action taken.

---

### 7.7 Execution Engine

Purpose:
- Executes on-chain swaps according to routing engine output
- Applies max slippage protection
- Ensures token allowlist enforcement at execution time
- Ensures correct transfer + accounting hooks

Execution constraints:
- max slippage enforced
- route allowlist enforced
- liquidity checks enforced
- no “unsafe override” execution allowed in normal operation

---

### 7.8 Risk Governor Engine

Purpose:
- Continuously evaluates risk conditions before and after execution
- Blocks or pauses trading during unsafe periods

Risk Governor monitors:
- volatility thresholds
- liquidity deterioration
- repeated failed swaps
- abnormal slippage outcomes
- drawdown events
- gas spikes
- market dislocations

Actions:
- throttle trade frequency
- reduce exposure / shift posture
- pause execution
- force defensive behavior

---

### 7.9 Settlement + Ledger Engine

Purpose:
- Computes realized profit on trade closure
- Updates vault ledger buckets deterministically
- Ensures realized profit is never double-counted
- Ensures no withdrawal eligibility until ledger finalization

Outputs:
- realized profit value (if any)
- updated balances (principal / positions / profit)
- settlement logs for indexing + UI

Key rule:
Profit only exists upon trade closure. Unrealized gains are not treated as profit.

---

### 7.10 Fee Routing Engine

Purpose:
- Deducts performance fee from realized profit only
- Routes fee allocations according to protocol splits

Fee rules:
- USDT payout mode → 15% of realized profit
- LOOP payout mode → 10% of realized profit

Fee split routing:
- 25% Dev/Ops/Bills/Upkeep
- 10% Marketing/Onboarding/Ads/Partnerships
- 55% Ratcheting Stability Reserve System
- 10% LoopLabs

---

### 7.11 Profit Router Engine (Compound + Withdrawable)

Purpose:
After fees are routed, remaining realized profit is handled per user setting:

- **Compound All** → reinvest 100%
- **Compound 50 / Withdraw 50** → reinvest 50%, credit 50%
- **Withdraw All** → credit 100%

If payout method is:
- **USDT** → credit to Withdrawable Profit Balance
- **LOOP** → credit to Suspense Profit Balance (monthly settlement)

---

### 7.12 Monthly LOOP Rewards Engine

Purpose:
- Computes monthly reward amounts for LOOP payout vaults
- Converts suspense profit (realized closed profit) into claimable LOOP
- Enforces calendar month cutoff rules

Core characteristics:
- monthly distribution schedule (calendar month)
- claim-based delivery (user claims to wallet)
- deterministic mapping between realized profit and reward amount

LOOP rewards are never calculated from unrealized profit.

---

### 7.13 Fail-Safe + Pause Engine

Purpose:
- Immediate defensive shutdown logic when safety conditions trigger

Triggers may include:
- repeated execution failures
- extreme volatility events
- oracle/quote disagreement beyond threshold
- abnormal slippage events
- DEX instability
- chain disruption events

Actions:
- pause vault
- stop new trade openings
- allow safe settlement/unwind paths
- block unsafe withdrawals or state transitions

YieldLoop prioritizes vault integrity and user protection above continued trading.

---

## 8. Strategy Logic (Arbitrage + Profit Capture Rules)

YieldLoop v1.2 uses conservative strategies designed to exploit realistic market inefficiencies, not fantasies.

The system focuses on:
- spread opportunities that actually clear fees and costs
- controlled rotation behavior based on vault configuration
- predictable settlement outcomes

No leverage. No derivatives. No borrowing. No games.

---

### 8.1 Strategy Types Supported (v1.2)

YieldLoop may execute the following categories of trades:

1) **PCS↔BiSwap Arbitrage**
2) **Spread Capture / Volatility Cycle Capture**
3) **Controlled Rotation / Rebalance (within vault allocations)**

Each strategy is subject to guardrails and the Trade Quality Gate.

---

### 8.2 PCS↔BiSwap Arbitrage

#### 8.2.1 What it is
Arbitrage occurs when the same asset pair has a temporary price dislocation between PCS and BiSwap.

Example structure:
- Buy Token A cheaper on DEX 1
- Sell Token A higher on DEX 2
- Capture net spread after execution costs

#### 8.2.2 When it is allowed
Arbitrage may only execute if:
- both venues have sufficient liquidity
- slippage remains under caps
- expected price impact remains under caps
- the Trade Quality Gate approves net expectancy after:
  - gas
  - slippage buffer
  - fee overhead
  - reserve buffer
  - execution uncertainty margin

#### 8.2.3 Practical constraint
Most “arb” opportunities visible in public quotes are fake once execution is attempted.

YieldLoop assumes arbitrage is rare and opportunistic.

It will not:
- chase thin spreads
- spam transactions
- accept marginal edge

If the edge is not clearly profitable after all costs, YieldLoop does nothing.

---

### 8.3 Spread Capture / Volatility Cycle Capture

#### 8.3.1 What it is
YieldLoop may capture profit from natural volatility cycles.

This includes:
- buying on controlled pullbacks
- selling into controlled strength
- closing positions only when profit clears required thresholds

This is not day trading with emotions.
This is threshold-based automated execution.

#### 8.3.2 Why it works (when done correctly)
Most major assets oscillate.
YieldLoop attempts to capture part of those oscillations through:
- entry thresholds
- exit thresholds
- safety caps
- conservative trade frequency

#### 8.3.3 What stops it from bleeding
The Trade Quality Gate prevents:
- trading for crumbs
- closing trades too early
- burning profit on fees and gas

If volatility exists but cannot produce net profit after costs, YieldLoop stays idle.

---

### 8.4 Controlled Rotation / Rebalance Logic

#### 8.4.1 Purpose
The user’s allocation weights serve as the allowed exposure map.

YieldLoop may rotate within the whitelist to:
- increase USDT posture during unsafe conditions
- reduce volatile exposure during drawdown
- return toward target weights during stable conditions

#### 8.4.2 Constraints
Rotation is constrained by:
- user-set weights
- protocol guardrails
- volatility thresholds
- drawdown posture rules

YieldLoop does not “free-roam” into risk assets beyond the whitelist.

---

### 8.5 Strategy Defaults (Conservative by Design)

YieldLoop defaults to conservative behavior:
- low trade frequency
- high minimum net profit requirements
- strict slippage caps
- strict liquidity minimums
- cooldown periods between actions

This is intentional.

YieldLoop is designed to survive and scale.
A vault that trades too much is not a vault — it’s a fee-burning machine.

---

### 8.6 What YieldLoop Will Not Do

YieldLoop v1.2 will not:
- execute leveraged strategies
- use borrowed capital
- lend user funds
- farm yield farms
- provide “APY” marketing
- chase meme coins
- trade illiquid pools
- execute strategies that depend on constant emissions

If it can’t survive without hype, it doesn’t belong in v1.

---

## 9. Guardrails, Safety Systems, and Failsafes

YieldLoop is only credible if it can survive adverse conditions.

Most DeFi systems fail because they either:
- trade too often,
- trade into bad liquidity,
- ignore volatility,
- ignore gas conditions,
- ignore MEV exposure,
- or keep trading when the system should stop.

YieldLoop is designed with non-negotiable guardrails that override strategy selection.

---

### 9.1 Safety Philosophy

YieldLoop follows one simple rule:

> Survival beats performance.

The system is built to:
- avoid catastrophic losses,
- prevent churn trading,
- preserve vault integrity,
- enforce deterministic settlement,
- and pause when conditions are unsafe.

YieldLoop would rather miss a profit than take a loss it didn’t need to take.

---

### 9.2 Trade Quality Gate (Primary Safety Layer)

The Trade Quality Gate is the single most important safety engine.

A trade is only allowed if:

**Expected Net Profit ≥ Minimum Required Net Profit**

Minimum required profit includes:
- slippage buffer
- gas buffer
- fee overhead
- reserve contribution expectation
- execution uncertainty margin

If the opportunity does not clearly clear all costs:
- the trade is rejected
- the vault remains idle

This prevents:
- micro-profit churn
- death-by-fees
- “busy trading” with no expectancy

---

### 9.3 Slippage Controls

YieldLoop enforces strict slippage rules.

Trades must specify:
- max allowable slippage
- revert if slippage exceeds threshold

Slippage thresholds are conservative by default.

Trades that cannot be executed safely under slippage limits are not executed.

---

### 9.4 Price Impact Controls

Even if a trade quote looks profitable, price impact can destroy it.

YieldLoop enforces:
- max price impact thresholds per trade

Trades exceeding price impact caps are rejected.

---

### 9.5 Liquidity Threshold Controls

YieldLoop refuses to trade into thin pools.

Liquidity checks include:
- minimum pool depth
- minimum liquidity thresholds
- routing path verification

This reduces exposure to:
- liquidity traps
- manipulated pools
- sudden pool drains

---

### 9.6 Volatility Breakers

YieldLoop pauses or shifts posture under extreme volatility.

Volatility breaker actions may include:
- stop opening new volatile positions
- rotate temporarily toward USDT posture
- enforce cooldown windows
- pause vault execution entirely if required

Volatility breaker exists because:
volatility is where slippage and MEV become lethal.

---

### 9.7 Drawdown Protection

YieldLoop implements drawdown controls at the vault level.

Two layers:

#### 9.7.1 Soft Drawdown Mode
If drawdown exceeds a defined threshold, the vault:
- reduces trade frequency
- tightens risk constraints
- increases quality thresholds
- shifts posture toward stability (USDT tilt)

#### 9.7.2 Hard Drawdown Mode
If drawdown exceeds the hard threshold:
- vault pauses execution
- no new positions open
- system enters defensive posture until conditions stabilize or user closes

YieldLoop does not “revenge trade.”

---

### 9.8 Trade Throttles and Cooldowns

YieldLoop enforces throttling to prevent overtrading.

Controls include:
- max trades per time window
- cooldown time between trades
- max open positions per vault

This is essential to prevent:
- strategy churn
- chaotic loops
- overexposure to MEV and poor fills

---

### 9.9 Gas Controls

Gas conditions matter.

YieldLoop refuses to trade when:
- gas costs spike beyond acceptable bounds
- net profitability cannot clear gas + safety buffers

This prevents:
- burning profits on network conditions
- trading during congestion events

---

### 9.10 Execution Failure Handling

YieldLoop expects that trades may fail sometimes due to:
- pool shifts
- quote staleness
- liquidity changes
- MEV disruption
- gas volatility

If repeated failures occur:
- vault throttles
- routing reevaluates
- system may pause

Repeated failures are treated as an unsafe condition, not as a reason to retry endlessly.

---

### 9.11 Emergency Pause / Kill Switch

YieldLoop includes a protocol-level emergency pause mechanism.

Triggers may include:
- DEX instability
- oracle/quote disagreement beyond limits
- chain disruption
- abnormal exploit patterns
- catastrophic market event

Emergency pause actions:
- stop new trade execution
- preserve vault ledger integrity
- allow safe withdrawal paths where possible
- prevent state corruption

Emergency pause exists because on-chain systems must be able to stop when reality breaks.

---

### 9.12 Summary

YieldLoop’s guardrails are designed to ensure:

- the protocol does not trade when it shouldn’t
- user vaults are not churned into dust
- the system pauses when conditions are unsafe
- survivability remains the priority

YieldLoop does not claim perfection.
It claims discipline.

---

## 10. Profit Settlement, Compounding, and Withdrawals

YieldLoop’s profitability is not the hard part.

Correct settlement is.

Most systems fail because they blur:
- principal vs profit,
- realized vs unrealized,
- compounded vs withdrawable,
- and fee routing vs user balances.

YieldLoop explicitly separates these categories and enforces deterministic settlement rules to prevent accounting disputes and withdrawal exploits.

---

### 10.1 Realized Profit Only

YieldLoop recognizes profit only when:

> A position closes.

Unrealized value changes are not treated as profit.

This prevents:
- fake APY reporting
- fee mischarges
- reward inflation
- disputes over “paper gains”

---

### 10.2 Settlement Event (Close Position → Settle)

When a strategy closes a position, the protocol performs settlement in a strict sequence:

1) Compute realized profit
2) Deduct protocol performance fee (profit fee)
3) Route fee split allocations
4) Determine net profit remainder
5) Apply compound setting
6) Route profit remainder into:
   - compounded balance (reinvested), and/or
   - withdrawable profit (USDT payout), or
   - suspense profit (LOOP payout)

No profit becomes withdrawable until this sequence completes.

---

### 10.3 Profit Fee Application (Performance Fee Only)

YieldLoop charges fees only on realized profit:

- If payout method is **USDT** → **15%** fee on realized profit
- If payout method is **LOOP** → **10%** fee on realized profit

No fee is charged on principal deposit amounts.

Fees are never charged on unrealized profit.

---

### 10.4 Fee Routing Split (Deterministic)

All performance fees are allocated:

- **25%** — Dev/Ops/Bills/Upkeep/Maintenance
- **10%** — Marketing/Ads/Onboarding/Partnerships
- **55%** — Ratcheting Stability Reserve System
- **10%** — LoopLabs

Fee routing must be:
- transparent
- auditable on-chain
- enforced by protocol logic

---

### 10.5 Compounding Rules

After fees are deducted, the remaining net profit is handled per the vault’s compounding setting:

#### Option A — Compound All
- 100% of net profit is reinvested into vault active capital

#### Option B — Compound 50 / Withdraw 50
- 50% reinvested into vault active capital
- 50% credited to profit payout routing

#### Option C — Withdraw All (No Compounding)
- 100% credited to profit payout routing

Compounding is applied to profits only. Principal is separate.

---

### 10.6 Profit Payout Routing

After compounding is applied, the remaining profit (if any) is routed based on payout selection:

#### USDT Payout Mode
- credited to **Withdrawable Profit Balance**
- user can withdraw at any time (subject to vault integrity rules)

#### LOOP Payout Mode
- credited to **Suspense Profit Balance**
- not immediately withdrawable as USDT
- processed at month-end for LOOP distribution
- becomes claimable as LOOP after monthly settlement

---

### 10.7 Withdrawals (Profit Withdrawal Anytime)

YieldLoop supports “withdraw profits anytime,” but strictly under these rules:

- only **realized profit** can be withdrawn
- withdrawal amount cannot exceed Withdrawable Profit Balance
- withdrawal executes in USDT (BEP-20)
- withdrawals require user wallet authorization
- system enforces anti-reentrancy and state-safe withdrawal sequencing

Profit withdrawals do not require closing the vault.

---

### 10.8 Principal Withdrawal (Vault Closeout)

Principal is not freely withdrawable without settlement.

To withdraw principal, the user must:
- request vault closeout (CLOSE_REQUESTED)
- allow the system to unwind open positions
- finalize settlement
- receive remaining principal + remaining realized profit balances

YieldLoop does not support withdrawing principal while positions remain open, because that creates:
- forced liquidation behavior
- inconsistent accounting
- user harm
- protocol fragility

---

### 10.9 Withdrawal Integrity Rules

YieldLoop enforces strict integrity constraints:

- withdrawals cannot occur during settlement execution
- suspense profit cannot be withdrawn as USDT under LOOP payout mode
- withdrawals must not interrupt state transitions
- all withdrawal actions must be sequential and verified

These rules exist to prevent accounting corruption and exploit behavior.

---

### 10.10 Summary

YieldLoop settlement is built around three requirements:

1) profit is only real when closed
2) fees are only charged on realized profit
3) profits are segregated into:
   - compounded profit
   - withdrawable profit (USDT)
   - suspense profit (LOOP monthly)

This structure makes YieldLoop auditable, fair, and survivable.

---

## 11. LOOP Rewards System (Monthly Distribution + Claim Rules)

LOOP is an optional profit payout asset. It is designed to reward users who align with the long-term protocol incentive structure rather than extracting profits immediately in USDT.

LOOP rewards are not based on hype.
They are based on closed-profit performance and are issued under deterministic monthly rules.

---

### 11.1 What LOOP Is

LOOP is a protocol reward asset that may be chosen as a vault’s profit payout method.

Key characteristics:
- optional payout selection (not mandatory)
- tied to realized vault performance
- distributed on a monthly calendar schedule
- claimable by the user to their wallet

LOOP is not:
- a guaranteed return product
- a dividend
- a share of YieldLoop
- a promise of price increases

LOOP value is market-driven and can rise or fall.

---

### 11.2 Why LOOP Exists

LOOP exists to create:
- long-term alignment with protocol stability
- reduced short-term sell pressure from reward cycles
- a smoother distribution cadence (monthly)
- improved sustainability relative to continuous emissions models

LOOP also supports:
- incentive alignment with the Ratcheting Stability Reserve System
- stronger long-term token integrity vs inflationary reward designs

---

### 11.3 LOOP Payout Mode (How It Works)

When a vault is configured for LOOP payout:

1) trades execute normally under guardrails
2) realized profit is computed only upon position close
3) performance fee is deducted (10% rate in LOOP mode)
4) remaining profit is routed according to compound settings
5) the profit intended for payout is not paid instantly
6) instead it is credited into:
   - **Suspense Profit Balance**

Suspense Profit represents closed profit approved for monthly LOOP settlement.

---

### 11.4 Monthly Settlement Window (Calendar Month)

LOOP rewards are calculated monthly using a calendar month cutoff.

At month-end:
- all suspense profit accumulated from closed trades within that month is finalized
- the finalized amount becomes eligible for LOOP reward processing

Monthly settlement exists to:
- reduce reward emission chaos
- reduce exploit surface from rapid claim cycles
- simplify accounting and reporting
- ensure reward issuance is based on confirmed results

---

### 11.5 LOOP Reward Calculation (Deterministic)

LOOP issuance is based on:

- realized closed profits attributed to the vault during the month
- the vault’s compounding preference
- application of the LOOP-mode performance fee (10%)
- protocol-defined conversion/issuance rules

LOOP rewards are never calculated from:
- unrealized gains
- open position value
- estimated profit
- speculative “APY”

---

### 11.6 Claim Process

After month-end settlement:

- claimable LOOP becomes visible in the vault dashboard
- the user may claim LOOP at any time (subject to protocol-defined claim window and gas costs)
- claiming delivers LOOP to the user wallet

YieldLoop does not auto-send LOOP in v1.2.
Rewards are claim-based to:
- reduce wasteful spam distribution
- allow users to claim when desired
- keep execution cost contained

---

### 11.7 Unclaimed Rewards

If the user does not claim LOOP immediately:
- rewards remain claimable under protocol rules
- unclaimed LOOP is not treated as forfeited unless explicitly defined by governance rules

YieldLoop is designed to avoid punitive mechanics.

---

### 11.8 Safety and Integrity Rules

LOOP reward rules include:

- rewards cannot exceed validated suspense profit amounts
- suspense profit is ledger-separated from withdrawable USDT profit
- reward calculations are deterministic and auditable
- claim cannot occur during settlement execution
- rewards cannot be “double claimed”

---

### 11.9 Summary

LOOP rewards are designed to be:
- performance-based (closed profit only)
- monthly (calendar schedule)
- deterministic (auditable accounting)
- optional (user selects payout mode)

This structure avoids the failure pattern of most DeFi reward tokens:
constant emissions, constant dumping, eventual collapse.

---

## 12. Fee Model and Fee Routing

YieldLoop is designed for sustainability. The fee system funds operations, growth, and long-term protocol resilience without requiring inflationary token emissions.

YieldLoop charges fees only on realized profits.
No fee is charged on deposits or principal.

---

### 12.1 Fee Type

YieldLoop uses a single fee type:

- **Performance Fee (Realized Profit Fee)**

This fee applies only when:
- a trade closes, and
- realized profit is confirmed

If no profit is realized:
- no fee is charged

---

### 12.2 Performance Fee Rates (USDT vs LOOP)

YieldLoop supports two payout modes with different fee rates:

#### USDT Payout Mode
- **15%** of realized profit

#### LOOP Payout Mode
- **10%** of realized profit

The reduced fee for LOOP payout exists because LOOP payout:
- delays extraction pressure
- aligns users with protocol incentives
- supports token utility adoption
- reduces constant sell behavior

---

### 12.3 Fee Calculation Timing

Performance fees are deducted only when:

> a position closes and realized profit is confirmed.

Fees are not charged on:
- deposits
- principal
- unrealized value changes

Fee charging before settlement is not permitted.

---

### 12.4 Fee Routing Split (v1.2)

All collected performance fees are split and routed into four categories:

- **25% — Dev/Ops/Bills/Upkeep/Maintenance**
- **10% — Marketing/Ads/Onboarding/Partnerships**
- **55% — Ratcheting Stability Reserve System**
- **10% — LoopLabs**

This split is:
- deterministic
- auditable on-chain
- enforced by protocol logic

---

### 12.5 Purpose of Each Allocation

#### 12.5.1 Dev/Ops (25%)
Supports ongoing protocol execution and continuity including:
- contract maintenance and upgrade work (where applicable)
- infrastructure support
- monitoring systems
- security tools
- admin utilities
- operational overhead

YieldLoop assumes continuous operation requires continuous funding.

#### 12.5.2 Marketing / Growth (10%)
Supports measured growth including:
- onboarding funnels
- advertising
- partnerships
- education campaigns
- brand expansion

YieldLoop does not rely on “hype emission farming.”
Growth must be paid for.

#### 12.5.3 Ratcheting Stability Reserve (55%)
The majority of fee allocation is routed into reserve mechanisms designed to strengthen:
- protocol resilience
- continuity
- stability capacity
- system overcapitalization

This reserve does not guarantee token price outcomes.

#### 12.5.4 LoopLabs (10%)
LoopLabs funding is intended for:
- experiments and innovation
- future strategy R&D (non-v1)
- auxiliary ecosystem projects approved under protocol rules

LoopLabs exists as a structured innovation budget rather than random expansion.

---

### 12.6 Fee Integrity Requirements

Fee logic must be implemented with strict integrity:

- fees cannot be bypassed by timing or trade behavior
- fees cannot be charged without realized profit
- fees must route before profit becomes withdrawable
- all routing must emit on-chain logs for auditing and indexing

---

### 12.7 Summary

YieldLoop uses a simple, sustainable fee structure:

- fee only on realized profit
- discounted fee when profits are taken in LOOP
- deterministic routing split
- reserve-first sustainability design

The fee system is designed to keep YieldLoop alive long-term without needing inflationary token emissions or deceptive marketing.

---

## 13. Ratcheting Stability Reserve System (“Ratchet Engine”)

YieldLoop is designed to survive long-term. That requires a system reserve that grows with protocol usage.

The Ratchet Engine exists to:
- strengthen protocol resilience,
- increase overcapitalization capacity,
- support continuity during disruption,
- and reduce fragility that destroys most DeFi systems.

YieldLoop does not use the Ratchet Engine to promise token price gains.
It exists as a stability mechanism, not a profit guarantee.

---

### 13.1 Reserve Funding Source

The Ratchet Engine is funded automatically through fee routing.

Fee allocation:
- **55% of all performance fees** are routed into the Ratcheting Stability Reserve System.

This means reserve growth is:
- performance-driven
- adoption-driven
- sustainability-driven

No reserve funding is pulled from user principal.

---

### 13.2 Reserve Objectives

The Stability Reserve is designed to support:

1) **Protocol Continuity**
   - ability to maintain infrastructure and operations
   - ability to survive market downturns

2) **System Resilience**
   - ability to absorb shocks and disruptions
   - reduced likelihood of cascading failure events

3) **Overcapitalization Capacity**
   - increasing system backing capacity over time
   - strengthening long-term system credibility

4) **Stability Actions (Restricted)**
   - permitted actions must be rule-based and constrained
   - no discretionary price manipulation mechanics

---

### 13.3 What the Reserve May Be Used For

The reserve may be used only under protocol-defined rules such as:

- **Execution Continuity**
  - emergency operational requirements
  - critical infrastructure coverage during disruptions

- **Risk Event Absorption**
  - extreme market dislocation mitigation
  - MEV-driven disruption recovery (where applicable)
  - abnormal slippage event recovery (where applicable)

- **Redemption / Liquidity Support Mechanisms** (if implemented)
  - stability liquidity support
  - protocol-controlled liquidity operations (restricted)

All reserve actions must be:
- logged
- auditable
- rule-based

---

### 13.4 What the Reserve Will NOT Do

YieldLoop does not guarantee:
- price floors
- token appreciation
- “ratchet always up”
- profit protection
- loss reimbursement

The reserve is not:
- insurance
- a backstop promise
- a user guarantee
- a refund pool

YieldLoop avoids these claims by design because they are:
- factually unsafe,
- economically unsustainable,
- and legally risky.

---

### 13.5 Reserve Control Philosophy (Rules Over Vibes)

The Ratchet Engine must be implemented as:
- deterministic actions,
- bounded by thresholds,
- and governed by defined parameters.

Reserve usage should follow “modes” such as:

- **Build Mode**
  - reserve accumulates only
  - no aggressive actions

- **Defensive Mode**
  - reserve prioritizes system survival actions
  - restricts any optional activities

- **Stability Mode**
  - reserve may perform permitted stability actions under strict rules
  - no discretionary behavior

The default posture is always conservative.

---

### 13.6 Reserve Transparency

The system reserve must be visible and auditable.

YieldLoop must display at minimum:
- total reserve balance
- reserve inflows (fee routing)
- reserve outflows (if any)
- reserve usage category tags

Users must be able to verify reserve behavior on-chain.

---

### 13.7 Summary

The Ratchet Engine is a sustainability mechanism funded by protocol performance fees.

- It strengthens protocol durability.
- It supports continuity during disruption.
- It improves long-term resilience.
- It does not promise profit or token price outcomes.

YieldLoop survives by building reserves, not by selling dreams.

---

## 14. Vault Lifecycle State Machine

YieldLoop vaults operate under a deterministic lifecycle. This prevents overlapping actions, protects accounting integrity, and ensures users always know what state their vault is in.

The vault lifecycle state machine is mandatory for safe operation.

---

### 14.1 Why a State Machine Exists

Without state enforcement, vault logic becomes exploitable.

Examples of what breaks systems:
- withdrawing during settlement
- executing a trade while closeout is running
- charging fees twice
- crediting profit twice
- claiming LOOP while month-end settlement is active
- overlapping trade executions

The state machine ensures:
- actions occur in valid order
- vault accounting stays correct
- user outcomes remain auditable

---

### 14.2 Vault States (v1.2)

YieldLoop vaults use the following minimum states:

1) **FUNDED**
2) **CONFIGURED**
3) **ACTIVE**
4) **PAUSED**
5) **CLOSE_REQUESTED**
6) **CLOSED**

---

### 14.3 State Definitions

#### 14.3.1 FUNDED
Meaning:
- deposit received
- vault exists on-chain
- configuration not finalized

Allowed actions:
- set allocations
- set compound preference
- set payout method
- cancel before activation (optional, if supported)

Blocked actions:
- trading
- claiming rewards
- withdrawing profit (no profit exists yet)

---

#### 14.3.2 CONFIGURED
Meaning:
- user completed required settings
- vault is ready for activation

Allowed actions:
- user confirms start
- activation transitions to ACTIVE

Blocked actions:
- trade execution until activation occurs

---

#### 14.3.3 ACTIVE
Meaning:
- vault is executing trades continuously (bounded by guardrails)
- profit settlement is enabled

Allowed actions:
- protocol trades under guardrails
- user profit withdrawal (USDT payout mode only, realized profit only)
- configuration change request (restricted rules apply)
- request pause
- request closeout

Blocked actions:
- withdrawing principal while open positions exist

---

#### 14.3.4 PAUSED
Meaning:
- execution is halted due to:
  - user pause request, or
  - safety trigger activation

Allowed actions:
- user may resume (if safe)
- user may request closeout
- user may withdraw realized profit (if USDT payout mode)

Blocked actions:
- new trade openings

Notes:
- PAUSED exists to prevent continued trading under unsafe conditions.

---

#### 14.3.5 CLOSE_REQUESTED
Meaning:
- user requested principal withdrawal and vault closure
- the system must unwind open positions safely

Allowed actions:
- unwind execution only
- settlement routines
- final profit accounting
- fee routing

Blocked actions:
- opening new positions
- changing vault configuration
- claiming LOOP during closeout settlement window

The vault remains in CLOSE_REQUESTED until:
- open positions are unwound
- final settlement is complete

---

#### 14.3.6 CLOSED
Meaning:
- vault finalized
- remaining balances distributed
- vault no longer trades

Allowed actions:
- view historical performance
- claim remaining LOOP rewards if eligible (per protocol rules)

Blocked actions:
- reactivation
- trading

---

### 14.4 Allowed State Transitions

Valid transitions:

- FUNDED → CONFIGURED  
- CONFIGURED → ACTIVE  
- ACTIVE → PAUSED  
- PAUSED → ACTIVE  
- ACTIVE → CLOSE_REQUESTED  
- PAUSED → CLOSE_REQUESTED  
- CLOSE_REQUESTED → CLOSED  

Any other transition is invalid and must be rejected.

---

### 14.5 State Integrity Constraints (Non-Negotiable)

The protocol must enforce:

- no simultaneous trade execution and closeout execution
- no withdrawals during active settlement routines
- no claim operations during month-end settlement routines
- no double-finalization of positions
- all state changes must emit deterministic logs

---

### 14.6 Summary

The vault lifecycle state machine ensures:
- safe sequencing
- correct settlement
- reliable withdrawals
- clean reward distribution
- reduced exploit surface

This structure is required for YieldLoop to operate as a real protocol rather than a fragile trading script.


---

## 15. Governance / Admin Controls (v1 Scope)

YieldLoop v1.2 is built to be deterministic and rule-driven. Governance and admin controls exist only to support:
- system safety,
- parameter updates,
- and continuity.

They do not exist to provide profit guarantees, discretionary trading behavior, or private control over user funds.

---

### 15.1 Governance Philosophy

YieldLoop governance must follow one principle:

> Users should not have to “trust the team” for the protocol to behave correctly.

The system must remain:
- auditable,
- parameter-driven,
- and rule-bound.

Governance is meant to manage:
- guardrail parameters,
- safety thresholds,
- approved venues,
- and protocol upgrades.

---

### 15.2 Admin Control Boundaries

YieldLoop admin controls must be restricted.

Admins must NOT be able to:
- withdraw user funds
- redirect vault assets
- override profit accounting
- override fee routing
- fabricate rewards
- change vault settings without user authorization
- force user trades outside protocol constraints

If admin controls allow these actions, YieldLoop becomes a custody product, which is not the v1 design.

---

### 15.3 Allowed Admin Actions (v1)

YieldLoop allows admin controls only for:

#### 15.3.1 Parameter Updates
Admin/governance may update:
- slippage caps
- price impact caps
- liquidity thresholds
- volatility thresholds
- max trade frequency thresholds
- cooldown windows
- drawdown soft/hard thresholds
- routing allowlists (PCS/BiSwap)

All changes must be:
- logged
- delayed with a timelock (recommended)
- reviewable by users before effective

#### 15.3.2 Emergency Pause
Admins may trigger:
- protocol-wide pause
- venue-specific pause (PCS only / BiSwap only)
- token-specific pause (if abnormal behavior occurs)

Emergency pause exists as a safety requirement.

#### 15.3.3 Upgrade Controls (If Upgradeable Contracts Are Used)
If YieldLoop uses upgradeable contracts, upgrades must be:
- timelocked
- transparently disclosed
- gated by governance procedures
- restricted by upgrade scopes

If non-upgradeable contracts are used:
- upgrades require redeployment and migration path design

---

### 15.4 Guardrail Governance (Non-Optional Discipline)

Guardrails exist to protect:
- vault integrity
- user outcomes
- protocol reputation

Governance cannot arbitrarily weaken guardrails for “performance.”

If guardrails are weakened:
- the system shifts toward gambling
- user trust collapses
- token integrity collapses
- and the protocol becomes short-lived

YieldLoop is designed to avoid that failure mode.

---

### 15.5 Governance Does Not Imply Profit Rights

Governance controls do not create:
- dividends
- profit-sharing rights
- ownership of YieldLoop
- claim rights on reserve balances

Governance exists for system management, not for distributing profits.

---

### 15.6 Transparency Requirements

Governance/admin actions must emit logs showing:
- what was changed
- previous value → new value
- effective timestamp
- reason tag (if applicable)

Users should be able to audit governance actions on-chain.

---

### 15.7 Summary

YieldLoop v1.2 governance/admin controls exist to:
- keep the system safe
- update parameters responsibly
- manage upgrades and emergencies

They must remain tightly constrained to prevent YieldLoop from becoming a discretionary custody product or a trust-based operation.

---

## 16. Security Model and Risk Disclosures

YieldLoop is designed to reduce risk through conservative design, strict guardrails, and deterministic settlement. However, risk cannot be eliminated in DeFi.

This section exists to state the truth clearly:

> Users can lose money.

YieldLoop does not hide that reality.

---

### 16.1 Smart Contract Risk

All smart contracts may contain vulnerabilities.

Risks include:
- coding errors
- logic flaws
- unexpected edge cases
- integration failures with external DEX routers
- reentrancy attacks
- allowance-related issues

Audits and testing can reduce risk but cannot guarantee safety.

Users acknowledge:
- using YieldLoop means accepting smart contract risk.

---

### 16.2 DEX Risk (PCS / BiSwap)

YieldLoop executes through decentralized exchanges which may fail or be attacked.

DEX-related risks include:
- liquidity drains
- manipulated pool pricing
- broken routing due to pool changes
- temporary instability and abnormal spreads
- malicious tokens (mitigated by whitelist restrictions)

YieldLoop reduces these risks by:
- strict liquidity minimums
- strict price impact caps
- strict slippage controls
- token allowlisting

But DEX risk remains.

---

### 16.3 Market Risk

YieldLoop trades real markets.

Risks include:
- sudden crashes
- violent pump cycles
- abnormal volatility
- liquidity collapse during panic events
- correlation spikes (everything dropping together)

YieldLoop cannot guarantee:
- preservation of capital
- profitable outcomes

Guardrails reduce exposure but cannot stop market reality.

---

### 16.4 MEV Risk

MEV is unavoidable on public blockchains.

MEV risks include:
- sandwich attacks
- frontrunning
- backrunning
- toxic routing behavior

YieldLoop reduces MEV exposure via:
- conservative execution logic
- slippage limits
- trade-quality gating
- throttles and cooldown windows

However, MEV cannot be eliminated entirely.

---

### 16.5 Oracle / Quote Risk

YieldLoop relies on on-chain quote outputs for execution routing.

Quote risk includes:
- quote staleness
- router discrepancies
- sudden pool state changes
- exploitation of predictable routing paths

YieldLoop mitigates by:
- strict execution revert logic
- slippage caps
- multi-venue comparisons
- abnormal quote disagreement triggers

---

### 16.6 Blockchain Risk (BNB Chain)

BNB Chain risks include:
- network congestion
- high gas spikes
- temporary outage events
- chain reorgs
- RPC instability

YieldLoop may pause execution during chain disruption to protect vault integrity.

---

### 16.7 Operational Risk

Even non-custodial systems require operations.

Operational risks include:
- monitoring failures
- delayed detection of abnormal events
- misconfigured parameters
- UI/UX reporting bugs

YieldLoop reduces this risk through:
- deterministic logging
- strict guardrail enforcement
- transparent on-chain activity

---

### 16.8 User Risk (Wallet / Key Security)

Users are responsible for:
- wallet private key security
- avoiding phishing
- correct transaction approval
- managing device security

If the user loses keys:
- YieldLoop cannot recover funds.

---

### 16.9 LOOP Token Risk

LOOP is optional.

Risks include:
- price volatility
- liquidity shifts
- market-driven price decline
- inability to exit at expected value

YieldLoop does not guarantee LOOP price outcomes.

Users selecting LOOP payout accept:
- token price risk
- claim timing risk
- market liquidity risk

---

### 16.10 Summary

YieldLoop is designed to be:
- conservative
- rule-bound
- survivable

But it cannot remove:
- smart contract risk
- market risk
- MEV risk
- blockchain risk

Users choose to participate voluntarily and accept these risks knowingly.

---

## 17. Roadmap / Future Expansions

YieldLoop v1.2 is intentionally narrow and conservative. It exists to prove the core system:
- isolated vault trading
- deterministic settlement
- profitable execution under strict guardrails
- sustainable fee routing
- reserve-first stability structure
- optional monthly LOOP rewards

Future expansions will only be added after the core system proves itself live.

YieldLoop does not expand by hype.
It expands by verified stability.

---

### 17.1 v1.2 (Current Release Scope)

Included features:
- BNB Chain deployment
- $250 USDT minimum deposit per vault
- isolated vault accounting
- whitelisted assets:
  - BTCB, ETH, SOL, XRP, BNB, USDT
- PCS + BiSwap routing
- conservative strategy execution:
  - spread capture
  - PCS↔BiSwap arbitrage (when profitable)
- strict guardrails and safety pause logic
- realized profit settlement
- withdraw profits anytime (USDT payout vaults)
- LOOP payout option:
  - monthly calendar settlement
  - claim-based LOOP distribution
- fee routing split:
  - 25% Dev/Ops
  - 10% Marketing
  - 55% Ratchet Reserve
  - 10% LoopLabs

v1.2 does not include:
- fiat onboarding/offboarding
- custodial accounts
- lending
- yield farming
- leverage
- derivatives
- token expansion beyond whitelist

---

### 17.2 v2 (Possible Additions After Proven Stability)

Potential upgrades may include:

#### 17.2.1 Expanded Reporting and Analytics
- advanced vault analytics dashboard
- trade history export tools
- deeper performance breakdowns
- simplified “explain this trade” output
- tax tooling integrations (optional)

#### 17.2.2 Additional Whitelisted Assets
Expansion may occur only when:
- liquidity is deep
- pool integrity is consistent
- routing reliability is proven
- risk modeling supports it

YieldLoop will not support:
- meme coins
- illiquid microcaps
- reflective / tax tokens
- tokens with abnormal transfer restrictions

#### 17.2.3 Additional Strategies (Conservative)
Possible strategies:
- improved rotation/rebalance logic
- improved trend filters
- improved volatility gates
- more sophisticated spread/cycle capture logic

Any added strategy must remain:
- conservative
- guardrail-bound
- auditable

---

### 17.3 v3 (High Complexity Features — Optional)

These features are considered long-term and higher risk:

#### 17.3.1 Fiat On-Ramp / Off-Ramp (“Normie Rail”)
YieldLoop may later support:
- Transak
- MoonPay
- other rails

This would require:
- UX redesign
- compliance risk evaluation
- increased customer support capacity

This is intentionally excluded from v1.2.

#### 17.3.2 Multi-Chain Support
Future chain support is possible but not included in v1.2.

#### 17.3.3 Additional Vault UX Features
Possible enhancements:
- multiple vault templates
- goal-based vault names and categories
- automated savings routing
- scheduled deposits

YieldLoop will prioritize survivability over feature bloat.

---

### 17.4 Expansion Policy

YieldLoop expansion is governed by the following rules:

- add nothing that increases fragility unnecessarily
- prove stability before expanding scope
- keep the system simple enough to audit
- prioritize guardrails and user protection
- never add features that require deception to market

---

### 17.5 Summary

YieldLoop will expand cautiously and only after v1.2 proves:
- stable execution,
- sustainable profitability,
- safe settlement,
- and strong reserve accumulation.

This is how real protocols survive.

---

## 18. Glossary

This glossary defines the key terms used throughout the YieldLoop v1.2 whitepaper.

---

### ACTIVE (Vault State)
A vault state where YieldLoop trading execution is enabled and allowed to operate under guardrails.

---

### Allocation Weights
The user-defined percentage distribution across the whitelist assets:
BTCB, ETH, SOL, XRP, BNB, USDT.

Weights must sum to 100%.

---

### Arbitrage (PCS↔BiSwap)
A trade strategy that attempts to capture price differences between PancakeSwap and BiSwap for the same asset pair, only when profitable after all costs.

---

### Auditability
The ability for users and third parties to verify system actions through on-chain logs and deterministic accounting.

---

### BEP-20
Token standard used on BNB Chain (similar to ERC-20 on Ethereum).

---

### BiSwap
A supported decentralized exchange (DEX) on BNB Chain used as a trading venue by YieldLoop.

---

### CLOSED (Vault State)
A vault state where trading is permanently stopped and final settlement has occurred.

---

### CLOSE_REQUESTED (Vault State)
A vault state triggered when the user initiates vault closure. The system unwinds open positions, performs final settlement, and prepares vault for closure.

---

### Compound / Compounding
The reinvestment of realized profits back into vault active trading capital. Compounding applies only to profit, not to principal.

---

### Cooldown
A forced waiting period between execution actions used to reduce churn trading and exposure to unfavorable market conditions.

---

### Custody / Custodial
Holding funds on behalf of users through controlled accounts. YieldLoop v1.2 is non-custodial (self-custody).

---

### DEX (Decentralized Exchange)
A blockchain-based exchange (e.g., PCS, BiSwap) where tokens can be swapped on-chain through liquidity pools.

---

### Drawdown
A decline in vault value relative to a reference peak or baseline. Drawdown thresholds trigger defensive behavior or vault pausing.

---

### Execution Engine
The component responsible for executing approved trades on-chain.

---

### Fee Routing
The deterministic allocation of fees to protocol categories:
Dev/Ops, Marketing, Ratchet Reserve, LoopLabs.

---

### FUNDED (Vault State)
A vault state indicating a deposit has been made and the vault exists, but it is not yet configured and active.

---

### Guardrails
Hard safety controls that limit execution behavior regardless of strategy. Examples include slippage caps, liquidity minimums, drawdown rules, and volatility breakers.

---

### LOOP
YieldLoop’s optional profit payout token. LOOP rewards are distributed monthly to vaults that select LOOP payout mode.

---

### LOOP Payout Mode
A vault configuration where realized profits are routed into suspense and converted/distributed as LOOP monthly. LOOP payout uses a reduced performance fee (10%).

---

### LoopLabs
A fee allocation category (10% of fees) intended for protocol innovation, testing, and future ecosystem projects.

---

### MEV (Maximal Extractable Value)
On-chain exploitation behavior such as frontrunning, sandwich attacks, and backrunning.

---

### Minimum Deposit
The minimum required deposit amount to create a vault:
$250 USDT (BEP-20) in v1.2.

---

### Monthly Settlement
A calendar-month process where suspense profit is finalized and LOOP rewards become claimable.

---

### PancakeSwap (PCS)
A supported decentralized exchange (DEX) on BNB Chain used as a trading venue by YieldLoop.

---

### Performance Fee
The fee YieldLoop charges on realized profits only:
- USDT payout: 15%
- LOOP payout: 10%

---

### Principal
The original deposited amount (excluding realized profits).

---

### Profit Router Engine
Engine responsible for allocating net realized profit according to:
- compounding selection, and
- payout selection (USDT vs LOOP).

---

### Realized Profit
Profit confirmed only when positions close. Unrealized value changes are not treated as profit.

---

### Ratchet Engine / Ratcheting Stability Reserve System
A reserve mechanism funded by 55% of performance fees, designed to strengthen system resilience and stability capacity. Does not guarantee price outcomes.

---

### Routing Engine
The engine that selects the best trading venue and route between PCS and BiSwap based on expected net execution.

---

### Self-Custody
A model where users control their own private keys and wallet. YieldLoop v1.2 is self-custody only.

---

### Slippage
The difference between expected and executed price in a swap, caused by market movement or insufficient liquidity.

---

### Suspense Profit Balance
A ledger bucket used in LOOP payout mode to hold realized profits until the monthly LOOP settlement occurs.

---

### Trade Quality Gate
The key safety mechanism that blocks trades unless expected net outcome clears gas, slippage, fee, and safety buffers.

---

### USDT Payout Mode
A vault configuration where realized profits become withdrawable in USDT. USDT payout uses a 15% performance fee.

---

### Vault
An isolated accounting container tied to a single deposit instance and vault configuration.

---

### Vault Controller Engine
The engine responsible for enforcing vault state integrity, configuration rules, and execution permissions.

---

### Volatility Breaker
A rule that pauses or restricts trading during extreme volatility to reduce slippage and execution damage.

---

### Withdrawable Profit Balance
A ledger bucket containing realized profits that are available for withdrawal in USDT (for USDT payout mode vaults).

---

