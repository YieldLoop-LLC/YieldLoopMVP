# YieldLoop

## Autonomous AI-Governed Trading Infrastructure  
### Sovereign Per-Chain Deployment Model  

---

**Author:** Todd Koletsky  
HT1 (SW), United States Navy (Ret.)  
Systems Architect  

**Date:** February 21, 2026  
**Version:** 3.0 – Autonomous Control Edition  

---

YieldLoop is a deterministic, AI-directed trading protocol deployed per-chain as sovereign,
self-contained infrastructure.

The system operates under immutable economic invariants, bounded parameter ranges,
automated protection enforcement, and closed asset universes.

There is no governance, no multisig authority, no bridge dependency,
and no human parameter control.

User onboarding supports both:
- Native Web3 wallet deposits
- Embedded wallet creation with fiat on-ramp integration

All control logic is autonomous and constrained by mathematically enforced safety rules.

---

# 1. Executive Summary

YieldLoop is an autonomous, AI-governed trading protocol deployed per blockchain as a sovereign,
self-contained system.

The protocol executes disciplined spot trading and cross-DEX arbitrage across a closed set of
high-liquidity digital assets. All activity is constrained by deterministic liquidity caps,
positive net expectancy requirements, coverage enforcement, and bounded parameter ranges.

There is no human governance, no multisig authority, and no upgrade intervention path capable
of overriding core economic invariants. All control logic is autonomous and enforced on-chain.

YieldLoop operates in fixed monthly cycles. Trading activity occurs throughout the cycle,
and profit is finalized at cycle end. Users may elect to receive profit in:

• USDT (stablecoin claimable balance), or  
• LOOP (internal accounting unit redeemable 1:1+ via the ratchet mechanism)

Performance fees are charged only on realized net profit. No fee is applied in a loss month.

The protocol incorporates:

• Liquidity-aware trade sizing  
• Cross-DEX spread validation  
• Deterministic slippage enforcement  
• Coverage ratio protection thresholds  
• Automatic trading halts under stress conditions  
• A one-way ratchet that may increase redemption value when surplus permits  

LOOP is not a circulating token. It exists only during redemption transactions and is minted
and burned atomically. Redemption is backed by on-chain reserve accounting and governed by
explicit coverage thresholds.

Each YieldLoop deployment is sovereign to its chain. There are no bridges and no shared reserves
between instances. Capital does not move cross-chain.

User onboarding supports:

• Direct Web3 wallet deposits  
• Embedded wallet creation using Privy  
• Fiat on-ramp integration via Transak  

YieldLoop never custodies user private keys and does not handle fiat directly.

The objective of YieldLoop is not to promise yield. The objective is structural durability:
to participate in market activity only when edge exists, to preserve capital when it does not,
and to enforce transparency through immutable economic rules.

Profitability depends on market conditions, liquidity availability, and execution discipline.
The system is designed to reduce fragility—not eliminate risk.

---

# 2. Core Philosophy

YieldLoop is built on a simple premise:

Markets change. Discipline must not.

The protocol is designed around structural durability rather than aggressive expansion.
Every architectural decision prioritizes containment of risk, clarity of accounting,
and resistance to human error.

## 2.1 No Human Governance

YieldLoop contains no discretionary governance layer.

There is:
• No multisig authority  
• No upgrade committee  
• No emergency admin override  
• No parameter manipulation by token holders  

All system behavior is enforced by deterministic rules and bounded parameter ranges.

Human governance introduces political risk, coordination failure, and key compromise
risk. YieldLoop replaces governance with autonomous enforcement.

The system can adapt — but only within predefined, immutable bounds.

---

## 2.2 Closed Asset Universe

YieldLoop trades only a fixed set of high-liquidity assets:

BTC  
ETH  
BNB  
XRP  
SOL  
USDC  
USDT  

The asset universe is not expandable.

This constraint reduces:
• Liquidity fragility  
• Oracle exposure  
• Rug-pull risk  
• Low-depth slippage traps  
• Execution unpredictability  

Edge durability is more important than asset quantity.

---

## 2.3 Sovereign Per-Chain Deployment

Each YieldLoop deployment operates independently per blockchain.

There are:
• No bridges  
• No shared reserves  
• No cross-chain redemption  
• No shared LOOP accounting  

If one deployment fails, others remain unaffected.

Cross-chain contagion is eliminated by design.

---

## 2.4 AI Governance Within Hard Bounds

YieldLoop is AI-directed but not AI-unbounded.

Autonomous control may adjust:

• Trade frequency  
• Edge thresholds  
• Lane activation  
• Risk throttling  
• Ratchet timing (within limits)  

However, it cannot violate:

• Liquidity-aware trade caps  
• Net positive expectancy requirements  
• Coverage ratio enforcement  
• Ratchet non-decrease invariant  
• Monthly cycle boundaries  

Adaptability exists inside structure.  
Structure cannot be overridden.

---

## 2.5 Monthly Cycle Discipline

YieldLoop operates in fixed monthly cycles.

This provides:

• Clear profit finalization  
• Deterministic fee accounting  
• Clean compounding logic  
• Protection enforcement windows  
• Parameter update boundaries  

Mid-cycle mutation increases fragility.  
Cycle discipline reduces chaos.

---

## 2.6 Capital Preservation Over Constant Activity

YieldLoop does not attempt to manufacture yield.

Trading occurs only when:

Expected spread  
− Fees  
− Gas  
− Slippage  
− Safety buffer  
> 0  

If edge does not exist, capital waits.

In a negative month:
• No performance fee is charged  
• Ratchet does not decrease  
• Protection mechanisms may tighten  

Principal risk exists in volatile markets.
Previously realized profits are not clawed back by the system.

---

## 2.7 The Ratchet Philosophy

The ratchet is designed to move slowly and only when coverage surplus permits.

It never decreases.

When surplus exists:
Redemption value may increase incrementally.

When surplus does not exist:
It pauses.

The ratchet is not designed for speed.
It is designed for structural reinforcement.

---

## 2.8 Transparency Over Marketing

YieldLoop is not positioned as a guaranteed yield instrument.

It is a structured trading infrastructure governed by explicit mathematical constraints.

All economic transitions emit on-chain events.
All invariant violations trigger deterministic protection responses.

The objective is durability through clarity.

---

# 3. System Architecture Overview

YieldLoop is a modular smart contract system designed to separate
responsibilities across distinct components.

No single contract controls the full economic state.

Each module has limited authority and explicit interaction boundaries.

The system is deployed per-chain as a sovereign instance.

---

## 3.1 Conceptual Architecture

At a high level, YieldLoop consists of:

User Layer  
→ Vault Accounting Layer  
→ Trading Execution Layer  
→ Profit Finalization Layer  
→ Claim & Redemption Layer  
→ Protection & Enforcement Layer  
→ Autonomous Control Layer  

These layers operate inside fixed monthly cycles.

Economic transitions are deterministic and event-logged.

There is no human intervention layer.

---

### User Layer

Users interact through:

• Web3 wallet (MetaMask, Trust Wallet, etc.)  
• Embedded wallet (via Privy)  
• Fiat on-ramp (via Transak → embedded wallet)

Users:

• Create vaults  
• Deposit USDT (BEP-20)  
• Select asset allocation weights  
• Choose reward mode (USDT or LOOP)  
• Select compounding preference  

Users cannot modify system parameters.

---

### Vault Accounting Layer

Responsible for:

• Vault creation (500 USDT minimum)  
• Subsequent deposits (250 USDT minimum)  
• 50,000 USDT vault cap enforcement  
• Allocation storage  
• Reward mode storage  
• Compounding preference storage  
• Cycle boundary enforcement  
• Parameter queuing  

Vault state does not directly execute trades.

---

### Trading Execution Layer

Executes:

• Spot swaps  
• Cross-DEX arbitrage  
• Liquidity-aware trades  

Enforces:

• Trade size caps (depth-based)  
• Slippage limits  
• Daily turnover limits  
• Net positive expectancy requirement  

Trading respects Protection state at all times.

---

### Profit Finalization Layer

At cycle end:

• Calculates realized net profit  
• Applies performance fee  
• Routes fee allocation  
• Credits USDT claims  
• Credits LOOP entitlements  

If profit ≤ 0:
• No performance fee  
• No rewards distributed  

---

### Claim & Redemption Layer

Users may:

• Claim USDT  
• Claim LOOP entitlement  

LOOP:

• Exists only during redemption  
• Minted and burned atomically  
• Non-transferable  
• Backed by reserve accounting  

Redemption validates coverage ratio before transfer.

---

### Protection & Enforcement Layer

Monitors:

• Coverage ratio  
• Liquidity thresholds  
• Volatility conditions  
• AUM growth shock  
• Trade depth integrity  

Escalation hierarchy:

1. Emit warning  
2. Pause ratchet  
3. Pause LOOP rewards  
4. Halt trading  

Protection enforcement cannot be bypassed.

---

### Autonomous Control Layer

Replaces governance.

The Autonomous Control Layer may adjust:

• Trade frequency  
• Spread thresholds  
• Lane activation  
• Risk multipliers  
• Ratchet timing (within bounds)  

It cannot modify:

• Coverage thresholds  
• Ratchet non-decrease rule  
• Asset universe  
• Vault caps  
• Fee structure  
• Monthly cycle boundaries  

All adjustments apply only at cycle boundaries.

---

## 3.2 Smart Contract Modules

Below is the contract-level structure.

---

### 1. VaultManager

Responsibilities:

• Manage vault creation  
• Enforce deposit minimums  
• Enforce 50,000 USDT vault cap  
• Store allocation weights  
• Store reward mode  
• Store compounding preference  
• Enforce cycle boundaries  
• Queue parameter updates  

VaultManager does not execute trades.

---

### 2. TradingEngine

Responsibilities:

• Execute spot swaps  
• Execute cross-DEX arbitrage  
• Enforce liquidity-aware trade size formula  
• Enforce slippage limits  
• Enforce turnover caps  
• Emit trade execution events  

TradingEngine must respect ProtectionModule state.

---

### 3. RewardsAllocator

Responsibilities:

• Finalize cycle profit  
• Apply 15% USDT fee or 10% LOOP fee  
• Route fee allocation  
• Credit claim balances  
• Emit ProfitFinalized and FeeDistributed events  

No fee is applied if profit ≤ 0.

---

### 4. ClaimPortal

Responsibilities:

• Store claimable USDT  
• Store LOOP entitlement (USDT-denominated)  
• Apply compounding logic  
• Forward redemption requests  

ClaimPortal does not hold trading capital.

---

### 5. RedemptionContract

Responsibilities:

• Validate coverage ratio  
• Validate protection state  
• Mint LOOP  
• Burn LOOP  
• Transfer USDT to redeemer  
• Emit LoopRedeemed event  

Mint and burn occur atomically.

---

### 6. LoopToken (Restricted ERC-20)

Characteristics:

• Non-transferable  
• Mintable only by RedemptionContract  
• Burnable only by RedemptionContract  
• No peer-to-peer transfers  
• No circulating supply  

LOOP exists only momentarily during redemption.

---

### 7. ProtectionModule

Responsibilities:

• Monitor coverage ratio  
• Monitor liquidity depth  
• Monitor volatility  
• Monitor AUM shock  
• Escalate protection stages  
• Pause ratchet  
• Pause rewards  
• Halt trading  

All actions emit reason-coded events.

---

### 8. AutonomousControlModule

Replaces Governance.

Responsibilities:

• Adjust bounded parameters  
• Activate or pause trading lanes  
• Adjust risk multipliers  
• Propose ratchet increase (if eligible)  

Constraints:

• Cannot alter immutable invariants  
• Cannot change asset universe  
• Cannot reduce ratchet  
• Cannot bypass protection  
• Cannot alter fee percentages  
• Cannot change vault cap  

All changes apply at cycle boundaries only.

---

## 3.3 Separation of Concerns

The system enforces strict separation:

• Trading capital ≠ Redemption reserves  
• Redemption logic ≠ Trading logic  
• Autonomous control ≠ Economic invariants  
• NFT logic ≠ Protection enforcement  
• Onboarding ≠ Vault accounting  

No contract has unilateral authority over full system state.

---

YieldLoop’s architecture is designed to reduce attack surface,
improve audit clarity, and enforce deterministic economic behavior.

---

# 4. Immutable Core Invariants

YieldLoop’s durability depends on strict enforcement of economic invariants.

These invariants cannot be modified by the Autonomous Control Module.
They are enforced at the smart contract level and apply to all cycles,
all vaults, and all chain deployments.

If an invariant would be violated, the transaction must revert
or protection logic must escalate.

---

## 4.1 Ratchet Non-Decrease Rule

The Redemption Price (RP) may increase.
It may never decrease.

At all times:

RP_new ≥ RP_old

No contract function exists that can reduce RP.

If eligibility conditions are not satisfied, the ratchet pauses.

---

## 4.2 Coverage Enforcement

Required backing:

Required_Backing = LOOP_Entitlements × RP

Coverage ratio:

Coverage = Redemption_Reserve / Required_Backing

Thresholds:

C_warn = 1.10  
C_min  = 1.05  
C_crit = 1.00  

Enforcement:

If Coverage < C_warn:
• Emit warning event

If Coverage < C_min:
• Pause ratchet
• Pause LOOP rewards

If Coverage < C_crit:
• Halt trading

Coverage enforcement cannot be bypassed.

---

## 4.3 Trade Size Discipline

Each trade must satisfy:

Trade_Size ≤ minimum of:

• 0.02 × D1  
• 0.01 × D2  
• 0.01 × AUM_vault  
• 0.005 × AUM_total  

Where:

D1 ≈ 0.01 × Pool_Reserve  
D2 ≈ 0.02 × Pool_Reserve  

If constraint fails, trade is rejected.

---

## 4.4 Net Positive Expectancy Requirement

A trade executes only if:

Expected_Spread
− DEX_Fees
− Gas_Cost
− Slippage_Estimate
− Safety_Buffer
> 0

If inequality fails, trade execution reverts.

The Autonomous Control Module may adjust Safety_Buffer within bounds,
but cannot remove it.

---

## 4.5 Monthly Cycle Enforcement

YieldLoop operates in fixed monthly cycles.

• Trading begins at cycle start.
• Profit finalizes at cycle end.
• Parameter updates apply only at cycle boundary.
• Mid-cycle deposits wait for next cycle.

Cycle boundaries are immutable.

---

## 4.6 Fee Application Rule

Performance fee applies only if:

Profit_realized > 0

If Profit_realized ≤ 0:
• Fee = 0
• No LOOP entitlement
• No USDT reward

Fee percentages are fixed:

• 15% for USDT reward mode  
• 10% for LOOP reward mode  

Autonomous Control cannot modify these percentages.

---

## 4.7 LOOP Supply Constraint

LOOP:

• Is non-transferable
• Exists only during redemption
• Is minted and burned atomically
• Cannot circulate

At no time does LOOP exist as a tradable asset.

---

## 4.8 Vault Capital Constraint

• Initial deposit: 500 USDT  
• Subsequent deposit: 250 USDT  
• Vault cap: 50,000 USDT  

If vault reaches cap:
• No additional deposits permitted
• Trading continues
• Profits accumulate normally

Vault cap cannot be modified.

---

## 4.9 Asset Universe Lock

The following assets are immutable:

BTC  
ETH  
BNB  
XRP  
SOL  
USDC  
USDT  

No new assets may be added.

Autonomous Control cannot modify asset universe.

---

## 4.10 Protection Escalation Hierarchy

Protection stages are strictly ordered:

1. Warning  
2. Ratchet pause  
3. Reward pause  
4. Trading halt  

Stages may escalate but cannot skip backward without condition restoration.

Protection cannot be disabled.

---

## 4.11 Structural Integrity Condition

At all times:

Redemption_Reserve ≥ 0

If Coverage < 1.00:
• Trading must halt
• No rewards issued
• Ratchet paused

Recovery requires Coverage ≥ minimum threshold.

---

These invariants define the boundaries of system behavior.

Autonomous adaptation operates inside these constraints.
It cannot override them.

--- 

# 5. Autonomous Control Plane (AI Governance Model)

YieldLoop contains no human governance.

There is no multisig.
There is no token-based voting.
There is no timelock-controlled upgrade path.

Instead, YieldLoop operates under an Autonomous Control Plane
that may adjust bounded parameters within predefined ranges.

Autonomy exists inside strict mathematical and structural constraints.

---

## 5.1 Purpose of the Autonomous Control Plane

Markets are dynamic.

Liquidity shifts.
Volatility changes.
Arbitrage spreads compress or expand.
Gas costs fluctuate.

Rigid systems fail when market conditions change.

The Autonomous Control Plane allows adaptation —
without allowing violation of core invariants.

---

## 5.2 Scope of Authority

The Autonomous Control Module MAY adjust:

• Arbitrage spread threshold (within bounds)  
• Safety buffer (within bounds)  
• Trade frequency  
• Daily turnover cap (within bounds)  
• Lane activation or pause  
• Capital staging multiplier  
• Ratchet step size (within cap and eligibility)  

The Autonomous Control Module MAY NOT adjust:

• Asset universe  
• Vault deposit minimums  
• Vault cap  
• Fee percentages  
• Coverage thresholds  
• Ratchet non-decrease rule  
• Monthly cycle boundaries  
• Protection enforcement rules  

Any attempt to exceed bounds must revert.

---

## 5.3 Bounded Parameter Ranges

Each adjustable parameter has:

• Hard minimum  
• Hard maximum  
• Default value  

These bounds are immutable once deployed.

The Autonomous Control Module can select values
only within the predefined interval.

Example:

Safety_Buffer ∈ [X_min, X_max]  
Turnover_Max ∈ [Y_min, Y_max]  
Ratchet_Step ≤ Step_Cap  

Bounds are enforced on-chain.

---

## 5.4 Cycle-Boundary Application

All parameter changes apply only at cycle boundaries.

Mid-cycle mutation is not permitted.

This ensures:

• Predictable behavior  
• Clean accounting  
• No intra-cycle rule changes  
• Protection of user expectations  

---

## 5.5 Lane Activation & Pause

Trading strategies operate in lanes.

Autonomous Control may:

• Activate lane if conditions are favorable  
• Pause lane if edge collapses  
• Re-enable lane when conditions recover  

Lane decisions are logged via event emission.

Paused lanes retain configuration but execute no trades.

---

## 5.6 Ratchet Proposal Logic

The Autonomous Control Module may propose ratchet increases
only if all eligibility criteria are satisfied:

• Coverage ≥ required threshold  
• 30-day profit > 0  
• Reserve surplus ≥ minimum  
• Protection stage not escalated  

Ratchet step:

RP_new = RP_old × (1 + Step)

Where:

Step ≤ Step_Cap

Ratchet may pause.
Ratchet may never decrease.

---

## 5.7 Tighten Fast, Loosen Slowly Principle

The Autonomous Control logic may:

• Tighten risk parameters immediately  
• Loosen parameters only after sustained stability  

This asymmetric authority reduces fragility
during volatile periods.

---

## 5.8 No Fund Movement Authority

The Autonomous Control Module:

• Cannot move funds  
• Cannot transfer reserves  
• Cannot withdraw user capital  
• Cannot bypass redemption validation  
• Cannot modify accounting balances  

Its authority is limited to parameter selection
within bounded intervals.

---

## 5.9 Event Transparency

Every parameter adjustment emits:

• ParameterChanged event  
• Old value  
• New value  
• Effective cycle  

Every lane activation or pause emits:

• LaneStateChanged event  
• Reason code  

Autonomy is transparent.

---

## 5.10 Failure Containment

If the Autonomous Control Module fails:

• Default parameters remain active  
• Trading continues under last valid configuration  
• Protection module remains operational  

Autonomous Control enhances adaptability.
It does not control survival.

---

YieldLoop replaces human governance with bounded autonomy.

Structure remains superior to discretion.

---

# 6. Trading Engine & Liquidity Discipline

YieldLoop’s Trading Engine is designed around a single objective:

Participate only when structural edge exists.
Stand down when it does not.

It does not force activity.
It does not manufacture yield.
It executes only when expected net profitability is positive.

---

## 6.1 Trading Scope

The Trading Engine performs:

• Spot rebalancing trades  
• Cross-DEX arbitrage between PancakeSwap and BiSwap  
• Allocation-aligned position management  

Trading is confined to the closed asset universe:

BTC  
ETH  
BNB  
XRP  
SOL  
USDC  
USDT  

No external assets are touched.

---

## 6.2 Liquidity-Aware Execution

Every trade must respect real-time liquidity depth.

YieldLoop does not assume infinite liquidity.
It does not rely on static pool assumptions.

Trade size is dynamically constrained based on:

• DEX pool reserve depth  
• Vault size  
• Total AUM  

This prevents:

• Self-induced slippage  
• Liquidity exhaustion  
• Edge compression  
• Becoming the liquidity being harvested  

---

## 6.3 Trade Size Constraint (Formal)

For each trade:

Let:

D1 ≈ 1% impact depth  
D2 ≈ 2% impact depth  

Trade_Size must satisfy:

Trade_Size ≤ minimum of:

• 0.02 × D1  
• 0.01 × D2  
• 0.01 × AUM_vault  
• 0.005 × AUM_total  

If any constraint fails, trade is rejected.

This ensures scalability discipline.

---

## 6.4 Net Positive Expectancy Requirement

Before execution, each trade must satisfy:

Expected_Spread  
− DEX_Fees  
− Gas_Cost  
− Slippage_Estimate  
− Safety_Buffer  
> 0  

If inequality fails, execution reverts.

This ensures:

• No speculative trades  
• No edge assumption  
• No “hope-based” execution  

Safety_Buffer exists to absorb latency and volatility noise.

---

## 6.5 Arbitrage Discipline

Arbitrage is executed only when:

• Verified spread exceeds friction  
• Both legs can be executed within depth limits  
• Net after-cost spread remains positive  

Cross-DEX arbitrage is atomic in intent.
Partial execution risk is mitigated through depth constraint and buffer logic.

---

## 6.6 Spot Allocation Management

User allocation weights determine directional bias.

The Trading Engine may:

• Buy and hold assets aligned with allocation  
• Rebalance toward allocation targets  
• Harvest spread opportunities within allocation set  

The engine does not override user allocation.
It optimizes inside it.

---

## 6.7 Turnover & Overtrading Control

Daily turnover is bounded:

Daily_Turnover ≤ Turnover_Max × AUM_vault

Turnover_Max is bounded by Autonomous Control within predefined limits.

This prevents:

• Excessive churn  
• Fee bleed  
• MEV exploitation  
• Gas inefficiency  

---

## 6.8 AUM Shock Constraint

If:

New_Deposits / AUM_total > Growth_Threshold

Then:

Deployable_Capital = 0.5 × New_Deposits

Remaining capital stages into next cycle.

This prevents liquidity shock from large inflows.

---

## 6.9 Lane-Based Execution

Trading strategies operate in lanes.

Each lane may represent:

• Arbitrage  
• Allocation rebalancing  
• Volatility-responsive execution  

Autonomous Control may pause or activate lanes,
but lanes must still obey all trade constraints.

---

## 6.10 No Edge = No Trade

If spreads compress,
if liquidity contracts,
if gas spikes,
if volatility disrupts predictability,

Trading reduces or stops.

The protocol is designed to wait.

Capital preservation outranks constant activity.

---

YieldLoop does not attempt to predict markets.

It enforces discipline when markets offer opportunity,
and restraint when they do not.

---

# 7. Vault Structure & Deposit Rules

YieldLoop organizes user capital into isolated vaults.

Each vault represents:

• A unique deposit account  
• A fixed allocation profile  
• A selected reward mode  
• A compounding preference  
• A capped capital limit  

Vaults operate independently but participate in the same trading infrastructure.

---

## 7.1 Vault Creation

To create a vault:

• Minimum initial deposit: 500 USDT (BEP-20)  
• Deposit must be received before cycle cutoff  
• If deposited mid-cycle, activation begins next cycle  

Vault activation occurs only at cycle start.

---

## 7.2 Subsequent Deposits

After vault creation:

• Minimum additional deposit: 250 USDT  
• Deposits mid-cycle are queued  
• Capital becomes deployable at next cycle start  

This prevents intra-cycle capital distortion.

---

## 7.3 Vault Cap

Each vault has a hard cap:

50,000 USDT total contributions

If cap is reached:

• Additional deposits are rejected  
• Trading continues normally  
• Profits accumulate without restriction  

The cap is immutable.

---

## 7.4 Allocation Selection

Upon vault creation, users must allocate
0–100% of capital across the closed asset universe:

BTC  
ETH  
BNB  
XRP  
SOL  
USDC  
USDT  

Total allocation must equal 100%.

Allocation defines directional bias.
The Trading Engine optimizes execution inside that allocation.

Autonomous Control cannot override allocation weights.

---

## 7.5 Reward Mode Selection

Users must choose one reward mode:

1. USDT Mode  
2. LOOP Mode  

Reward mode determines:

• Performance fee percentage  
• Distribution logic  
• Claim behavior  

Mode may be changed only at cycle boundary.

---

## 7.6 Compounding Preference

Users selecting LOOP Mode must choose:

• Compound 100%  
• Compound 50% / Claim 50%  
• Claim 100%  

For USDT Mode:

• Claimable balance accrues in USDT  
• Compounding reinvests automatically into vault capital  

Compounding adjustments apply at next cycle.

---

## 7.7 Cycle Participation

Vault lifecycle per cycle:

1. Capital deploys at cycle start  
2. Trading executes throughout month  
3. Profit finalized at cycle end  
4. Fees applied  
5. Rewards credited  
6. Compounding applied  
7. Claims unlocked  

Mid-cycle changes do not alter current cycle behavior.

---

## 7.8 Account Closure

Users may request withdrawal at cycle boundary.

Upon closure:

• All principal returned  
• All USDT claims returned  
• LOOP entitlements may be redeemed  

Withdrawal does not disrupt other vaults.

---

## 7.9 System Discipline

Vault structure enforces:

• Capital containment  
• Clean accounting  
• Predictable execution  
• No intra-cycle parameter mutation  
• Controlled AUM scaling  

YieldLoop scales by increasing vault count —
not by concentrating unlimited capital into a single structure.

---

# 8. Asset Universe (Closed Set)

YieldLoop operates within a fixed asset universe.

The following assets are permanently supported:

BTC  
ETH  
BNB  
XRP  
SOL  
USDC  
USDT  

These assets are represented as BEP-20 tokens on BNB Chain.

---

## 8.1 Non-Expandable Universe

The asset universe is immutable.

No governance process exists to add new assets.
The Autonomous Control Module cannot modify this list.
No external proposal mechanism exists.

If expansion is desired in the future,
it requires deployment of a new protocol instance.

---

## 8.2 Rationale for Closure

Restricting the asset universe reduces systemic risk.

Benefits include:

• Deep liquidity concentration  
• Reduced oracle surface area  
• Lower rug-pull probability  
• Predictable volatility characteristics  
• Simplified risk modeling  
• Improved audit clarity  

Edge durability is prioritized over token diversity.

---

## 8.3 Allocation Discipline

User allocation weights must distribute 100% of capital
across this fixed universe.

The Trading Engine may:

• Rebalance inside the universe  
• Execute arbitrage between supported assets  
• Hold positions aligned with allocation  

It may not interact with unsupported assets.

---

## 8.4 Stablecoin Role

USDT and USDC serve as:

• Settlement assets  
• Arbitrage legs  
• Capital preservation buffers  
• Redemption accounting base  

Redemption reserves are denominated in USDT.

---

The closed asset universe is a structural boundary,
not a temporary configuration.

YieldLoop prioritizes depth, discipline,
and survivability over expansion.

---

# 9. Onboarding Architecture (Web3 + Embedded Wallet)

YieldLoop supports two user entry paths:

1. Native Web3 wallet deposits  
2. Embedded wallet + fiat on-ramp  

YieldLoop does not custody private keys.
YieldLoop does not process fiat.
YieldLoop only interacts with on-chain wallet addresses.

---

## 9.1 Web3 Wallet Path (Crypto-Native Users)

Users may connect:

• MetaMask  
• Trust Wallet  
• Any compatible BEP-20 wallet  

Flow:

1. Connect wallet  
2. Approve USDT  
3. Deposit 500 USDT (minimum)  
4. Configure vault  
5. Participate next cycle  

YieldLoop interacts only with the connected address.

Users maintain full custody of private keys.

---

## 9.2 Embedded Wallet Path (Normie Users)

For users without an existing wallet,
YieldLoop integrates embedded wallet infrastructure via:

Privy (wallet provider)

Privy generates a smart wallet controlled by user authentication
(email or supported login method).

YieldLoop does not control Privy accounts.
YieldLoop does not store private keys.

The embedded wallet belongs to the user.

---

## 9.3 Fiat On-Ramp

Fiat conversion is handled via:

Transak (fiat-to-crypto onramp provider)

Flow:

1. User completes identity verification (if required)  
2. User completes card or bank transaction  
3. Transak converts fiat to USDT  
4. USDT is sent directly to user's embedded wallet  

YieldLoop does not:

• Custody fiat  
• Perform KYC  
• Process card payments  
• Interact with banking systems  

YieldLoop only sees USDT once it reaches the wallet address.

---

## 9.4 Deposit Activation

Once USDT is in the wallet:

1. User creates vault  
2. Vault becomes active at next cycle boundary  

Mid-cycle deposits wait for activation.

---

## 9.5 Custody Boundaries

YieldLoop:

• Does not hold user private keys  
• Does not have access to embedded wallet credentials  
• Cannot withdraw user funds without contract interaction  
• Cannot reverse deposits  

Embedded wallet control resides with the user
through the authentication provider.

---

## 9.6 Migration & Self-Custody

YieldLoop does not provide wallet export tooling.

Users who wish to migrate to self-custody
may transfer funds to another wallet
using standard blockchain transaction methods.

Crypto-native users may bypass embedded wallets entirely.

---

## 9.7 Infrastructure Risk Separation

Onboarding risk is external to YieldLoop:

• Wallet provider outage  
• Onramp interruption  
• KYC delays  
• Regional restrictions  

These do not affect on-chain protocol logic.

YieldLoop remains operational regardless of onboarding provider state.

---

The onboarding architecture separates:

Wallet custody  
Fiat conversion  
Trading logic  
Redemption logic  

This separation reduces systemic coupling
and preserves protocol-level integrity.

---

# 10. Profit Handling & Fee Model

YieldLoop charges a performance fee only on realized net profit.

If a cycle produces no profit, no fee is applied.

There are no management fees.
There are no withdrawal penalties.
There are no hidden charges.

---

## 10.1 Profit Finalization

At cycle end:

Profit_realized = End_Value − Start_Value − Net_Deposits

If Profit_realized ≤ 0:

• Fee = 0  
• No rewards distributed  
• Ratchet remains unchanged  

If Profit_realized > 0:

Fee is applied based on reward mode.

---

## 10.2 Performance Fee Rates

Reward Mode determines fee percentage:

• USDT Mode → 15% of Profit_realized  
• LOOP Mode → 10% of Profit_realized  

Net profit:

Profit_net = Profit_realized − Fee

Fee applies only to positive profit.

---

## 10.3 Fee Allocation

Performance fee is distributed as follows:

• 60% → Ratchet Pool  
• 40% → Reward Pool  

Ratchet Pool strengthens redemption backing.
Reward Pool supports LOOP entitlement coverage.

Fee routing emits on-chain events.

---

## 10.4 USDT Mode Distribution

If reward mode = USDT:

Profit_net is credited to:

• Claimable USDT balance  
• Or reinvested if compounding selected  

User receives stablecoin directly.

---

## 10.5 LOOP Mode Distribution

If reward mode = LOOP:

Profit_net is denominated in USDT entitlement.

User must choose:

• Compound 100%  
• Compound 50% / Claim 50%  
• Claim 100%  

Claimed portion becomes redeemable via LOOP mechanism.

Compounded portion remains in vault capital.

---

## 10.6 No Profit, No Fee Principle

In a negative month:

• No fee is charged  
• Ratchet does not decrease  
• No rewards distributed  

Principal may fluctuate due to market conditions.
Previously realized profits are unaffected by current losses.

---

## 10.7 Economic Separation

Trading capital, reward accounting,
and ratchet reserves are segregated.

Fee distribution strengthens system backing
before rewards are paid.

This structure prioritizes structural integrity
over aggressive distribution.

---

YieldLoop’s fee model is simple:

No profit → no fee  
Profit → disciplined distribution  
Ratchet growth → surplus-dependent  
No downward adjustment

---

# 11. LOOP Internal Accounting & Redemption

LOOP is not a circulating token.

It is an internal accounting unit representing claimable profit
denominated in USDT and backed by reserve accounting.

LOOP exists only during redemption transactions.

It is minted and burned atomically.

---

## 11.1 LOOP Characteristics

LOOP:

• Is non-transferable  
• Has no peer-to-peer functionality  
• Cannot be traded  
• Cannot circulate  
• Cannot exist outside redemption  

LOOP supply does not persist.

There is no external market for LOOP.

---

## 11.2 Entitlement Accounting

If reward mode = LOOP:

At cycle end:

Entitlement_USDT = Profit_net × Claim_Portion

Where Claim_Portion ∈ {0%, 50%, 100%}

No LOOP is minted at this stage.

Only USDT-denominated entitlement is recorded.

---

## 11.3 Redemption Process

When user initiates redemption:

1. Validate Coverage ratio  
2. Validate Protection state  
3. Calculate LOOP_to_mint = Entitlement_USDT / RP  
4. Mint LOOP_to_mint  
5. Immediately burn LOOP_to_mint  
6. Transfer Entitlement_USDT (USDT) to user  

Mint and burn occur within the same transaction.

At no point does LOOP circulate.

---

## 11.4 Redemption Price (RP)

RP represents:

USDT value per 1 LOOP unit.

RP starts at 1.00 and may increase via ratchet mechanism.

RP may never decrease.

---

## 11.5 Coverage Requirement

Before redemption:

Required_Backing = LOOP_Entitlements × RP

Coverage = Redemption_Reserve / Required_Backing

If Coverage < minimum threshold:

• Redemption may throttle  
• Ratchet pauses  
• Rewards may pause  
• Trading may halt  

Redemption logic cannot bypass coverage validation.

---

## 11.6 Reserve Backing

Redemption reserves are funded by:

• 60% of performance fees  
• Ratchet surplus accumulation  
• System vault reinforcement  

Reserves are denominated in USDT.

LOOP is backed by accounting,
not by market demand.

---

## 11.7 Structural Properties

LOOP is designed to:

• Avoid speculative pricing  
• Avoid liquidity pools  
• Avoid token volatility  
• Avoid reflexive collapse  
• Avoid bank-run mechanics  

Redemption is deterministic,
subject to coverage enforcement.

---

## 11.8 LOOP Is Not Equity

LOOP does not grant:

• Governance rights  
• Profit share rights  
• Voting rights  
• Ownership rights  

It is an internal redemption accounting mechanism only.

---

YieldLoop separates:

Trading profits  
Reward accounting  
Reserve backing  
Token issuance  

This separation reduces fragility
and prevents speculative distortion.

---

# 12. Ratchet Engine Mechanics

The Ratchet Engine governs the upward adjustment
of the Redemption Price (RP).

It is surplus-dependent.
It is coverage-dependent.
It is one-directional.

RP may increase.
RP may never decrease.

---

## 12.1 Purpose of the Ratchet

The ratchet strengthens redemption backing over time
when structural surplus permits.

It is not designed to:

• Guarantee growth  
• Accelerate returns  
• Compensate for trading losses  
• Override coverage constraints  

It operates only when safety margins are sufficient.

---

## 12.2 Eligibility Requirements

A ratchet adjustment may occur only if:

• Coverage ≥ 1.15  
• Rolling 30-day net profit > 0  
• Reserve surplus ≥ predefined minimum  
• Protection state is not escalated  
• No critical threshold has been breached  

If any condition fails, ratchet remains unchanged.

---

## 12.3 Ratchet Formula

When eligible:

RP_new = RP_old × (1 + Step)

Where:

Step ≤ Step_Cap

Step_Cap ≤ 0.005 (0.50%)

Default Step may be smaller and selected
by Autonomous Control within bounded range.

---

## 12.4 Slow Growth Principle

The ratchet is intentionally conservative.

It prioritizes:

• Reserve overcapitalization  
• Redemption safety margin  
• Long-term durability  

Rapid ratchet increases reduce safety margin
and are structurally discouraged.

---

## 12.5 Surplus Requirement

Reserve surplus is defined as:

Redemption_Reserve − Required_Backing

Ratchet increases may occur only if:

Surplus ≥ Surplus_Minimum

Surplus_Minimum is enforced on-chain.

---

## 12.6 Interaction with Protection Module

If Coverage falls below warning thresholds:

• Ratchet pauses automatically  

If Coverage < C_min:

• Ratchet and LOOP rewards pause  

If Coverage < C_crit:

• Trading halts  

Ratchet cannot override protection state.

---

## 12.7 Negative Cycle Behavior

In a negative cycle:

• No fee is charged  
• No ratchet increase occurs  
• RP remains constant  

Losses affect principal.
Previously ratcheted value does not decrease.

---

## 12.8 Autonomous Adjustment Limits

Autonomous Control may:

• Select ratchet step within bounds  
• Propose increase at cycle boundary  

Autonomous Control may not:

• Reduce RP  
• Override eligibility requirements  
• Bypass coverage thresholds  

---

## 12.9 Structural Outcome

The ratchet creates asymmetry:

Downside:
Principal fluctuates with market.

Upside:
Redemption value may increase over time,
but only when structural backing permits.

The ratchet is reinforcement,
not speculation.

---

# 13. Coverage & Protection Framework

YieldLoop enforces structural protection through deterministic
coverage monitoring and staged escalation logic.

Protection rules are immutable.

Autonomous Control cannot override protection enforcement.

---

## 13.1 Coverage Ratio Definition

Required_Backing = LOOP_Entitlements × RP

Coverage = Redemption_Reserve / Required_Backing

Coverage is calculated using fixed-point arithmetic
with consistent precision.

---

## 13.2 Coverage Thresholds

The following thresholds are enforced:

C_warn = 1.10  
C_min  = 1.05  
C_crit = 1.00  

These values are immutable.

---

## 13.3 Escalation Hierarchy

Protection escalates in stages:

Stage 1 — Warning  
If Coverage < C_warn:  
• Emit warning event  

Stage 2 — Ratchet Pause  
If Coverage < C_min:  
• Pause ratchet  
• Pause LOOP rewards  

Stage 3 — Trading Halt  
If Coverage < C_crit:  
• Halt trading  
• Freeze new reward issuance  
• Preserve reserves  

Escalation is automatic.

---

## 13.4 Liquidity & Volatility Protection

ProtectionModule also monitors:

• Liquidity depth collapse  
• Spread compression  
• Gas spike conditions  
• Abnormal volatility  
• AUM shock events  

If structural instability is detected:

• Trading lanes may pause  
• Risk multipliers may tighten  
• Turnover limits may reduce  

Protection precedes performance.

---

## 13.5 AUM Shock Constraint

If:

New_Deposits / AUM_total > Growth_Threshold

Then:

Deployable_Capital = 0.5 × New_Deposits

Remaining capital stages into next cycle.

This prevents destabilizing liquidity impact.

---

## 13.6 Recovery Logic

If Coverage restores above thresholds:

• Ratchet may resume  
• LOOP rewards may resume  
• Trading may resume  

Recovery requires sustained threshold satisfaction.

Protection does not deactivate instantly upon recovery.

---

## 13.7 Event Transparency

Every protection trigger emits:

• ProtectionStageChanged event  
• Coverage value  
• Threshold breached  
• Timestamp  

All state transitions are on-chain and auditable.

---

## 13.8 Structural Integrity Condition

At all times:

Redemption_Reserve ≥ 0

If Coverage < 1.00:

• Trading must halt  
• Ratchet must pause  
• Rewards must pause  

No contract function may bypass this condition.

---

## 13.9 Design Intent

The Protection Framework exists to:

• Prevent redemption insolvency  
• Prevent over-distribution  
• Prevent edge illusion  
• Preserve structural backing  

YieldLoop prioritizes survival over growth.

Protection is not optional.

---

# 14. NFT Structure & Economic Alignment

The YieldLoop NFT program is designed to align early participants
with long-term system growth.

NFTs do not control trading logic.
They do not affect coverage thresholds.
They do not override protection mechanisms.
They do not grant governance rights.

Their function is economic alignment and participation incentive.

NFT participation is optional.

---

## 14.1 Purpose

The NFT program exists to:

• Fund early development  
• Align long-term users  
• Provide performance fee advantages  
• Redistribute a portion of marketing allocation back to participants  

NFTs are not required to use YieldLoop.

---

## 14.2 NFT Classes

YieldLoop includes two NFT classes:

### Lifetime Genesis

Total Supply: 550  
• 500 Public  
• 50 Reserved (Team / Marketing)

Price: 1,000 USDT  

Benefits:

• 50% reduction in performance fee  
• Eligibility for marketing redistribution credit  
• Lifetime duration  

---

### Genesis Lite

Total Supply: 3,333  
• 3,000 Public  
• 333 Reserved (Team / Marketing)

Price: 300 USDT  

Benefits:

• 25% reduction in performance fee  
• Eligibility for marketing redistribution credit  
• 12-month duration  

---

## 14.3 Fee Discount Application

Base performance fees:

• 15% (USDT mode)  
• 10% (LOOP mode)  

NFT discounts apply only to realized performance fees.

Example (USDT mode):

Base fee = 15%

• Lifetime Genesis → 7.5%  
• Genesis Lite → 11.25%

Example (LOOP mode):

Base fee = 10%

• Lifetime Genesis → 5%  
• Genesis Lite → 7.5%

Discount reduces total fee collected.
Fee routing logic remains unchanged.

---

## 14.4 Fee Routing After Discount

After discount is applied:

• 60% of actual fee collected → Ratchet Pool  
• 40% of actual fee collected → Reward Pool  

NFTs reduce total fee amount,
but do not alter routing percentages.

Protection thresholds always apply before distribution.

---

## 14.5 Marketing Redistribution Credit

A fixed portion of realized marketing allocation
is redistributed monthly as deposit credit.

Marketing allocation is sourced from the fee distribution.

Redistribution characteristics:

• Derived only from actual realized fees  
• Calculated at cycle end  
• Distributed proportionally to eligible NFT holders  
• Credited as deposit credit only  

Deposit credits:

• Cannot be withdrawn as USDT  
• Cannot be redeemed as LOOP  
• Can only be applied to future vault deposits  

This mechanism increases capital participation
without weakening redemption reserves.

---

## 14.6 Duration Enforcement

Genesis Lite:

• Benefits valid for 12 months from mint date  
• Automatically expire  
• Expiration event emitted on-chain  

Lifetime Genesis:

• Benefits do not expire  

Expired NFTs revert to base fee structure.

---

## 14.7 Structural Isolation

NFT logic:

• Cannot alter trading parameters  
• Cannot alter Autonomous Control bounds  
• Cannot modify coverage thresholds  
• Cannot influence ratchet eligibility  
• Cannot pause or activate trading  

All benefits are enforced through smart contract logic.

No manual adjustments are possible.

---

## 14.8 Safety Independence

NFT participation does not alter:

• Protection escalation rules  
• Coverage enforcement  
• Ratchet non-decrease rule  
• Trade size constraints  
• Net expectancy requirements  

YieldLoop’s safety mechanisms remain independent
of NFT participation.

---

The NFT program strengthens user alignment
without introducing governance risk
or weakening system invariants.

# 15. System Vault & Overflow Reinforcement

YieldLoop includes an automatic reinforcement mechanism
designed to strengthen reserves during sustained profitability.

This mechanism operates under the same rules as user vaults.

It does not bypass fees.
It does not bypass protection.
It does not alter invariants.

---

## 15.1 Reward Pool Surplus & Suspense

The Reward Pool is intended to remain slightly overcapitalized
relative to outstanding LOOP entitlements.

If surplus accumulates beyond required buffers,
excess capital moves into a temporary holding balance ("Suspense").

Surplus is defined as:

Reward_Pool_Balance − Required_Reward_Buffer

Only excess above required buffer may move to Suspense.

Suspense funds remain in USDT.

---

## 15.2 System Vault Creation Threshold

When Suspense balance reaches:

500 USDT

A new System Vault is created.

System Vaults are protocol-owned vault containers.

They follow all standard vault rules.

---

## 15.3 System Vault Fill Mechanics

After creation:

Every additional 250 USDT accumulated in Suspense
is deposited into the active System Vault.

System Vault cap:

50,000 USDT

When cap is reached:

• That System Vault stops accepting deposits  
• A new System Vault is created once Suspense again reaches 500 USDT  
• Fill cycle repeats  

This structure prevents uncontrolled vault proliferation.

---

## 15.4 System Vault Configuration

Each System Vault operates with:

• Reward mode: LOOP  
• Compounding: 50% compound / 50% claim  

All other rules mirror user vaults:

• Same trading constraints  
• Same performance fee  
• Same liquidity discipline  
• Same protection enforcement  
• Same ratchet eligibility  

System Vaults do not receive special treatment.

---

## 15.5 System Vault Profit Flow

At cycle end:

System Vault profit is finalized like any other vault.

Because reward mode = LOOP:

• 50% of Profit_net compounds  
• 50% becomes LOOP entitlement  

Upon redemption of LOOP entitlement:

• 50% of redeemed USDT → Ratchet Pool  
• 50% of redeemed USDT → Reward Pool  

This reinforces both pools symmetrically.

---

## 15.6 Reinforcement Objective

System Vaults convert surplus into active capital.

They:

• Increase trading base gradually  
• Strengthen ratchet pool over time  
• Strengthen reward pool backing  
• Maintain structural overcapitalization  

Reinforcement occurs only when surplus exists.

---

## 15.7 Protection Interaction

If Coverage < C_min:

• Suspense accumulation pauses  
• No new System Vaults are created  
• Ratchet pauses  

If Coverage < C_crit:

• Trading halts  
• System Vault deposits pause  

System Vault logic cannot override protection state.

---

## 15.8 Structural Outcome

The System Vault mechanism ensures that:

• Surplus strengthens the protocol  
• Growth is incremental  
• Reinforcement is automatic  
• No external capital is required  
• Safety margins are preserved  

YieldLoop scales internally through discipline,
not through leverage.

---

# 16. Profitability Comparison Scenarios

The following examples illustrate how YieldLoop distributes profit
under different reward modes and NFT tiers.

These examples assume:

• Initial Vault Capital: 10,000 USDT  
• Monthly Gross Profit: 5% (500 USDT)  
• No mid-cycle deposits  
• No negative cycle  

Performance fee applies only to realized profit.

---

## 16.1 USDT Mode — No NFT

Gross Profit: 500 USDT  
Performance Fee (15%): 75 USDT  
Net Profit: 425 USDT  

Distribution:

• 425 USDT credited to user  
• 75 USDT distributed:
  - 45 USDT → Ratchet Pool (60%)
  - 30 USDT → Reward Pool (40%)

User receives 425 USDT.

---

## 16.2 USDT Mode — Genesis Lite (25% Fee Reduction)

Base Fee: 15%  
Reduced Fee: 11.25%

Gross Profit: 500 USDT  
Fee (11.25%): 56.25 USDT  
Net Profit: 443.75 USDT  

Distribution:

• 443.75 USDT credited to user  
• 56.25 USDT distributed:
  - 33.75 USDT → Ratchet Pool
  - 22.50 USDT → Reward Pool

User earns +18.75 USDT more than non-NFT holder.

---

## 16.3 USDT Mode — Lifetime Genesis (50% Fee Reduction)

Base Fee: 15%  
Reduced Fee: 7.5%

Gross Profit: 500 USDT  
Fee (7.5%): 37.5 USDT  
Net Profit: 462.5 USDT  

Distribution:

• 462.5 USDT credited to user  
• 37.5 USDT distributed:
  - 22.5 USDT → Ratchet Pool
  - 15 USDT → Reward Pool

User earns +37.5 USDT more than non-NFT holder.

---

## 16.4 LOOP Mode — No NFT

Base Fee: 10%

Gross Profit: 500 USDT  
Fee (10%): 50 USDT  
Net Profit: 450 USDT  

If compounding 50% / claim 50%:

• 225 USDT compounded  
• 225 USDT becomes LOOP entitlement  

Fee Distribution:

• 30 USDT → Ratchet Pool  
• 20 USDT → Reward Pool  

User retains upside via ratchet exposure.

---

## 16.5 LOOP Mode — Genesis Lite (25% Fee Reduction)

Base Fee: 10%  
Reduced Fee: 7.5%

Gross Profit: 500 USDT  
Fee (7.5%): 37.5 USDT  
Net Profit: 462.5 USDT  

50/50 Example:

• 231.25 USDT compounded  
• 231.25 USDT LOOP entitlement  

Fee Distribution:

• 22.5 USDT → Ratchet Pool  
• 15 USDT → Reward Pool  

---

## 16.6 LOOP Mode — Lifetime Genesis (50% Fee Reduction)

Base Fee: 10%  
Reduced Fee: 5%

Gross Profit: 500 USDT  
Fee (5%): 25 USDT  
Net Profit: 475 USDT  

50/50 Example:

• 237.5 USDT compounded  
• 237.5 USDT LOOP entitlement  

Fee Distribution:

• 15 USDT → Ratchet Pool  
• 10 USDT → Reward Pool  

---

## 16.7 Negative Cycle Scenario

If Gross Profit ≤ 0:

• Fee = 0  
• No USDT rewards  
• No LOOP entitlement  
• Ratchet does not decrease  

Principal fluctuates with market conditions.
Previously realized profits are unaffected.

---

## 16.8 Structural Observation

USDT Mode:
• Higher fee
• Immediate liquidity
• No ratchet exposure

LOOP Mode:
• Lower base fee
• Exposure to ratchet increases
• Optional compounding

NFT Tiers:
• Reduce fee burden
• Increase net retained profit
• Do not alter system safety

YieldLoop’s economics are transparent,
deterministic,
and profit-dependent.

---

# 17. Cross-Chain Sovereign Deployment Model

YieldLoop is deployed per blockchain as a sovereign instance.

Each deployment operates independently.

There are no shared reserves.
There are no shared redemption pools.
There is no cross-chain LOOP accounting.
There are no bridges.

---

## 17.1 Sovereign Instance Principle

Each chain deployment includes:

• Independent VaultManager  
• Independent TradingEngine  
• Independent ProtectionModule  
• Independent Ratchet Pool  
• Independent Reward Pool  
• Independent RedemptionContract  
• Independent LOOP accounting  

Capital never moves across chains.

---

## 17.2 No Bridge Architecture

YieldLoop does not:

• Bridge capital between chains  
• Share redemption backing across chains  
• Share ratchet state across chains  
• Maintain wrapped LOOP tokens  

Bridges introduce systemic exploit risk.
YieldLoop eliminates that category entirely.

---

## 17.3 Independent LOOP Accounting

Each deployment maintains its own:

• Redemption Price (RP)  
• Coverage ratio  
• LOOP entitlement ledger  
• Reserve balance  

LOOP on one chain has no relationship
to LOOP on another chain.

There is no cross-chain arbitrage of LOOP.

---

## 17.4 Deployment Expansion Model

If YieldLoop expands to additional chains:

• A new sovereign deployment is created  
• Contracts are redeployed  
• Reserves are independent  
• Protection logic is independent  
• Autonomous Control operates independently  

Expansion does not modify existing deployments.

---

## 17.5 Risk Containment

If one deployment experiences:

• Liquidity collapse  
• Market disruption  
• Technical failure  
• Protection escalation  

Other deployments remain unaffected.

This prevents contagion.

---

## 17.6 Shared Philosophy, Not Shared State

Deployments may share:

• Codebase design  
• Economic philosophy  
• Parameter bounds  
• Structural invariants  

They do not share:

• Capital  
• Reserves  
• Ratchet growth  
• Coverage backing  

YieldLoop scales horizontally,
not centrally.

---

## 17.7 Structural Intent

The cross-chain model prioritizes:

• Containment over expansion  
• Isolation over interconnectedness  
• Predictability over complexity  

Each chain instance stands alone.

Failure in one instance does not propagate.

---

# 18. Security & Immutability Architecture

YieldLoop is designed to maximize structural trust.

It contains:

• No governance layer  
• No admin override  
• No emergency backdoor  
• No parameter manipulation authority  
• No proxy upgrade path  

Core contracts are immutable once deployed.

---

## 18.1 No Administrative Control

There is no:

• Owner function to pause trading  
• Owner function to modify fees  
• Owner function to alter coverage thresholds  
• Owner function to change asset universe  
• Owner function to alter ratchet logic  

All protection logic is autonomous and deterministic.

---

## 18.2 Immutable Core Modules

The following modules are immutable:

• VaultManager  
• TradingEngine  
• ProtectionModule  
• RatchetEngine  
• LOOP Redemption Accounting  
• Reward Pool  
• Ratchet Pool  

Contract bytecode cannot be modified post-deployment.

---

## 18.3 Autonomous Bounded Parameters

Autonomous Control may:

• Adjust trade sizing within fixed bounds  
• Select ratchet step within capped range  
• Tighten liquidity constraints  
• Pause trading lanes if protection thresholds trigger  

Autonomous Control may not:

• Alter threshold constants  
• Alter fee rates  
• Alter routing percentages  
• Modify asset universe  
• Reduce Redemption Price  

Autonomy exists only inside hard-coded bounds.

---

## 18.4 Event Transparency

All state transitions emit on-chain events:

• ProtectionStageChanged  
• RatchetUpdated  
• VaultCreated  
• FeeDistributed  
• RedemptionExecuted  
• NFTExpired  

System behavior is auditable.

---

## 18.5 Failure Containment

If a bug is discovered:

• The affected deployment remains isolated  
• No cross-chain contagion occurs  
• New deployment must be launched  
• Old deployment remains immutable  

YieldLoop does not support in-place upgrades.

Version migration requires:

• New contract deployment  
• Voluntary user migration  

---

## 18.6 Upgrade Philosophy

YieldLoop does not upgrade itself.

Improvements require:

• New sovereign deployment  
• Public documentation of changes  
• Fresh audit  

Old deployments remain intact.

No hidden evolution is possible.

---

## 18.7 Key Management

System-owned vaults operate via contract logic only.

There are no private keys with special authority.

Treasury-like balances (Ratchet / Reward / Suspense)
are contract-controlled and rule-bound.

---

## 18.8 Design Intent

Security is achieved through:

• Constraint  
• Immutability  
• Isolation  
• Deterministic enforcement  

YieldLoop prioritizes
trust minimization over flexibility.

---

# 19. Full System Walkthrough

This section illustrates how YieldLoop operates
from deposit to redemption under real conditions.

---

## 19.1 Vault Creation

A user connects via:

• Web3 wallet (MetaMask, Trust, etc.)  
or  
• Embedded wallet (Privy) with optional Transak fiat purchase  

User deposits:

Minimum 500 USDT

Upon receipt:

• A vault is created  
• Vault is assigned unique ID  
• Vault capacity = 50,000 USDT  
• Vault enters Pending state until next cycle  

---

## 19.2 Mid-Cycle Deposit

If deposit occurs mid-month:

• Funds remain idle in vault  
• No trading occurs  
• Capital activates at next cycle boundary  

Trading begins on the 1st of the month.

---

## 19.3 Allocation Selection

Before cycle start, user allocates capital:

0–100% across:

BTC  
ETH  
BNB  
XRP  
SOL  
USDC  
USDT  

Allocation determines what assets
the TradingEngine may operate against.

User selects reward mode:

• USDT  
• LOOP  

If LOOP:

User selects compounding preference:

• Compound 100%  
• Compound 50% / Claim 50%  
• Claim 100%  

If NFT held:

Fee discount is automatically detected.

---

## 19.4 Cycle Execution

On cycle start:

• TradingEngine activates  
• Arbitrage and spot strategies execute  
• Expectancy must remain positive  
• Liquidity limits enforced  
• ProtectionModule monitors coverage  

Trading continues until cycle end.

---

## 19.5 Cycle Finalization (Positive Month)

Example:

Vault capital: 10,000 USDT  
Gross profit: 500 USDT  

Performance fee applied.

Fee is distributed:

• 60% → Ratchet Pool  
• 40% → Reward Pool  

Net profit distributed per reward mode.

---

## 19.6 USDT Claimant Flow

If reward mode = USDT:

• Net profit becomes claimable USDT  
• User may withdraw immediately  
• Or leave idle for next cycle  

No LOOP accounting involved.

If NFT:

Reduced fee increases net profit retained.

---

## 19.7 LOOP Claimant Flow

If reward mode = LOOP:

• Net profit becomes USDT-denominated entitlement  

Based on compounding selection:

Compound portion:
• Added to vault capital  

Claim portion:
• Recorded as LOOP entitlement  

When user redeems:

• LOOP minted and burned atomically  
• USDT transferred  
• Redemption Price applied  
• Coverage validated  

If RP has ratcheted upward,
user receives greater USDT per LOOP unit.

---

## 19.8 Ratchet Interaction

If system surplus permits:

• RP increases modestly  
• Increase is permanent  
• Only upward movement allowed  

Ratchet does not activate in negative cycles.

---

## 19.9 System Vault Reinforcement

If Reward Pool surplus exceeds buffer:

• Excess moves to Suspense  
• At 500 USDT → System Vault created  
• Each 250 USDT fills until 50,000 cap  

System Vault:

• Trades like any vault  
• Pays same performance fee  
• Uses LOOP mode  
• Compounds 50% / claims 50%  

Claimed LOOP from System Vault:

• 50% → Ratchet Pool  
• 50% → Reward Pool  

This strengthens reserves over time.

---

## 19.10 Negative Cycle

If month ends negative:

• Fee = 0  
• No USDT rewards  
• No LOOP entitlements  
• Ratchet remains unchanged  

Principal may decline.

Protection thresholds remain enforced.

Previously realized profit remains intact.

---

## 19.11 Coverage Escalation Example

If Coverage < C_min:

• Ratchet pauses  
• LOOP rewards pause  

If Coverage < C_crit:

• Trading halts  
• System stabilizes  

When Coverage recovers:

• Trading resumes  
• Ratchet resumes if eligible  

Protection cannot be bypassed.

---

## 19.12 Vault Cap & Expansion

Vault cap = 50,000 USDT.

When cap is reached:

• Vault stops accepting deposits  
• User may create new vault  

Vaults scale horizontally.

---

## 19.13 Account Closure

User may:

• Withdraw all capital  
• Close vault  

Upon closure:

• Remaining capital returned  
• Any LOOP entitlement redeemed  
• Vault marked inactive  

Closure does not affect other vaults.

---

## 19.14 System Summary

YieldLoop operates on:

• Fixed cycle boundaries  
• Profit-only fee model  
• Deterministic accounting  
• Coverage-based protection  
• Surplus-driven ratchet growth  
• Automatic internal reinforcement  

Every component is rule-bound.

No human override exists.

---

# 20. Risk Disclosure & Structural Limits

YieldLoop is a rule-based trading protocol.

It is not insured.
It does not guarantee profit.
It does not eliminate market risk.

Participation involves real financial risk.

---

## 20.1 Market Risk

Vault capital is deployed into:

• Spot positions  
• Arbitrage routes  
• Volatility environments  

Markets may:

• Decline sharply  
• Experience liquidity collapse  
• Experience abnormal spread compression  
• Experience extreme volatility  

Principal value may decrease during negative cycles.

YieldLoop does not guarantee capital preservation.

---

## 20.2 Trading Risk

Although:

• Expectancy must remain positive  
• Liquidity depth limits are enforced  
• Gas friction is accounted for  

Unexpected market events may result in:

• Slippage  
• Execution delay  
• Temporary mispricing  
• Net negative cycle  

Autonomous Control operates within bounds,
but cannot eliminate market uncertainty.

---

## 20.3 Smart Contract Risk

YieldLoop operates entirely on-chain.

Risks include:

• Undiscovered contract bugs  
• Logic flaws  
• Dependency failures  
• Oracle inaccuracies  
• Blockchain-level failure  

Contracts are immutable once deployed.
Bugs cannot be patched in-place.

---

## 20.4 Protection Limits

ProtectionModule enforces:

• Coverage thresholds  
• Trading halt conditions  
• Ratchet pause logic  

Protection reduces insolvency risk,
but cannot prevent trading losses.

Coverage protection applies to LOOP redemption,
not principal preservation.

---

## 20.5 Liquidity Risk

YieldLoop depends on:

• PancakeSwap  
• BiSwap  
• BNB Chain infrastructure  

If DEX liquidity collapses
or chain infrastructure fails:

• Trading may halt  
• Capital may be temporarily illiquid  

DEX-level risk cannot be eliminated.

---

## 20.6 Negative Cycles

In a negative month:

• No fee is charged  
• No LOOP is issued  
• Ratchet does not decrease  

However:

Principal may decline.

Previous profits are not clawed back,
but future growth may slow.

---

## 20.7 NFT Risk

NFT purchase:

• Does not guarantee profitability  
• Only reduces performance fee  
• Does not influence trading outcome  

Marketing redistribution credits
depend on realized fee generation.

If profit = 0,
redistribution = 0.

---

## 20.8 Ratchet Misinterpretation Risk

Ratchet increases:

• Are surplus-dependent  
• Are not guaranteed  
• May pause for extended periods  

RP does not increase automatically each cycle.

---

## 20.9 No Governance Recourse

YieldLoop contains:

• No governance mechanism  
• No admin override  
• No appeal process  

If you disagree with system behavior,
your option is to withdraw.

Autonomy is a design choice.

---

## 20.10 Regulatory Risk

Users are responsible for:

• Local compliance  
• Tax reporting  
• Legal eligibility  

YieldLoop does not provide legal or tax advice.

---

## 20.11 Structural Reality

YieldLoop reduces certain systemic risks
through:

• Isolation  
• Immutability  
• Deterministic enforcement  
• Coverage modeling  

It does not eliminate risk.

Participation requires understanding
that capital is at risk.

YieldLoop is a tool,
not a guarantee.

---

# 21. Conclusion — Design Philosophy & Intent

YieldLoop exists to formalize a simple principle:

Profit must be earned.
Risk must be bounded.
Accounting must be deterministic.
Protection must be automatic.

The protocol does not promise outcomes.
It enforces structure.

---

## 21.1 Design Philosophy

YieldLoop was built around constraints:

• Fixed asset universe  
• Profit-only fee model  
• Deterministic fee routing  
• Coverage-based protection  
• One-directional ratchet  
• No governance  
• No admin override  
• No upgrade proxy  

Constraint creates trust.

---

## 21.2 Separation of Concerns

YieldLoop separates:

Trading  
Reward accounting  
Redemption backing  
Ratchet growth  
NFT incentives  

No component is allowed to override another.

Trading does not control redemption.
NFTs do not control safety.
Autonomy does not override invariants.

This separation reduces systemic fragility.

---

## 21.3 Survival Over Speed

The protocol does not chase maximum yield.

It prioritizes:

• Liquidity-aware execution  
• Incremental reinforcement  
• Controlled scaling  
• Horizontal vault growth  

Ratchet growth is slow by design.
System vaults grow only when surplus permits.

---

## 21.4 Autonomy Without Governance

YieldLoop removes:

• Voting  
• Parameter manipulation  
• Admin discretion  

All logic is rule-bound.

Users interact with transparent contracts,
not committees.

---

## 21.5 Structural Asymmetry

Losses affect principal.

Ratchet increases, once earned, do not reverse.

Protection pauses before insolvency.

Growth is conditional.
Decline is bounded by enforcement.

---

## 21.6 What YieldLoop Is Not

YieldLoop is not:

• A guaranteed income system  
• A leveraged yield farm  
• A governance experiment  
• A speculative token ecosystem  
• A bridge-based liquidity network  

It is a constrained trading vault protocol
with deterministic reinforcement mechanics.

---

## 21.7 Final Statement

YieldLoop is built on the belief that:

Transparency beats promises.
Constraint beats flexibility.
Isolation beats interconnected fragility.
Discipline beats acceleration.

The system does not rely on trust in operators.

It relies on enforced rules.

Participation is voluntary.
Understanding is required.
Risk is real.

The protocol stands on structure,
not optimism.


