# YieldLoop Genesis NFT Program — Minting + Staking + Discounts + Rewards (Spec)
**Version:** 1.0  
**Status:** REQUIRED IMPLEMENTATION SPEC (MVP-Ready)  
**Audience:** Smart contract + backend engineers, app engineers  
**Owner:** Todd Koletsky  

---

## 0. Purpose (What this is)
This document defines the complete Genesis NFT program for YieldLoop:
- Minting and supply rules
- Pricing ladder (fixed schedule)
- Team allocation handling
- Staking NFTs to a *specific vault*
- Per-vault performance-fee discounts (capped)
- Genesis Reward Pool funding (from mint proceeds)
- Reward distribution mechanics (vault-bound)
- Transfer/unstake rules (no protocol marketplace)
- Required events, state, and safety guardrails

This spec is designed to be:
- Simple
- Abuse-resistant
- Deterministic
- “Boring is good”

This NFT program must **not**:
- Promise returns
- Guarantee profits
- Create emissions
- Bypass YieldLoop safety/guardrails

---

## 1. Supply + Allocation
### 1.1 Total Supply
- **Total Genesis NFTs:** 3,300

### 1.2 Public vs Team Allocation
- **Public Mint (For Sale):** 3,000
- **Team/Community Allocation (Not sold by protocol):** 300  
  Intended usage:
  - community service partnerships
  - contributor incentives
  - bug bounties
  - marketing/community growth
  - grants/accelerator partnership obligations

**Hard rule:** the protocol must not run an internal marketplace or liquidity program.

---

## 2. Mint Pricing (Fixed Ladder)
Only NFTs **#1–#3000** are sold by the mint contract.  
The price must range from **$10 to $50** using a fixed tranche schedule.

### 2.1 Recommended Ladder (6 tranches × 500 NFTs)
- 1–500: **$10**
- 501–1000: **$15**
- 1001–1500: **$20**
- 1501–2000: **$30**
- 2001–2500: **$40**
- 2501–3000: **$50**

### 2.2 Non-Negotiable Rules
- The ladder is published **before mint**.
- The ladder **never changes**.
- No Dutch auction.
- No “dynamic pricing” based on demand.

---

## 3. Mint Payment Asset + Accounting
### 3.1 Payment Asset
Choose one:
- **USDT (BEP-20)** preferred for clean accounting, or
- **BNB** if grant/UX requires it

If BNB is used:
- mint contract must convert proceeds to USDT via a defined route (or route to treasury and convert off-path with explicit accounting).

### 3.2 Proceeds Split (On Mint)
On every public mint payment:
- **25%** → Genesis Reward Pool Principal (on-chain treasury bucket)
- **75%** → Operations Treasury (dev, audit, infra, marketing, partnerships)

> Note: This is a routing rule, not a profit promise. Pool performance may be zero.

---

## 4. Genesis Reward Pool (What it is / what it is not)
### 4.1 Definition
The Genesis Reward Pool is a protocol-owned pool funded by **25% of public mint proceeds**.  
Its purpose is to create a long-lived, conservative incentive stream for Genesis NFT holders.

### 4.2 Investment Policy (Conservative)
- Pool capital is deployed into YieldLoop using a **conservative configuration**
- Pool payout mode:
  - **50% compound**
  - **50% withdraw to distributable rewards**
- If no profitable opportunities exist:
  - the pool remains idle
  - distributions may be **zero**

### 4.3 Non-Promise Statement (Hard)
The system must never market or represent this as guaranteed yield, dividends, or passive income.

---

## 5. NFT Utility Overview (What owners get)
Genesis NFTs provide:
1) **Per-vault performance-fee discount** (requires staking to that vault)
2) **Vault-bound reward deposits** (requires staking to that vault)
3) **Discord access / feature testing / dedicated team channel**
4) Optional: early previews, bug bounty eligibility, feedback influence

NFTs do **not** provide:
- governance rights (unless explicitly added later and documented)
- ownership of protocol revenue
- guaranteed payouts

---

## 6. Vault-Bound Staking Model (Core Design)
### 6.1 Why vault-bound
All benefits are scoped to a **specific vaultId**.  
This prevents:
- global wallet gaming
- fee abuse
- “one NFT benefits infinite vaults” exploits

### 6.2 Staking requirement
To activate discounts and rewards:
- NFT must be **staked** to a specific `vaultId`.

### 6.3 One NFT → One vault at a time
- Each NFT can be staked to **exactly one** vault at a time.
- Reassignment requires:
  - unstake
  - cooldown
  - stake to new vault

---

## 7. Fee Discount Rules (Per Vault)
### 7.1 Discount Rate
- **0.2% additional fee discount per staked NFT**
- **Cap:** **5.0% max** discount per vault  
  (meaning max benefit at **25 NFTs** staked to that vault)

### 7.2 What fee does it reduce?
- Discount applies only to:
  - **YieldLoop performance fees on realized profit**
- Discount does **not** apply to:
  - principal
  - withdrawals
  - closeouts
  - any loss scenario
  - any non-performance fee (if introduced later)

### 7.3 Discount Snapshot Timing (Important)
Discount is computed using a **settlement snapshot**:
- the discount used for a realized profit settlement is the discount state **at the time the profit is realized/settled** (implementation-defined boundary, but must be deterministic and auditable)
- discount must not change mid-trade

### 7.4 No priority / no execution advantage
Discount must not:
- change execution priority
- alter guardrails
- bypass throttles
- bypass safety controls

---

## 8. Reward Distribution Rules (Vault-Bound)
### 8.1 Reward source
Rewards distributed to Genesis NFTs come from:
- the **withdrawable portion** of the Genesis Reward Pool’s realized profits

No emissions. No minting. No fake yield.

### 8.2 Where rewards go
Rewards are deposited to the **vault’s claim ledger** for the vault to which NFTs are staked.

Interpretation:
- If you stake NFTs to Vault #123, rewards route to Vault #123’s reward-credit bucket.

### 8.3 How rewards are used by the vault owner
Reward credits follow the vault’s configured profit routing:
- User may withdraw rewards (if enabled by product rules)
- Or compound rewards into the vault (if the vault is configured to compound)

**Hard rule:** rewards must not bypass settlement logic or guardrails.

### 8.4 Pro-rata rule
Rewards are distributed pro-rata by:
- number of NFTs staked to a vault
- relative to total NFTs staked across all vaults

Example:
- total staked NFTs across protocol: 1,000
- vault A has 10 staked NFTs
- vault A gets 1% of that distribution cycle’s distributable rewards

### 8.5 No stake = no rewards
- Unstaked NFTs receive **no rewards**.
- Rewards are always directed to active, staked placements.

---

## 9. Stake / Unstake / Transfer Rules
### 9.1 Unstake required for transfer
To transfer an NFT:
- it must be **unstaked first**

### 9.2 Cooldowns (Anti-abuse)
Required cooldowns:
- **Stake activation delay:** staking becomes active after `stakeActivationDelay` (recommended: 6–24 hours)
- **Unstake cooldown:** after unstake, the NFT cannot be staked again for `restakeCooldown` (recommended: 24 hours)

Purpose:
- prevents flash-stake fee manipulation
- prevents “borrow NFT for settlement” behavior

### 9.3 Discount removal
When unstaked:
- discount benefit is removed immediately for future settlements

### 9.4 No protocol marketplace
- No in-app buying/selling
- No liquidity pools
- No “floor price support”
Users may transfer peer-to-peer at their own discretion.

---

## 10. Required On-Chain Components
### 10.1 Contracts
1) **GenesisNFT (ERC-721)**
   - mint for #1–#3000
   - admin mint/claim for #3001–#3300 (team/community allocation)
   - baseURI management (timelocked)

2) **NFTVaultStaking**
   - stake NFT to `vaultId`
   - unstake NFT
   - query: NFTs staked per vault, per user
   - enforces cooldowns
   - emits required events

3) **GenesisRewardTreasury**
   - holds 25% mint proceeds (principal)
   - tracks distributable rewards vs principal
   - does not promise returns

4) **GenesisRewardDistributor**
   - takes distributable rewards (USDT)
   - computes per-vault share by staked NFT weight
   - credits rewards into the vault claim ledger (or calls a YieldLoop vault contract method to credit)
   - distribution can be periodic or on-demand, but must be deterministic and auditable

5) **DiscountOracle (or library)**
   - reads staking state to compute per-vault discount
   - used at settlement to snapshot discount

### 10.2 Integration points with YieldLoop
YieldLoop settlement must have an integration point such as:
- `getVaultFeeDiscountBps(vaultId) -> uint16`
- `creditVaultGenesisReward(vaultId, amountUSDT)`

---

## 11. Admin + Governance Controls (Safety)
All sensitive actions must be:
- multisig-controlled
- timelocked (recommended 72–96 hours)

### 11.1 Timelocked actions
- changing baseURI / metadata reveal
- changing treasury/distributor addresses (if upgradeable)
- changing cooldown params (within bounds)
- pausing mint
- pausing staking
- pausing distribution

### 11.2 Emergency actions (no timelock)
Emergency pause allowed for:
- minting
- staking
- distribution

Emergency actions must not:
- move user vault funds
- redirect rewards to arbitrary addresses
- change fee models

---

## 12. Events (Required)
### 12.1 Mint events
- `GenesisMint(address buyer, uint256 quantity, uint256 totalPaid, uint256 tranchePrice)`
- `TeamMint(address to, uint256 tokenId)` (or batch)

### 12.2 Staking events
- `NFTStaked(address owner, uint256 tokenId, uint256 vaultId, uint64 activeAtUTC)`
- `NFTUnstaked(address owner, uint256 tokenId, uint256 vaultId, uint64 cooldownUntilUTC)`
- `VaultDiscountSnapshot(uint256 vaultId, uint16 discountBps, uint64 timestampUTC)`

### 12.3 Rewards events
- `GenesisRewardsDistributed(uint256 totalAmountUSDT, uint256 totalStakedNFTs, uint64 timestampUTC)`
- `VaultGenesisRewardCredited(uint256 vaultId, uint256 amountUSDT, uint256 stakedNFTsForVault)`

### 12.4 Pauses
- `MintPaused(bool isPaused)`
- `StakingPaused(bool isPaused)`
- `DistributionPaused(bool isPaused)`

---

## 13. State + Query Requirements (Frontend needs these)
The DApp must be able to query:
- current mint tranche price
- remaining supply for sale
- user’s owned NFTs
- user’s staked NFTs by vault
- vault’s current discount (estimated) + last snapshot
- vault’s lifetime Genesis rewards credited
- global total staked NFTs
- last distribution timestamp
- current pause status

---

## 14. Edge Cases + Failure Modes (Must handle cleanly)
- If distribution fails mid-way:
  - fail closed
  - do not partially credit without recording
  - allow retry with idempotent distribution ids
- If vault is closed:
  - staking to that vault should be blocked
  - rewards should not be credited to a closed vault
  - user must unstake/reassign
- If YieldLoop is paused:
  - staking may remain available (optional), but:
  - discount snapshots and reward credits must remain consistent
- If reward pool produces no profit:
  - distribution is zero
  - system must not “make up” rewards

---

## 15. Implementation Notes (Strong recommendations)
- Use USDT accounting for all reward flows.
- Keep the staking contract simple and auditable.
- Prevent re-entrancy in any function that transfers tokens.
- Make distribution idempotent (use `distributionId` + `claimed/credited` flags).
- Consider non-transferable mode as an optional future feature, but **do not** require it for Genesis v1.

---

## 16. “Boring is Good” Public Summary (for docs/UI)
Genesis NFTs are optional.
They provide:
- per-vault fee discounts (capped)
- vault-bound reward credits (only if pool profits exist)
- community + testing access

No guarantees. No emissions. No hype.
If profits are zero, rewards are zero.
