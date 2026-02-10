# YieldLoop — Vault Isolation + Multi-Vault “Brains” Explainer (v1.2)

Purpose: Explain (1) how vaults are separate, (2) how they are generated and managed, and (3) why the system must support more than one vault per user.

---

## 1. What a Vault Is (Not a UI Concept)

A YieldLoop vault is an on-chain accounting container with deterministic rules. It is the backbone of:
- correct profit attribution
- settlement correctness
- safe withdrawals
- audit traceability

A vault is not a “pooled fund” and not a “sub-account in a database.” It is an enforceable isolation boundary.  [oai_citation:0‡YieldLoopMVP.pdf](sediment://file_00000000bb5471f88a4d250d8c785d4d)

---

## 2. Why Vaults Must Be Isolated

Each vault is isolated by design:
- vault balances are not pooled with other vaults
- vault accounting is deterministic per vault
- fees and profit are calculated per vault
- guardrails evaluate vault-level risk and behavior  [oai_citation:1‡YieldLoopMVP.pdf](sediment://file_00000000bb5471f88a4d250d8c785d4d)

This ensures:
- clean attribution of performance
- fairness between users
- minimized cross-user contagion
- simplified audits and forensic traceability  [oai_citation:2‡YieldLoopMVP.pdf](sediment://file_00000000bb5471f88a4d250d8c785d4d)

**Practical consequence:**  
If Vault A breaks, pauses, or suffers execution failure, Vault B does not inherit its exposure. Isolation prevents “one user’s risk contaminates everyone.”

---

## 3. Vault Ownership (Who Controls What)

A vault is always bound to the wallet address that created it:
- the user wallet is the vault controller
- profits and withdrawals route only to the vault owner
- protocol cannot redirect vault assets to arbitrary addresses
- vault behavior is constrained to user config + protocol guardrails  [oai_citation:3‡YieldLoopMVP.pdf](sediment://file_00000000bb5471f88a4d250d8c785d4d)

This is the on-chain enforcement layer that makes the product defensible and user-trustable.

---

## 4. How Vaults Are Created (“Generated”)

YieldLoop supports multiple vaults per user by allowing multiple deposit instances.  [oai_citation:4‡YieldLoopMVP.pdf](sediment://file_00000000bb5471f88a4d250d8c785d4d)

Creation sequence (conceptual):
1) User deposits the minimum base asset amount required to create a new vault (v1.2: **$1,000 USDT initial deposit**)
   - Subsequent deposits into an existing vault follow the protocol minimums:
     - Standard: **$250**
     - Basic Genesis NFT: **$175**
     - Lifetime Genesis NFT: **$100**

Note: These minimums affect deposit convenience only and do not modify execution behavior, strategy selection, fees, or profit mechanics.

[oai_citation:5‡YieldLoopMVP.pdf](sediment://file_00000000bb5471f88a4d250d8c785d4d)  
2) Protocol assigns a new unique `vaultId`  
3) Protocol stores the VaultLedger record for that `vaultId` (see Section 5)  
4) Vault is bound to `owner = msg.sender`  [oai_citation:6‡YieldLoopMVP Appendix.pdf](sediment://file_000000007a3871fda2e6fcd1db40ccad)  
5) Vault starts in a state that requires configuration before execution (see state rules below)

**Result:** each deposit creates a new vault container, not a “top-up” to a shared pool.

---

## 5. How Vaults Stay Separate (The Ledger “Buckets”)

Each vault must track balances in separate internal buckets (ledger separation).  [oai_citation:7‡YieldLoopMVP.pdf](sediment://file_00000000bb5471f88a4d250d8c785d4d)

Required separation (conceptual):
1) Principal balance (remaining principal basis)
2) Active position balance (currently deployed)
3) Unrealized position value (UI only; not profit)
4) Realized profit (only on close + settlement)
5) Withdrawable profit (USDT; withdraw anytime)
6) Suspense profit (USDT; reserved for LOOP payout mode)  [oai_citation:8‡YieldLoopMVP.pdf](sediment://file_00000000bb5471f88a4d250d8c785d4d)

This prevents:
- profit mislabeling
- double counting
- withdrawal exploits
- reward disputes
- false performance reporting  [oai_citation:9‡YieldLoopMVP.pdf](sediment://file_00000000bb5471f88a4d250d8c785d4d)

### 5.1 VaultLedger (Recommended Required Fields)
The ledger is per-vault, and must include at minimum:
- `vaultId` unique ID
- `owner` wallet address
- `baseAsset` deposit asset (must be whitelisted)
- `settlementAsset` must be USDT
- `status` state machine
- principal / profit / suspense fields
- strategy hash binding for execution  [oai_citation:10‡YieldLoopMVP Appendix.pdf](sediment://file_000000007a3871fda2e6fcd1db40ccad)  [oai_citation:11‡YieldLoopMVP Appendix.pdf](sediment://file_000000007a3871fda2e6fcd1db40ccad)

### 5.2 Profit Is USDT Only (Hard Rule)
Withdrawable profit is always stored as USDT only.  [oai_citation:12‡YieldLoopMVP Appendix.pdf](sediment://file_000000007a3871fda2e6fcd1db40ccad)

Profit cannot “exist” as BTCB/ETH/SOL/XRP/BNB in the withdrawable ledger.

### 5.3 NAV Is Not Profit (Hard Rule)
NAV may rise or fall. It is not profit until realized.  
NAV increases do not create withdrawable profit.  [oai_citation:13‡YieldLoopMVP Appendix.pdf](sediment://file_000000007a3871fda2e6fcd1db40ccad)

---

## 6. How Vaults Are Managed (The “Brains” That Coordinate Many Vaults)

A multi-vault protocol needs coordination. The “brains” are the combined system components that make multiple isolated vaults run safely and consistently:

### 6.1 Vault Registry / Index (Brain 1)
Maintains:
- mapping of vaultId → owner
- list of vaults per owner
- enumeration for UI and indexer

Purpose:
- support multiple independent vaults per user
- allow the dashboard to display vault list reliably

### 6.2 Vault State Machine Controller (Brain 2)
Enforces sequential, state-safe actions:
- no overlapping settlement routines
- vault actions must be sequential and state-safe
- withdrawals cannot exceed withdrawable profit ledger  [oai_citation:14‡YieldLoopMVP.pdf](sediment://file_00000000c9b871f89ce00ee3dca449f3)

Purpose:
- prevent reentrancy and accounting corruption
- prevent “double settlement” and “double withdrawal” exploits

### 6.3 Strategy Proposal + Hash Approval Binder (Brain 3)
For each vault:
- a strategy is proposed
- user approves it
- execution is hash-bound to that approval
- execution without approval is prohibited (non-negotiable)  [oai_citation:15‡YieldLoopMVP Appendix.pdf](sediment://file_000000007a3871fda2e6fcd1db40ccad)

Purpose:
- user control per vault
- prevents silent strategy swaps
- makes execution audit-verifiable per vault

### 6.4 Execution Router (Brain 4)
Executes only within allowed venues and approved bounds:
- PCS and BiSwap only (v1.2)
- no third-party DEX routing allowed  [oai_citation:16‡YieldLoopMVP Appendix.pdf](sediment://file_000000007a3871fda2e6fcd1db40ccad)

Purpose:
- makes performance consistent
- reduces exploit surface area
- keeps system predictable across many vaults

### 6.5 Accounting + Settlement Engine (Brain 5)
Runs deterministic settlement sequence per vault:
- settlement begins only when trade cycle complete
- fixed order steps:
  - snapshot balances
  - compute realized result in USDT
  - compute costs
  - compute profit
  - compute fee
  - route fee
  - credit ledgers
  - emit ledger snapshot  [oai_citation:17‡YieldLoopMVP Appendix.pdf](sediment://file_000000007a3871fda2e6fcd1db40ccad)

Purpose:
- guarantees profit correctness per vault
- prevents “profit printing”
- enforces USDT profit-only rule

### 6.6 Guardrails + Integrity Rules (Brain 6)
Per-vault enforcement:
- sequential and state-safe actions
- no overlapping settlement
- anti-reentrancy
- withdrawal caps at withdrawable ledger
- suspense profit cannot be withdrawn as USDT while LOOP payout mode is active for the month  [oai_citation:18‡YieldLoopMVP.pdf](sediment://file_00000000c9b871f89ce00ee3dca449f3)

Purpose:
- keeps many vaults safe even when one vault hits edge cases

---

## 7. Why Multiple Vaults Are Required (Not Optional)

YieldLoop v1.2 supports multiple vaults per user because each vault is:
- independent
- separately configured
- separately settled
- separately reported  [oai_citation:19‡YieldLoopMVP.pdf](sediment://file_00000000bb5471f88a4d250d8c785d4d)

YieldLoop v1.2 explicitly does NOT use “sub-vault baskets” inside one vault.  
It supports multiple vaults instead of multiple baskets.  [oai_citation:20‡YieldLoopMVP.pdf](sediment://file_00000000bb5471f88a4d250d8c785d4d)

### 7.1 What multi-vault enables immediately
- Different allocations per vault
- Different payout method per vault (USDT vs LOOP)
- Different compounding preference per vault
- Clean performance attribution and reporting per strategy instance
- Safe “try a new configuration” without contaminating the old vault

### 7.2 Why “one pooled fund” breaks the product
A pool:
- mixes user outcomes
- blurs accounting
- complicates withdrawal fairness
- creates cross-user risk contagion

Vault isolation is a core product requirement, not a feature.

---

## 8. Closeout and Withdrawals (Per Vault)

User may request closeout at any time. Closeout requires:
- unwind/settlement of open positions
- completion of final profit settlement
- routing remaining principal + realized profits to user wallet  [oai_citation:21‡YieldLoopMVP.pdf](sediment://file_00000000bb5471f88a4d250d8c785d4d)

“Withdraw profits anytime” applies only to realized profit.  
Principal withdrawal requires closeout/unwind.  [oai_citation:22‡YieldLoopMVP.pdf](sediment://file_00000000bb5471f88a4d250d8c785d4d)

---

## 9. Bottom Line

YieldLoop is not “a bot running on user funds.”
It is “many isolated vault containers, each with deterministic accounting, hash-bound approval, and settlement rules.”

Multi-vault is required because:
- each deposit is an independent strategy instance
- user configuration must be vault-specific
- settlement and withdrawals must be vault-specific
- audits and traceability must remain clean per vault
- cross-user contagion must be structurally impossible
