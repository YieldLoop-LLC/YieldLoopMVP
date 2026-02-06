# YieldLoop Emergency Withdrawal + LOOP Redemption + Liveness Safety (v1.0)

Status: REQUIRED IMPLEMENTATION  
Applies To: YieldLoop v1.x  
Audience: Smart Contract + Backend Engineers  
Developer must not deviate from this specification.

References:
- LOOP Receipt / Redemption Token Model (Hard Invariants)   [oai_citation:4‡LOOP Redemption Model.pdf](sediment://file_0000000022a071f5bca60a0da1384781)
- Failure Modes & Emergency Behavior (Fail Closed Philosophy)  [oai_citation:5‡Failure modes and emergency’s.pdf](sediment://file_00000000d99871f5aaa53bed7112ce3e)
- Governance + Timelock Specification (Admin Limits)  [oai_citation:6‡Governance and timelock.pdf](sediment://file_00000000d32871f58bbc38ed325c1eab)
- MVP Appendix (State Machine, Parameters, Dust, Guardrails)  [oai_citation:7‡YieldLoopMVP Appendix.pdf](sediment://file_000000009e40722faa7f6ba01d0cfcf3)

---

## 1. Purpose

This document defines the complete behavior for:
- user emergency withdrawal (USDT-only)
- LOOP redemption mechanics (solvency + atomicity)
- liveness handling (congestion, gas, infra outages)
- edge-case failure containment (partials, dust, deadlocks)
- admin/governance constraints during emergencies

Goals:
- Solvency is always enforced.
- Liveness failures are handled explicitly (queued, retried, observable).
- No hidden “hero mode” behaviors.
- Users always retain a path to exit.
- The system fails closed (pause > gamble).  [oai_citation:8‡Failure modes and emergency’s.pdf](sediment://file_00000000d99871f5aaa53bed7112ce3e)

---

## 2. Key Distinction: Solvency vs Liveness

### 2.1 Solvency (Accounting Safety)
Solvency means:
- claims are backed by assets on-chain
- invariants enforce “no undercollateralized receipts”  [oai_citation:9‡LOOP Redemption Model.pdf](sediment://file_0000000022a071f5bca60a0da1384781)

### 2.2 Liveness (Execution Availability)
Liveness means:
- the user can get a transaction included
- swaps can clear in current market/gas conditions
- RPC/indexer availability does not block access

Solvency DOES NOT imply liveness.

This protocol MUST handle both.

---

## 3. Hard Invariants (Non-Negotiable / Must Revert)

### 3.1 LOOP Receipt Invariants
- LOOP is minted only against realized net profit.  [oai_citation:10‡LOOP Redemption Model.pdf](sediment://file_0000000022a071f5bca60a0da1384781)
- LOOP supply MUST NOT exceed Redemption Pool USDT.  [oai_citation:11‡LOOP Redemption Model.pdf](sediment://file_0000000022a071f5bca60a0da1384781)
- LOOP redemption MUST NOT exceed Redemption Pool USDT.  [oai_citation:12‡LOOP Redemption Model.pdf](sediment://file_0000000022a071f5bca60a0da1384781)
- Admin/governance MUST NOT mint LOOP. 

### 3.2 User Fund Control Invariants
- Protocol/admin cannot redirect vault assets to arbitrary addresses.  [oai_citation:13‡Governance and timelock.pdf](sediment://file_00000000d32871f58bbc38ed325c1eab)
- Transfers out of a vault may only go to vault.owner (or protocol-defined pools *only if explicitly permitted*).
- No performance fees on losses. 

### 3.3 Execution Safety Invariants
- Trades/swaps may only occur in EXECUTING or CLOSE_PENDING.  [oai_citation:14‡YieldLoopMVP Appendix.pdf](sediment://file_000000009e40722faa7f6ba01d0cfcf3)
- Emergency closeout MUST obey guardrails and fail closed. 

---

## 4. LOOP Redemption (Atomic, Race-Proof)

### 4.1 Atomic Burn → Transfer
Redemption MUST be a single atomic call:

1) require(poolUSDT >= amountUSDT)
2) burn LOOP (user)
3) transfer USDT (pool -> user)

No intermediate state. No separate “approve + claim” splitting inside protocol.
No loops that can be partially processed.

### 4.2 No Concurrent Overdraw
Implementation MUST be race-safe in same block:
- the pool balance check must be evaluated inside the atomic function
- no external calls between check and transfer

### 4.3 LOOP Transferability (Receipt Token Reality)
To preserve “receipt token” semantics and reduce regulatory/UX ambiguity:
- LOOP SHOULD be non-transferable in v1 (recommended hard lock):
  - allow only mint-to-user and burn-from-user flows
  - disallow arbitrary ERC20 transfers
If transferability is permitted, it MUST be explicitly justified and documented.

---

## 5. Emergency Withdrawal (USDT-Only, Loss Possible)

### 5.1 User Right to Emergency Withdraw
User may request emergency withdrawal at any time.

Emergency Withdrawal characteristics:
- output asset: USDT only
- fee tier: USDT fee rate (15%) applied to realized profit portion
- liquidation: positions may be closed “as-is” (loss possible)
- no guarantee of principal preservation

### 5.2 Emergency Withdrawal Ignores Payout Mode
Regardless of whether the user selected LOOP payout:
- principal + active positions + suspense profit (if any) are unwound to USDT
- settlement output is USDT to user
- USDT tier fee applies (15%)

This must be explicit to prevent disputes.

---

## 6. Emergency Modes (Two-Tier)

### 6.1 Soft Emergency (Per-Vault Default)
Flow:
- emergencyWithdraw(vaultId) sets vault.state -> CLOSE_REQUESTED
- system transitions -> CLOSE_PENDING and attempts unwind to USDT
- once fully USDT, CloseoutCompleted, then transfer to user

### 6.2 Hard Emergency (Global Flag)
When globalEmergency == true:
- trading disabled
- new deposits disabled
- emergency withdrawals are accepted but processed via FIFO queue
- retries occur under safe conditions

Rationale: prevents “everyone spam closes” causing chain-level failure.

---

## 7. Liveness Handling (Gas Wars, Congestion, Infra Failures)

### 7.1 Gas Spike Handling
If estimated gas (converted to USDT) exceeds maxGasUSDT:
- do not attempt unwind
- vault enters EMERGENCY_QUEUE_GAS
- retry on schedule until gas normalizes

### 7.2 Congestion / Inclusion Failures
If tx repeatedly fails to land:
- vault remains EMERGENCY_QUEUE_INCLUSION
- no “force” mechanism exists on-chain
- user is informed via events + UI

### 7.3 RPC / Indexer / Frontend Outages
Protocol must remain operable via direct chain interaction:
- users can call emergencyWithdraw and redeem directly on-chain
- frontend outage must not prevent contract-level exits  [oai_citation:15‡Failure modes and emergency’s.pdf](sediment://file_00000000d99871f5aaa53bed7112ce3e)

---

## 8. Emergency Closeout Swaps (Oracle/DEX Safety)

### 8.1 Oracle Failure or Staleness
If oracle is stale or deviating beyond bounds:
- trading blocked
- emergency closeout swaps are blocked
- vault enters EMERGENCY_QUEUE_ORACLE
- do not “guess” prices
This is consistent with fail-closed behavior.  [oai_citation:16‡Failure modes and emergency’s.pdf](sediment://file_00000000d99871f5aaa53bed7112ce3e)

### 8.2 DEX Liquidity Collapse / Routing Failures
If routes fail guardrails (liquidity/impact/slippage):
- do not execute
- vault enters EMERGENCY_QUEUE_DEX
- retry later
No routing to unsupported venues.  [oai_citation:17‡Failure modes and emergency’s.pdf](sediment://file_00000000d99871f5aaa53bed7112ce3e)

### 8.3 Approved Fallback Pricing (If any)
If and only if existing specs allow fallback to DEX TWAP:
- TWAP window must be defined
- deviation caps must be enforced
- otherwise block and queue

---

## 9. Partial Unwind / Half-Completed Closeouts

If unwind is partially executed:
- vault enters CLOSE_PENDING_PARTIAL
- resulting assets remain in vault
- subsequent retries unwind remaining balances to USDT

No attempts to “reverse” trades.
No discretionary rescue actions.

---

## 10. Dust Handling (Closeout Must Not Deadlock)

Define:
- dustThresholdUSDT = 0.50 (or as specified)  [oai_citation:18‡YieldLoopMVP Appendix.pdf](sediment://file_000000009e40722faa7f6ba01d0cfcf3)

Rules:
- balances below dustThresholdUSDT must not block closeout completion
- dust must be logged
- dust may be swept to Ratchet Reserve (recommended) and emitted as event

---

## 11. Reentrancy / State Safety

All functions in scope MUST be nonReentrant:
- redeemLOOP
- emergencyWithdraw
- closeout settlement payout

Ordering must follow:
- checks
- state changes
- effects
- external transfers

---

## 12. Strategy Independence (Emergency Must Not Deadlock)

Emergency withdrawal must not depend on:
- strategy hash
- strategy approval
- strategy validity window
- strategy object existence

Emergency is a separate exit path.

---

## 13. Anti-Spam / Grief Protection

Per vault:
- emergencyWithdrawCooldownSeconds = 300 (5 min) recommended
- repeated calls inside cooldown revert

Prevent griefing / denial-of-service via repeated revert attempts.

---

## 14. Redemption Pool Starvation Signaling (Transparency)

Even with perfect invariants, the UX must show:
- Redemption Pool USDT balance
- LOOP total supply
- Coverage ratio (pool / supply)

If pool approaches 0:
- LOOP minting stops
- LOOP redemptions may pause per spec  [oai_citation:19‡Failure modes and emergency’s.pdf](sediment://file_00000000d99871f5aaa53bed7112ce3e)
- users must see it clearly (no hiding)

---

## 15. Admin/Governance Boundaries in Emergencies

Per governance spec:
- Admin may emergency pause trading + deposits immediately
- Admin cannot move user funds
- Admin cannot change fees in emergency
- All sensitive changes require timelock  [oai_citation:20‡Governance and timelock.pdf](sediment://file_00000000d32871f58bbc38ed325c1eab)

Emergency “resume” must require timelock (recommended) to prevent flip-flop abuse.

---

## 16. Mandatory Events (Audit-Grade)

MUST emit:

### 16.1 Redemption
- LoopRedeemed(user, loopAmount, usdtAmount)

### 16.2 Emergency Withdrawal
- EmergencyWithdrawRequested(vaultId, user)
- EmergencyCloseStarted(vaultId)
- EmergencyCloseQueued(vaultId, queueReasonCode)
- EmergencyClosePartial(vaultId, remainingNonUSDT)
- EmergencyCloseCompleted(vaultId, usdtReturned)
- EmergencyCloseFailed(vaultId, reasonCode)

### 16.3 Dust
- DustSwept(vaultId, asset, amount, usdtValue)

---

## 17. UI Requirements (Non-Negotiable)

Every vault view MUST include:
- Emergency Withdraw (USDT) button (visible, not buried)
- Warning modal:
  - “Positions liquidated as-is”
  - “Loss is possible”
  - “Fee tier: USDT (15%)”
- Real-time status display:
  - NONE / QUEUED (gas/oracle/dex) / PARTIAL / COMPLETED
- Link to on-chain interaction instructions (for frontend outages)

---

## 18. Disclosure Requirements (One Sentence That Saves Your Ass)

Docs and UI MUST state:

“Emergency withdrawals are guaranteed but may be delayed during periods of extreme network congestion, gas spikes, oracle staleness, or DEX liquidity failures.”

---

## 19. Chain Halt Scenario

If the chain halts:
- protocol is frozen
- no off-chain IOUs
- operations resume when chain resumes

This is explicitly acknowledged.

---

## 20. Design Intent

YieldLoop does not attempt to eliminate blockchain risk.

YieldLoop attempts to:
- eliminate insolvency risks (receipt token solvency)
- eliminate admin extraction paths
- eliminate ambiguous emergency behavior
- keep failures observable and bounded
- degrade safely: pause > gamble  [oai_citation:21‡Failure modes and emergency’s.pdf](sediment://file_00000000d99871f5aaa53bed7112ce3e)

End of specification.
