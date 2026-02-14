# YieldLoop
## Consolidated Whitepaper  
### Version 1.0 (Draft)  
### Date: March 2026  
### Author: YieldLoop LLC  

---

## Abstract

YieldLoop is a smart-contract based, isolated vault system operating on BNB Chain.  
It enables user-approved trading strategies, performance-fee funded reserve mechanics, and a fully backed internal redemption instrument (LOOP).  

The system is designed for capital discipline, structural transparency, and mathematically permanent floor growth supported exclusively by realized profits.

---

# Table of Contents

1. Introduction  
2. System Design Principles  
3. Vault Architecture  
   - 3.1 Isolated Vault Model  
   - 3.2 Vault Caps & Allocation Limits  
   - 3.3 Multi-Vault per Wallet Design  
4. Trading Engine & Risk Framework  
   - 4.1 Approved Trading Universe  
   - 4.2 Conservative Default Parameters  
   - 4.3 User Configuration & Acknowledgment  
   - 4.4 Emergency Controls  
5. Capital Flow & Fee Structure  
   - 5.1 Performance Fee Model  
   - 5.2 Fee Allocation Breakdown  
6. LOOP Token Architecture  
   - 6.1 Minting Mechanism  
   - 6.2 Redemption Mechanics  
   - 6.3 Supply Constraints  
7. Reserve System  
   - 7.1 Reward Reserve (Redemption Pool)  
   - 7.2 Ratchet Pool (Floor Support)  
   - 7.3 Coverage Ratios & Buffer Targets  
8. Dynamic Ratchet Model  
   - 8.1 Eligibility Conditions  
   - 8.2 Smoothing Function  
   - 8.3 Floor Increase Constraints  
9. System Vault (Stabilization Engine)  
   - 9.1 Pressure Relief Logic  
   - 9.2 Profit Routing Rules  
   - 9.3 Operational Constraints  
10. NFT Structure  
    - 10.1 Genesis Lite  
    - 10.2 Genesis Lifetime  
    - 10.3 Fee Discount Rules  
    - 10.4 Supply Constraints  
11. Governance & Multisig Controls  
    - 11.1 Adjustable Parameter Ranges  
    - 11.2 Immutable Rules  
12. Transparency & Public Dashboard  
13. Security Model  
14. Economic Invariants  
15. Risk Disclosures  
16. Legal & Structural Notes  
17. Roadmap  

---

# 1. Introduction

YieldLoop is a smart-contract based vault system operating on BNB Chain.  
It enables users to deploy capital into isolated execution environments, select or approve defined trading strategies, and receive realized profit distributions under a structured, rule-bound framework.

YieldLoop does not promise returns, advertise yield targets, or project performance.  
All profit is variable and dependent on realized market execution.

The system is built around three core objectives:

1. Capital Isolation  
2. Mathematical Discipline  
3. Structural Transparency  

Each user vault is isolated at the smart-contract level.  
User principal is never commingled with system reserves or protocol-owned capital.  
All trading activity is restricted to predefined assets and bounded risk parameters.

Performance fees are collected only on realized profit.  
These fees fund a dual-reserve structure that supports an internal redemption instrument, LOOP.

LOOP is not externally traded.  
It is minted exclusively from realized user profit when selected as a reward option.  
Each minted unit is backed by USDT in a dedicated redemption reserve.  
LOOP may be redeemed for USDT at the prevailing floor price under defined system conditions.

A secondary reserve mechanism, referred to as the Ratchet Pool, enables permanent upward adjustment of the LOOP redemption floor when mathematically supported by accumulated surplus.  
Floor increases are deterministic, smoothing-constrained, and may only occur when sufficient capital exists to support permanent protection.

Excess reserve capital is routed to a protocol-owned stabilization vault operating under conservative constraints.  
This mechanism strengthens long-term structural backing and prevents capital inefficiency without distributing speculative rewards.

YieldLoop is designed to operate as a closed financial loop:

Capital → Execution → Realized Profit → Performance Fee → Structural Backing → Redemption Integrity → Capital Confidence

All critical economic behaviors are bounded either immutably in contract or adjustable only within restricted parameter ranges via multisignature governance.

The system prioritizes durability over velocity.  
Growth is permitted to accelerate when mathematically supported by realized surplus.  
At all times, safety constraints precede expansion.

YieldLoop is a capital discipline framework first, and a growth system second.

---

# 2. System Design Principles

YieldLoop is governed by structural rules designed to prevent discretionary behavior, capital misuse, or economically unstable expansion.

The following principles define the system’s foundation.

---

## 2.1 Capital Isolation

- Each user vault is isolated at the smart-contract level.
- User principal is never commingled with other user vaults.
- User principal is never used to fund reserves, ratchet mechanisms, or protocol-owned strategies.
- System reserves are segregated from user capital at all times.

Principal may increase or decrease solely as a result of approved trading execution.

---

## 2.2 Realized Profit Only

- Performance fees are charged only on realized profit.
- No fees are collected on principal.
- No fees are collected on unrealized gains.
- No profit projections, APR targets, or forward-looking return estimates are embedded in protocol logic.

If a vault cycle produces no profit, no performance fee is collected.

---

## 2.3 Deterministic Backing

- LOOP may only be minted when a user elects to receive realized profit in LOOP.
- Minting requires an equivalent USDT transfer into the Reward Reserve.
- LOOP supply cannot expand without corresponding reserve backing.

Redemption is funded exclusively from the Reward Reserve.

---

## 2.4 Permanent Floor Integrity

The LOOP redemption floor may only increase when:

- Reserve coverage exceeds the defined target buffer.
- Sufficient Ratchet Pool surplus exists to permanently support the increase.
- Smoothing and cooldown constraints are satisfied.

The floor may never decrease.

Floor increases require capital transfer from the Ratchet Pool to the Reward Reserve sufficient to support the new redemption liability.

No floor increase may occur without full structural funding.

---

## 2.5 No Discretionary Money Flows

Protocol-owned capital flows are restricted to the following paths:

- Performance fee allocation (fixed percentage)
- Ratchet Pool funding
- Reward Reserve funding
- System Vault funding (excess routing only)
- System Vault profit routing to reserves

No other routing paths exist.

Multisignature governance cannot withdraw reserve capital for discretionary use.

---

## 2.6 Bounded Governance

Multisignature governance may adjust certain economic parameters only within predefined contract-enforced ranges.

Governance cannot:

- Lower minimum reserve requirements below immutable thresholds
- Mint LOOP without backing
- Reduce redemption floor
- Withdraw user principal
- Override ratchet eligibility logic

All parameter adjustments emit on-chain events and are publicly visible.

---

## 2.7 Elastic but Disciplined Growth

YieldLoop permits accelerated floor growth during periods of elevated surplus.

Growth remains:

- Mathematically constrained
- Smoothing-controlled
- Buffer-protected
- Deterministic

The system is designed to absorb volatility, not amplify it.

---

## 2.8 Transparency by Default

The protocol publishes real-time visibility into:

- LOOP supply
- Reward Reserve balance
- Ratchet Pool balance
- Coverage ratios
- Floor price
- Last ratchet event
- Maximum safe ratchet capacity

YieldLoop relies on transparent accounting rather than narrative confidence.

---

These principles are embedded into contract logic and serve as the non-negotiable foundation of the system.

---

# 3. Vault Architecture

YieldLoop operates through isolated smart-contract vaults.  
Each vault represents a discrete capital allocation environment with independent accounting, execution, and profit realization.

---

## 3.1 Isolated Vault Model

- Each vault is deployed as an isolated contract instance.
- Vaults are owned and controlled by the originating wallet address.
- Capital within a vault is never commingled with other user vaults.
- Profit, loss, and fee calculations occur per vault.

System reserves (Reward Reserve, Ratchet Pool, System Vault) are separate from user vault contracts.

---

## 3.2 Multi-Vault Per Wallet

A single wallet address may create multiple vaults.

This allows users to:

- Segment risk profiles
- Allocate different trading configurations
- Maintain separate capital strategies
- Operate within per-vault capital limits

Vault creation is permissionless, subject to minimum and maximum deposit constraints.

The user interface may default to a single-vault view for simplicity while supporting advanced multi-vault operation.

---

## 3.3 Vault Deposit Parameters

Vault creation minimum:
- 500 USDT (BEP-20)

Subsequent deposit minimum:
- 250 USDT (BEP-20)

Per-vault capital cap:
- Adjustable within contract-enforced range (25,000–50,000 USDT)
- Hard maximum: 50,000 USDT per vault

If a vault reaches its maximum cap, additional capital requires creation of a new vault.

---

## 3.4 Trading Configuration

Upon vault creation or prior to cycle initiation, the user must:

1. Select or approve trading pairs from the approved universe
2. Select reward currency (USDT or LOOP)
3. If LOOP selected, select compound preference:
   - Compound 100%
   - Compound 50% / Claim 50%
   - Claim 100%
4. Acknowledge risk disclosures
5. Confirm strategy parameters

The system provides conservative defaults.

Users must explicitly approve any configuration before execution.

---

## 3.5 Approved Trading Universe

Trading is limited to spot transactions on PancakeSwap and Biswap.

Approved assets:

- BNB
- USDT
- USDC
- BTC (wrapped)
- ETH (wrapped)
- SOL (wrapped)
- XRP (wrapped)

No leverage.
No perpetuals.
No lending.
No yield farming.
No external protocol exposure beyond spot DEX execution.

---

## 3.6 Vault Cycle Mechanics

YieldLoop operates on a fixed 30-day cycle.

During cycle:

- Trading execution is active
- Profit and loss accrue in real time
- Unrealized gains are not fee-bearing

At cycle close:

- Positions may remain open and roll forward
- Realized profit is calculated
- Performance fee applied to realized profit only
- Reward distribution occurs according to selected reward preference

Users may elect to:

- Allow vault to auto-roll into next cycle
- Stop at end of cycle and close positions
- Trigger emergency exit (see 3.7)

---

## 3.7 Exit Mechanics

Standard Exit:
- User elects to stop at cycle end
- Positions close at cycle boundary
- Vault NAV returned in USDT
- No additional penalty

Emergency Exit:
- Positions close immediately
- Profit is forced to USDT claim mode
- Standard performance fee applied
- Additional exit penalty may apply (bounded, contract-defined)

Emergency exit does not affect other vaults.

---

## 3.8 Loss Handling

If a vault cycle produces a net loss:

- No performance fee is charged
- No LOOP is minted
- System reserves are unaffected
- Principal reflects trading loss only

User capital absorbs trading risk directly and exclusively.

---

Vault architecture is designed to ensure capital clarity, risk containment, and execution transparency.

---

# 4. Trading Engine & Risk Framework

YieldLoop utilizes a bounded, rules-based trading engine operating exclusively on approved decentralized exchanges.

The engine is designed to prioritize capital preservation and execution discipline over return maximization.

---

## 4.1 Execution Environment

All trading activity occurs:

- On BNB Chain
- Via PancakeSwap (PCS)
- Via Biswap
- Spot market transactions only

The engine executes:

- Buy and sell orders
- Rebalancing trades
- Cross-DEX arbitrage when spread conditions are met

The engine does not:

- Use leverage
- Use derivatives
- Borrow capital
- Engage in yield farming
- Stake external protocol tokens
- Bridge capital cross-chain

Execution is fully on-chain and transparent.

---

## 4.2 Approved Asset Universe

Trading pairs are restricted to the following wrapped assets:

- BNB
- USDT
- USDC
- BTC
- ETH
- SOL
- XRP

Additional assets may only be added via multisig governance within predefined contract limits.

---

## 4.3 Conservative Default Parameters

Each vault initializes with conservative execution constraints:

- Minimum USDT allocation buffer (e.g., ≥ 40%)
- Maximum allocation per asset (bounded)
- Maximum position size per trade
- Maximum exposure to arbitrage
- Drawdown detection thresholds
- Automatic exposure reduction on volatility spike

Users may adjust within allowed ranges.

Governance may only tighten constraints beyond defaults; it may not loosen beyond contract-defined maximums without satisfying coverage and cooldown conditions.

---

## 4.4 Arbitrage Conditions

Cross-DEX arbitrage may execute only when:

- Spread exceeds predefined minimum threshold
- Spread exceeds estimated gas + slippage costs
- Liquidity depth satisfies minimum size requirement
- Failure count threshold not exceeded

If arbitrage attempts fail consecutively beyond a bounded threshold, arbitrage module auto-disables for the remainder of the cycle.

---

## 4.5 Risk Escalation Controls

The system contains automatic risk responses:

If per-asset drawdown exceeds threshold:
- Reduce exposure
- Increase USDT allocation

If vault-level drawdown exceeds threshold:
- Shift majority of capital to USDT
- Restrict new exposure until stabilization

These responses are deterministic and not discretionary.

---

## 4.6 Multisig Risk Controls

Multisig may:

- Pause trading globally
- Pause arbitrage module
- Tighten exposure limits
- Raise minimum USDT allocation
- Reduce max position size

Multisig may not:

- Increase leverage (none exists)
- Override drawdown triggers
- Force risky exposure
- Bypass cooldown logic

All changes emit on-chain governance events.

---

## 4.7 Emergency Protocol

In the event of:

- DEX exploit
- Oracle instability
- Chain-level issue
- Contract anomaly

Multisig may:

- Immediately pause new trades
- Prevent new deposits
- Force vaults to settle at cycle end
- Disable arbitrage

At no time may multisig withdraw user capital.

---

## 4.8 Execution Transparency

Execution logs are publicly auditable via:

- On-chain transaction history
- Contract events
- Public dashboard reporting

The trading engine operates within deterministic constraints and does not rely on discretionary operator input.

---

YieldLoop’s trading engine is intentionally bounded to reduce systemic fragility and prevent capital overextension.

---

# 5. Capital Flow & Fee Structure

YieldLoop collects performance fees exclusively on realized profit.  
No fees are charged on principal or unrealized gains.

All capital movements follow deterministic routing rules embedded in contract logic.

---

## 5.1 Performance Fee Model

At the close of each vault cycle:

- Realized profit is calculated.
- If profit > 0, a performance fee is applied.
- If profit ≤ 0, no performance fee is charged.

Performance fee rates:

- 15% of realized profit when reward is claimed in USDT
- 10% of realized profit when reward is selected as LOOP

NFT-based fee discounts apply as defined in Section 10.  
Discounts reduce the applicable performance fee but do not alter fee allocation percentages.

---

## 5.2 Fee Allocation Breakdown

All collected performance fees are split as follows:

- 55% → Ratchet Pool
- 25% → Development & Operations
- 10% → Marketing, Partnerships, Education, Incentives
- 10% → LoopLab Allocation

These percentages are fixed in contract.

---

## 5.3 Profit Distribution Logic

After performance fee deduction:

If reward selection is USDT:
- Remaining profit is transferred to the user in USDT.

If reward selection is LOOP:
- Remaining profit is transferred to the Reward Reserve in USDT.
- LOOP is minted at the prevailing floor price.
- Minted LOOP is allocated according to the user’s compound selection:
  - 100% compound
  - 50% compound / 50% claim
  - 100% claim

Compound selections remain within vault accounting and are reflected in subsequent cycles.

---

## 5.4 LOOP Minting Constraint

LOOP may only be minted when:

- A vault generates realized profit
- User selects LOOP as reward
- Equivalent USDT is transferred into the Reward Reserve

Minting without USDT backing is not possible.

---

## 5.5 Reserve Routing Discipline

Funds within:

- Reward Reserve
- Ratchet Pool
- System Vault

May only move along contract-defined paths:

Ratchet Pool → Reward Reserve (during floor increase)  
Ratchet Pool → System Vault (excess routing)  
System Vault Profit → Reward Reserve and Ratchet Pool  
Reward Reserve → User (redemption only)

No other routing paths exist.

---

## 5.6 Loss Handling

If a vault cycle produces net loss:

- No performance fee is charged.
- No LOOP is minted.
- No reserve capital is used.
- User principal reflects the loss.

System reserves remain independent of trading losses.

---

## 5.7 Capital Invariants

At no time may:

- User principal fund reserve increases
- LOOP be minted without reserve backing
- Reserve capital be redirected to discretionary destinations
- Ratchet Pool fund withdrawals unrelated to floor adjustments

All capital flows are event-logged and publicly auditable.

---

YieldLoop’s fee and capital flow structure ensures that structural backing is derived exclusively from realized profit and not from user deposits or speculative token issuance.

---

# 6. LOOP Token Architecture

LOOP is an internal redemption instrument minted exclusively from realized vault profit.

LOOP is not externally traded and is not listed on automated market makers.  
It exists solely within the YieldLoop protocol as a mint-and-redeem accounting instrument.

---

## 6.1 Purpose of LOOP

LOOP provides users with the option to:

- Defer immediate USDT claim
- Participate in a structurally backed redemption instrument
- Benefit from permanent floor adjustments when supported by surplus

LOOP is not designed as a speculative token.  
Its value is defined by its redemption floor within the protocol.

---

## 6.2 Minting Mechanics

LOOP may only be minted when:

1. A vault produces realized profit.
2. The user selects LOOP as the reward currency.
3. Equivalent USDT (net of performance fee) is transferred to the Reward Reserve.

Minting formula:

Minted LOOP = NetProfitUSDT / CurrentFloorPrice

No LOOP may be minted without USDT backing.

---

## 6.3 Redemption Mechanics

LOOP may be redeemed for USDT at the prevailing floor price.

Redemption formula:

Redeemed USDT = LOOP Amount × CurrentFloorPrice

Upon redemption:

- LOOP is burned.
- USDT is transferred from the Reward Reserve to the user.

Redemption is funded exclusively from the Reward Reserve.

If redemption demand temporarily exceeds liquid reserve balance, redemptions enter a first-in, first-out queue.

The protocol does not mint LOOP to satisfy redemptions.

---

## 6.4 Supply Characteristics

LOOP supply expands only through minting tied to realized profit.

LOOP supply contracts through redemption and burn.

There is no pre-mint.
There is no presale allocation.
There is no team allocation.
There is no emission schedule.

Total supply is endogenous to protocol profit generation.

---

## 6.5 Floor Definition

The LOOP floor price represents the minimum redemption value per unit of LOOP.

The floor:

- May increase when ratchet conditions are met.
- May not decrease.
- Is fully funded by transfers from the Ratchet Pool to the Reward Reserve.

The floor is not market-determined.

---

## 6.6 No External Liquidity

LOOP is not paired on DEXs.
LOOP is not listed for public trading.
LOOP price discovery occurs exclusively through protocol-defined floor adjustments.

Users seeking liquidity utilize the redemption mechanism.

---

## 6.7 Compound Logic

If a user selects compound:

- LOOP remains within the vault accounting.
- Compounded LOOP participates in future floor increases.
- Compounded LOOP remains redeemable at floor.

Compound mechanics do not alter backing requirements.

---

## 6.8 Economic Boundary Conditions

LOOP cannot:

- Be minted without reserve funding
- Be redeemed above the current floor price
- Be supported by user principal
- Be inflated via governance action

LOOP’s structural integrity depends entirely on realized profit and disciplined reserve management.

---

# 7. Reserve System

YieldLoop operates a dual-reserve structure designed to ensure deterministic redemption integrity and permanent floor adjustments.

The reserve system consists of:

1. Reward Reserve  
2. Ratchet Pool  

These pools are segregated at the contract level and serve distinct economic functions.

---

## 7.1 Reward Reserve

The Reward Reserve holds USDT backing for all minted LOOP.

Purpose:

- Fund LOOP redemption
- Maintain structural backing for circulating supply

Balance Sources:

- USDT transferred during LOOP minting
- Transfers from Ratchet Pool during floor increases
- Allocations from System Vault profit routing

The Reward Reserve may only transfer funds:

- To users during LOOP redemption
- To internal accounting during ratchet floor adjustments (when required)

The Reward Reserve may not:

- Fund development
- Fund marketing
- Fund trading losses
- Be withdrawn by governance

---

## 7.2 Ratchet Pool

The Ratchet Pool accumulates 55% of all performance fees.

Purpose:

- Support permanent upward adjustment of the LOOP floor
- Provide surplus capital buffer
- Route excess capital to the System Vault when thresholds are exceeded

The Ratchet Pool may transfer funds only to:

- Reward Reserve (during floor increases)
- System Vault (excess routing)

The Ratchet Pool may not:

- Fund redemptions directly
- Fund development
- Fund discretionary withdrawals

---

## 7.3 Redemption Liability

Total redemption liability is defined as:

L = S × F

Where:

S = Total circulating LOOP supply  
F = Current floor price  

The system must maintain Reward Reserve coverage above the defined minimum coverage threshold.

Coverage ratio:

Coverage = Reward Reserve / L

Minimum immutable coverage threshold:

Coverage ≥ 1.05

Target coverage band (adjustable within range):

1.08 – 1.20

---

## 7.4 Buffer Requirement

Floor increases may only occur when:

- Coverage meets or exceeds Target Coverage
- Ratchet Pool surplus exists after satisfying buffer requirements
- Cooldown and smoothing conditions are satisfied

Buffer logic ensures that permanent floor increases do not weaken redemption integrity.

---

## 7.5 Floor Increase Funding Rule

When a floor increase ΔF is applied:

Required capital transfer:

RequiredTransfer = S × ΔF

This amount must move from Ratchet Pool to Reward Reserve.

Floor may not increase unless full funding transfer occurs.

This ensures the new floor is permanently collateralized.

---

## 7.6 Excess Routing Discipline

If either:

- Coverage exceeds upper band threshold
- Ratchet Pool surplus exceeds defined ratio threshold

Excess capital is routed exclusively to the System Vault.

No excess capital is distributed to users, marketing, or governance.

---

## 7.7 Structural Invariants

At no time may:

- User principal be used to support reserves
- Ratchet Pool fund redemptions directly
- Floor increase without capital transfer
- Coverage fall below immutable threshold due to ratchet action

Reserve mechanics are contract-enforced and publicly auditable.

---

# 8. Dynamic Ratchet Model

The Ratchet Model governs permanent upward adjustments to the LOOP redemption floor.

Floor increases are:

- Deterministic
- Fully funded
- Smoothing-controlled
- Buffer-protected
- Contract-constrained

The floor may never decrease.

---

## 8.1 Ratchet Eligibility Conditions

A ratchet event may only execute when all conditions are satisfied:

1. Coverage ≥ Target Coverage
2. Ratchet Pool surplus exists after required buffer
3. Cooldown period has elapsed
4. Required transfer funding is available

If any condition fails, no ratchet may occur.

---

## 8.2 Surplus Calculation

Define:

S = LOOP Supply
F = Current Floor
RR = Reward Reserve
RP = Ratchet Pool
L = S × F
Coverage = RR / L
SurplusPerToken = RP / S

Buffer requirement:

RequiredRR = TargetCoverage × L
BufferShortfall = max(0, RequiredRR − RR)

Usable Ratchet Capital:

UsableRP = RP − BufferShortfall

If UsableRP ≤ 0, no ratchet may execute.

---

## 8.3 Maximum Permanent Increase

Maximum permanent floor increase supported:

MaxPermanentIncrease = UsableRP / S

Floor may never increase beyond this amount.

---

## 8.4 Smoothing Function

To prevent abrupt floor volatility, ratchet increases are smoothed.

Define SmoothingFactor:

0.05 ≤ SmoothingFactor ≤ 0.30

Applied increase:

ΔF = MaxPermanentIncrease × SmoothingFactor

Additional hard cap:

ΔF ≤ 3% of Current Floor

Multisig may adjust SmoothingFactor and per-event cap within predefined ranges only.

---

## 8.5 Cooldown Logic

Ratchet events are subject to cooldown constraints.

Cooldown range:

12 hours – 72 hours

Cooldown may dynamically shorten when surplus ratio is high and lengthen when surplus is low, within contract-enforced bounds.

No ratchet may execute before cooldown elapses.

---

## 8.6 Capital Transfer Rule

When ΔF is applied:

Required capital transfer:

TransferAmount = S × ΔF

This amount is transferred from Ratchet Pool to Reward Reserve.

Floor increase is not applied until transfer completes.

---

## 8.7 Elastic Growth Behavior

The ratchet model allows accelerated floor growth when:

- Profit generation increases
- Performance fee accumulation increases
- Ratchet Pool surplus expands

However, growth remains constrained by:

- Coverage buffer
- Smoothing factor
- Cooldown interval
- Per-event cap

The model absorbs volatility rather than amplifies it.

---

## 8.8 Immutable Protections

The ratchet system cannot:

- Decrease the floor
- Execute without full funding transfer
- Use principal funds
- Override coverage minimum
- Bypass smoothing constraints

All ratchet calculations are transparent and reproducible.

---

The Dynamic Ratchet Model defines YieldLoop’s long-term stability and growth discipline.

---

# 9. System Vault (Stabilization Engine)

The System Vault is a protocol-owned capital buffer designed to absorb excess reserve capital and strengthen long-term structural backing.

The System Vault exists to:

- Prevent over-collateralization inefficiency
- Reinforce redemption integrity
- Provide surplus elasticity during volatile cycles

The System Vault does not distribute profit externally.

---

## 9.1 Capital Source

The System Vault receives capital exclusively from:

- Excess Ratchet Pool surplus
- Excess Reward Reserve surplus (if applicable)

Excess conditions are defined by contract thresholds:

- Coverage exceeds upper band
- Ratchet Pool surplus exceeds defined ratio threshold

No user principal may enter the System Vault.

---

## 9.2 Operational Constraints

The System Vault operates under strict trading rules:

- Spot trading only
- Approved asset universe only
- No leverage
- No lending
- No farming
- No external protocol exposure beyond approved DEX spot execution

Execution parameters are conservative and bounded.

Multisig may tighten constraints but may not expand beyond predefined maximum exposure limits.

---

## 9.3 Profit Routing

All realized profit generated by the System Vault must be routed to reserves.

System Vault profit allocation:

RR Allocation: 60% – 90%
RP Allocation: 10% – 40%
Total: 100%

Allocation percentages are adjustable within bounds via multisig.

No System Vault profit may be routed to:

- Development
- Marketing
- LoopLab
- Governance wallets

System Vault profit strengthens only structural reserves.

---

## 9.4 Loss Handling

If the System Vault produces a loss:

- No reserve capital is replenished
- No user principal is affected
- Loss remains isolated to System Vault capital

System Vault losses do not reduce existing Reward Reserve balances.

---

## 9.5 Capital Invariants

The System Vault may not:

- Distribute capital to users
- Distribute capital to team wallets
- Fund redemptions directly
- Be used as discretionary treasury

The System Vault serves only as a stabilization engine.

---

## 9.6 Structural Role

The System Vault creates a feedback loop:

Excess Surplus → Stabilized Deployment → Realized Profit → Reserve Strengthening

This mechanism improves capital efficiency without introducing speculative distribution behavior.

---

The System Vault completes the closed-loop architecture of YieldLoop.

# 10. NFT Structure

YieldLoop includes two NFT classes that provide performance fee discounts and limited protocol benefits.

NFTs do not grant:

- Ownership of reserves
- Access to user principal
- Claim on System Vault profit
- Governance control over immutable contract rules
- Guaranteed return rights

NFT benefits apply solely to performance fee calculation.

---

## 10.1 Genesis Lite

Genesis Lite NFTs provide a time-limited performance fee discount.

Characteristics:

- Price: 300 USDT
- Supply Cap: 3,333
- Team Allocation: 333
- Public Supply: 3,000
- Duration: 12 months from activation
- Discount: 20% reduction of applicable performance fee

Example:

If performance fee is 15% (USDT claim),
Genesis Lite holder pays 12%.

If performance fee is 10% (LOOP claim),
Genesis Lite holder pays 8%.

Genesis Lite benefits expire automatically after 12 months.
NFT remains transferable but fee benefit ceases after expiration.

---

## 10.2 Genesis Lifetime

Genesis Lifetime NFTs provide permanent performance fee discounts.

Characteristics:

- Price: 1,000 USDT
- Supply Cap: 550
- Team Allocation: 50
- Public Supply: 500
- Duration: Lifetime
- Discount: 50% reduction of applicable performance fee

Example:

If performance fee is 15% (USDT claim),
Genesis Lifetime holder pays 7.5%.

If performance fee is 10% (LOOP claim),
Genesis Lifetime holder pays 5%.

Genesis Lifetime benefits remain active as long as the NFT is held.

---

## 10.3 Discount Rules

- Discounts apply only to performance fees.
- Discounts do not alter fee allocation percentages.
- Discounts reduce total fee collected; allocation percentages apply to the reduced fee amount.
- Highest discount applies if multiple NFTs are held.
- Discounts are not stackable.
- NFT ownership is verified at cycle close to apply discount.

---

## 10.4 Economic Boundaries

NFTs do not:

- Mint LOOP
- Increase ratchet eligibility
- Influence smoothing factors
- Affect reserve buffers
- Bypass vault caps
- Override risk constraints
- Grant priority redemption

NFTs affect only performance fee percentage.

---

## 10.5 Team Allocation Constraints

Team-allocated NFTs:

- May be used for strategic partnerships or incentives
- May not alter protocol economics
- Are subject to identical fee discount rules
- Do not provide enhanced system privileges

---

NFT structure is intentionally limited to prevent reflexive token distortion while allowing aligned early participation.

# 11. Governance & Multisig Controls

YieldLoop utilizes a 3-of-5 multisignature governance model for operational parameter adjustments and emergency controls.

Governance authority is limited to bounded configuration ranges and protective actions.  
It does not include discretionary capital access.

---

## 11.1 Multisig Structure

- 3-of-5 signer requirement
- Immediate execution upon threshold approval
- All actions emit on-chain governance events
- All parameter changes publicly visible

Signers are independent operational actors.

---

## 11.2 Governance Authority Scope

Multisig may:

- Adjust bounded economic parameters within predefined ranges
- Pause trading
- Disable arbitrage module
- Prevent new deposits
- Prevent new vault creation
- Force vaults to settle at cycle end
- Tighten trading risk constraints

Multisig may not:

- Withdraw user principal
- Withdraw Reward Reserve funds
- Withdraw Ratchet Pool funds
- Withdraw System Vault funds
- Mint LOOP without reserve backing
- Reduce redemption floor
- Override ratchet funding requirements
- Disable redemption

---

## 11.3 Adjustable Economic Parameters

The following parameters may be adjusted within contract-enforced bounds:

### Target Coverage Band
- Adjustable Range: 1.08 – 1.20
- Immutable Minimum: 1.05

### Smoothing Factor
- Adjustable Range: 0.05 – 0.30

### Maximum Floor Increase Per Event
- Adjustable Range: 1% – 3%

### Ratchet Cooldown
- Adjustable Range: 12 hours – 72 hours

### System Vault Profit Split
- Reward Reserve Allocation: 60% – 90%
- Ratchet Pool Allocation: 10% – 40%
- Total must equal 100%

### Vault Capital Cap
- Adjustable Range: 25,000 – 50,000 USDT
- Immutable Maximum: 50,000 USDT

---

## 11.4 Rate Limiting

To prevent abrupt parameter shifts, daily change limits apply:

- Coverage band may change by ≤ 0.02 per 24 hours
- Smoothing factor may change by ≤ 0.05 per 24 hours
- Cooldown may change by ≤ 12 hours per 24 hours
- System Vault split may change by ≤ 10% per 24 hours

These limits are contract-enforced.

---

## 11.5 Emergency Powers

In the event of:

- DEX exploit
- Contract vulnerability
- Oracle instability
- Chain-level disruption

Multisig may:

- Immediately pause trading
- Disable arbitrage
- Disable new deposits
- Freeze new vault creation

Emergency powers may not access or redistribute capital.

---

## 11.6 Immutable Protections

The following rules are permanently locked:

- LOOP cannot be minted without USDT backing
- Floor may never decrease
- Ratchet requires full funding transfer
- User principal cannot fund reserves
- Reserve capital cannot be withdrawn
- System Vault cannot distribute to governance

Governance is bounded by contract-level invariants.

---

Governance exists to protect system integrity, not to direct capital flows.

---

# 12. Transparency & Public Dashboard

YieldLoop operates under a transparency-first model.

All critical economic metrics are publicly observable on-chain and reflected in a real-time dashboard interface.

Transparency replaces narrative assurance.

---

## 12.1 Public Dashboard Metrics

The following metrics are visible to all users:

- Current LOOP Floor Price
- Total LOOP Supply
- Reward Reserve Balance
- Ratchet Pool Balance
- System Vault Balance
- Redemption Liability (S × F)
- Coverage Ratio (Reward Reserve / Liability)
- Target Coverage Band
- Surplus Per Token (Ratchet Pool / Supply)
- Maximum Safe Floor Increase
- Last Ratchet Timestamp
- Current Cooldown Status

All calculations are deterministic and reproducible from contract state.

---

## 12.2 User Dashboard Metrics

When connected, a user may view:

- Vault NAV
- Vault principal
- Realized profit (current cycle)
- Selected reward mode
- Compound selection
- NFT discount status
- Performance fees paid (historical)
- Cycle countdown timer
- Pending redemption requests

User dashboards do not display projected returns.

---

## 12.3 Governance Log

All multisig actions are publicly logged, including:

- Parameter changed
- Previous value
- New value
- Timestamp
- Signer addresses
- Action classification (Risk, Economic, Emergency)

Governance actions are irreversible once executed.

---

## 12.4 Redemption Visibility

The system displays:

- Available immediate redemption liquidity
- Total LOOP pending redemption (if any)
- Queue status (if applicable)

This ensures users can assess liquidity conditions in real time.

---

## 12.5 On-Chain Verifiability

All balances are directly verifiable via:

- Smart contract state
- Public blockchain explorers
- Event logs

No off-chain accounting is required to verify structural integrity.

---

YieldLoop relies on visible capital accounting rather than promotional assurances.

---

# 13. Security Model

YieldLoop is designed with layered contract isolation, bounded governance authority, and strict capital segregation.

Security is enforced through architectural separation, deterministic logic, and constrained administrative control.

---

## 13.1 Contract Architecture

YieldLoop is composed of modular contracts:

- Vault contracts (isolated per user)
- Reward Reserve contract
- Ratchet Pool contract
- System Vault contract
- LOOP token contract
- Governance (multisig) control contract

Each contract has narrowly defined responsibilities.

No single contract controls all capital flows.

---

## 13.2 Capital Segregation

User vault principal is isolated and cannot be accessed by:

- Governance
- Reserve contracts
- System Vault

Reserve contracts cannot:

- Access user vault principal
- Access development allocations
- Interact with external destinations beyond defined paths

System Vault capital is segregated from both user vaults and core reserves.

---

## 13.3 Upgrade Policy

Core economic logic governing:

- LOOP minting
- Redemption
- Ratchet mechanics
- Reserve funding rules
- Immutable protections

Should be deployed as non-upgradeable contracts.

If upgradeability is implemented:

- It must be transparent
- It must be multisig-controlled
- It must be limited to non-economic components
- It must preserve economic invariants

Economic invariants may not be modified post-deployment.

---

## 13.4 Multisig Key Management

Governance is controlled by a 3-of-5 multisignature structure.

Best practices include:

- Hardware wallet usage
- Geographic distribution of signers
- No single organizational custody of all keys
- Public disclosure of signer addresses

Signers may not unilaterally execute actions.

---

## 13.5 External Dependencies

YieldLoop depends on:

- BNB Chain network integrity
- PancakeSwap and Biswap execution environments
- On-chain liquidity availability

The protocol does not rely on:

- Centralized custodians
- Off-chain price feeds for internal accounting
- Cross-chain bridges for reserve funding

DEX-level risks remain external and acknowledged.

---

## 13.6 Emergency Controls

In the event of:

- DEX exploit
- Chain instability
- Contract vulnerability discovery

Governance may:

- Pause trading
- Disable arbitrage
- Disable new deposits
- Freeze new vault creation

Governance may not withdraw capital under emergency conditions.

---

## 13.7 Audit Requirement

Prior to mainnet deployment:

- Smart contracts should undergo independent third-party audit
- Critical economic paths must be formally reviewed
- Ratchet math and reserve invariants must be verified

Audit reports should be made publicly accessible.

---

## 13.8 Risk Acknowledgment

Smart contract systems inherently carry risk including:

- Contract vulnerability
- DEX exploit
- Chain reorganization
- Liquidity fragmentation

Users accept these risks when interacting with the protocol.

---

YieldLoop’s security model emphasizes separation, bounded authority, and deterministic capital rules.

---

# 14. Economic Invariants

YieldLoop operates under a set of immutable economic invariants embedded in contract logic.

These invariants define the boundaries of system behavior and may not be altered by governance.

---

## 14.1 Principal Protection Invariant

- User principal is never used to fund reserves.
- User principal is never used to support ratchet increases.
- User principal is never used to fund System Vault activity.
- User principal is affected only by trading performance within its own vault.

Principal risk is isolated to market execution.

---

## 14.2 Backing Integrity Invariant

- LOOP cannot be minted without equivalent USDT being deposited into the Reward Reserve.
- LOOP supply expansion always increases Reward Reserve balance proportionally.
- No emission schedule exists.
- No discretionary minting is possible.

LOOP supply is strictly profit-derived.

---

## 14.3 Redemption Integrity Invariant

- Redemption is funded exclusively from the Reward Reserve.
- LOOP is burned upon redemption.
- Redemption floor cannot be reduced.
- Redemption above floor is not possible.

If insufficient liquidity exists, redemption enters a queue rather than minting additional supply.

---

## 14.4 Floor Permanence Invariant

- Floor may only increase.
- Floor increase requires full funding transfer from Ratchet Pool to Reward Reserve.
- Floor increase cannot occur without satisfying buffer requirements.
- Floor increase cannot exceed mathematically supported maximum.

Floor adjustments are permanent once applied.

---

## 14.5 Reserve Segregation Invariant

Reward Reserve may not fund:

- Development
- Marketing
- Governance withdrawals
- Trading losses

Ratchet Pool may not fund:

- Redemption directly
- Development or marketing
- Governance distributions

System Vault may not distribute capital outside reserve strengthening paths.

---

## 14.6 Governance Limitation Invariant

Multisig governance cannot:

- Withdraw reserve capital
- Lower immutable coverage minimum
- Decrease floor
- Mint LOOP without backing
- Override ratchet funding rules
- Access user vault principal

Governance authority is bounded and rate-limited.

---

## 14.7 Growth Discipline Invariant

Floor growth must always satisfy:

- Coverage ≥ immutable minimum
- Sufficient surplus funding
- Smoothing constraints
- Cooldown constraints

No ratchet may occur outside defined eligibility conditions.

---

## 14.8 Closed Loop Invariant

YieldLoop operates as a closed capital system:

User Capital → Execution → Realized Profit → Fee → Reserve Strengthening → Redemption Integrity

External speculative market forces do not influence internal floor mechanics.

---

These invariants are foundational and define YieldLoop’s structural discipline.

---

# 15. Risk Disclosures

Participation in YieldLoop involves material risk.  
Users should evaluate their risk tolerance and financial circumstances before deploying capital.

---

## 15.1 Market Risk

Vault capital is deployed into spot market trading.

Digital asset prices are volatile and may experience:

- Rapid price declines
- Liquidity contraction
- Market fragmentation
- Extended drawdowns

Trading losses directly reduce user principal.

YieldLoop does not guarantee profit.

---

## 15.2 Smart Contract Risk

YieldLoop relies on smart contracts deployed on BNB Chain.

Risks include:

- Undiscovered contract vulnerabilities
- Logic errors
- Exploits
- Integration failures

Even audited contracts may contain unknown risks.

---

## 15.3 DEX Risk

Trading execution depends on PancakeSwap and Biswap.

Risks include:

- Liquidity withdrawal
- Exploits or protocol failure
- Slippage beyond expectation
- Front-running or MEV conditions

YieldLoop does not control external DEX infrastructure.

---

## 15.4 Chain Risk

YieldLoop depends on BNB Chain network integrity.

Risks include:

- Chain congestion
- Reorganizations
- Validator instability
- Network outages

Chain-level issues may temporarily disrupt trading or redemption.

---

## 15.5 Redemption Liquidity Risk

Redemption is funded by the Reward Reserve.

Under extreme conditions:

- High redemption demand may exceed liquid availability
- Redemptions may enter queue processing
- Temporary delays may occur

YieldLoop does not guarantee instant liquidity under all conditions.

---

## 15.6 Governance Risk

YieldLoop utilizes a 3-of-5 multisignature governance structure.

Risks include:

- Signer compromise
- Coordination failure
- Delayed emergency response

Governance authority is bounded but not eliminated.

---

## 15.7 Regulatory Risk

Regulatory treatment of digital asset systems may evolve.

Changes in:

- Securities law interpretation
- Digital asset regulation
- Tax treatment
- Jurisdictional enforcement

May impact the operation or accessibility of YieldLoop.

Users are responsible for compliance with their local laws.

---

## 15.8 System Vault Risk

System Vault capital is subject to market risk.

Losses in the System Vault may:

- Reduce surplus generation
- Slow floor growth
- Reduce reserve strengthening pace

System Vault losses do not impact user principal directly.

---

## 15.9 NFT Risk

NFT ownership provides fee discounts only.

NFT value is not guaranteed and may fluctuate based on market demand.

NFTs do not represent equity or revenue rights.

---

## 15.10 No Guarantee

YieldLoop does not guarantee:

- Profit
- Floor increase frequency
- Liquidity speed
- Economic growth rate

All participation is voluntary and at risk.

---

Users acknowledge that digital asset systems carry inherent volatility and technological risk.

---

# 16. Legal & Structural Notes

YieldLoop is developed and operated by YieldLoop LLC.

The protocol may utilize affiliated entities for intellectual property holding, software development, or operational functions where appropriate.

---

## 16.1 Corporate Structure

YieldLoop LLC operates the protocol interface, development, and operational oversight.

Intellectual property related to the protocol may be held by a separate legal entity.

Corporate structure does not grant ownership rights over user vault capital.

---

## 16.2 Protocol Nature

YieldLoop is a smart contract-based capital allocation framework.

Users:

- Deploy capital voluntarily
- Select or approve trading configurations
- Assume trading risk
- Elect reward mode (USDT or LOOP)

The protocol does not:

- Guarantee returns
- Guarantee floor increase timing
- Guarantee liquidity under all conditions
- Provide individualized financial advice

All trading is executed under user-approved parameters.

---

## 16.3 LOOP Characterization

LOOP is an internal mint-and-redeem instrument.

LOOP:

- Is minted exclusively from realized profit
- Is fully backed by USDT reserves
- Is redeemable at a protocol-defined floor
- Is not externally traded by protocol design

LOOP does not represent:

- Equity ownership
- Governance control over immutable logic
- Revenue-sharing rights
- Claim on development allocations

---

## 16.4 NFT Characterization

Genesis Lite and Genesis Lifetime NFTs:

- Provide performance fee discounts
- Do not grant equity
- Do not grant profit share
- Do not grant reserve access
- Do not grant treasury rights

NFTs are utility-based fee instruments.

---

## 16.5 User Responsibility

Users are responsible for:

- Understanding smart contract interaction
- Managing wallet security
- Evaluating trading risk
- Compliance with local tax and regulatory obligations

YieldLoop does not provide tax advice or legal advice.

---

## 16.6 Regulatory Considerations

Digital asset regulation varies by jurisdiction.

Users access the protocol at their own discretion and risk.

YieldLoop LLC may restrict access in certain jurisdictions where required.

---

## 16.7 Limitation of Liability

Interaction with smart contracts involves technological risk.

To the maximum extent permitted by applicable law:

- YieldLoop LLC is not liable for trading losses
- YieldLoop LLC is not liable for smart contract exploits beyond negligence standards
- YieldLoop LLC is not liable for external DEX failures
- YieldLoop LLC is not liable for chain-level disruptions

Users accept protocol risk as a condition of interaction.

---

YieldLoop is structured as a rule-bound financial protocol operating through deterministic smart contract logic.

---

# 17. Roadmap

YieldLoop development follows a phased deployment strategy focused on structural integrity, audit discipline, and controlled release.

Timelines may adjust based on security review and market conditions.

---

## Phase I — Architecture Finalization

- Complete formal whitepaper specification
- Finalize smart contract logic and invariants
- Internal economic simulation of reserve mechanics
- Stress testing of ratchet dynamics under varying volatility scenarios
- Multisig governance structure formation

---

## Phase II — Smart Contract Development

- Vault contract implementation
- LOOP token implementation
- Reward Reserve and Ratchet Pool contracts
- System Vault contract
- Governance and parameter constraint enforcement
- Event logging and transparency interfaces

All contracts built with modular separation and bounded authority.

---

## Phase III — Security Audit

- Independent third-party contract audit
- Ratchet math verification
- Reserve invariant verification
- Governance boundary validation
- Critical bug remediation

Audit results to be publicly disclosed.

---

## Phase IV — Public Dashboard Deployment

- Real-time reserve reporting
- Ratchet transparency metrics
- Governance event log interface
- Redemption queue visibility
- Vault performance display

No projected yield metrics included.

---

## Phase V — Controlled Mainnet Launch

- Limited vault cap during initial deployment
- Conservative parameter defaults
- Trading engine conservative constraints
- Monitoring of execution behavior
- Continuous reporting transparency

---

## Phase VI — Gradual Expansion

- Increase vault cap within contract bounds (if warranted)
- Expand educational materials
- Strategic partnerships
- LoopLab development and operational integration
- Additional asset evaluation (within risk framework)

All expansion subject to structural safety constraints.

---

## Long-Term Objective

YieldLoop aims to operate as:

- A disciplined capital allocation protocol
- A structurally backed internal redemption system
- A volatility-absorbing financial framework
- A transparent, bounded economic engine

Growth is expected to occur organically as realized profit accumulates and reserve backing strengthens.

No guarantees are made regarding speed of expansion.

---

The roadmap prioritizes durability over velocity.

