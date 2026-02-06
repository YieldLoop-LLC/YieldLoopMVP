# YieldLoop Emergency Withdrawal + Redemption Safety Specification (v1.0)

Status: REQUIRED IMPLEMENTATION  
Applies To: YieldLoop v1.x  
Audience: Smart Contract + Backend Engineers  
Developer must not deviate from this specification.

---

## 1. Purpose

This document defines the complete emergency withdrawal, LOOP redemption, and liveness-safety
behavior for YieldLoop.

Goals:

- Guarantee solvency of receipt token (LOOP)
- Guarantee user right to exit
- Distinguish economic backing from execution availability
- Prevent deadlocks, race conditions, and ambiguous behavior
- Ensure failures degrade safely

This document supplements existing protocol specs and introduces no new economic model.

---

## 2. Definitions

- Solvency: Assets exist on-chain and back claims.
- Liveness: A transaction can be successfully executed and confirmed.
- Emergency Withdrawal: User-initiated exit that liquidates vault to USDT and applies USDT fee tier.
- Redemption Pool: USDT pool backing LOOP claims.

---

## 3. Core Invariants (Hard-Coded)

The following MUST be enforced at contract level:

- LOOP minting without realized profit is impossible.
- Total LOOP supply <= LOOP Redemption Pool USDT balance.
- LOOP redemption cannot exceed Redemption Pool balance.
- Admin cannot mint LOOP.
- Admin cannot move funds from user vaults.
- Vault funds can only be transferred to vault owner.
- Emergency withdrawal cannot bypass accounting rules.
- Emergency withdrawal cannot create negative balances.
- Emergency withdrawal ignores user payout mode (always USDT).

Any violation is a protocol-breaking error.

---

## 4. LOOP Redemption Atomicity

Redemption MUST be implemented as a single atomic operation:

1. require(poolBalance >= amount)
2. burn LOOP from user
3. transfer USDT from Redemption Pool to user

No intermediate state may exist.

Batch redemption loops are prohibited.

---

## 5. Emergency Withdrawal Types

### 5.1 Soft Emergency Withdrawal (Default)

User calls emergencyWithdraw(vaultId)

System behavior:

- Vault state -> CLOSE_PENDING
- Attempt to unwind positions under normal guardrails
- Convert all assets to USDT
- Apply USDT performance fee tier
- Transfer USDT to user

Loss is possible.

### 5.2 Hard Emergency (Global Flag)

If global emergency flag is active:

- Trading disabled
- New deposits disabled
- Emergency withdrawals enter FIFO queue
- System retries unwinds when conditions allow

Emergency withdrawal is guaranteed but not guaranteed to be instantaneous.

---

## 6. Liveness Failure Handling

Backing correctness does not imply execution availability.

If any of the following prevent immediate execution:

- Gas price > maxGasUSDT
- Oracle stale
- DEX routing failure
- RPC outage

Then:

- Vault enters EMERGENCY_QUEUE
- User-visible status displayed
- System retries periodically

No forced price guessing is permitted.

---

## 7. Gas Reserve Bucket

Protocol MUST maintain a gas reserve funded from fees.

Purpose:

- Subsidize emergency unwinds
- Ensure competitive gas bidding

Gas reserve cannot be used for:

- LOOP backing
- Fee rebates
- Admin withdrawals

---

## 8. Emergency Withdrawal vs Payout Mode

Emergency withdrawal ALWAYS results in USDT.

If vault had:

- Suspense profit for LOOP
- Pending LOOP distribution

Those balances are converted to USDT and included in emergency exit.

USDT fee tier applies.

---

## 9. Partial Unwind Handling

If unwind executes partially:

- Resulting asset is held
- Vault marked CLOSE_PENDING_PARTIAL
- Next retry attempts conversion to USDT

No trade reversal attempts.

---

## 10. Dust Handling

Define:

dustThresholdUSDT = 0.50

During emergency close:

- Any balance below dustThresholdUSDT may be swept to Ratchet Reserve
- Emit DustSwept(vaultId, amount)

Dust must not block closeout.

---

## 11. Reentrancy Protection

All emergency and redemption functions MUST be nonReentrant.

State updates MUST occur before external transfers.

---

## 12. Strategy Independence

Emergency withdrawal MUST NOT depend on:

- Strategy object
- Strategy approval state
- Strategy hash

Emergency withdrawal bypasses strategy layer.

---

## 13. Cooldown Protection

Per vault:

emergencyWithdrawCooldownSeconds = 300

Repeated calls within cooldown revert.

Prevents griefing and spam.

---

## 14. Events (Mandatory)

- EmergencyWithdrawRequested(vaultId)
- EmergencyCloseStarted(vaultId)
- EmergencyCloseQueued(vaultId)
- EmergencyClosePartial(vaultId)
- EmergencyCloseCompleted(vaultId, usdtReturned)
- EmergencyCloseFailed(vaultId, reasonCode)
- DustSwept(vaultId, amount)
- LoopRedeemed(user, amount)

---

## 15. UI Requirements

Every vault screen MUST display:

- Emergency Withdraw (USDT) button
- Warning: Loss possible. Positions liquidated as-is.
- Current emergency status (none / queued / partial / completed)

Emergency withdrawal must never be hidden in submenus.

---

## 16. Disclosure Requirement

Docs and UI MUST state:

"Emergency withdrawals are guaranteed but may be delayed during periods of extreme network congestion or gas spikes."

---

## 17. Chain Halt Scenario

If underlying chain halts:

- Protocol is frozen
- No off-chain IOUs
- No synthetic balances
- Operations resume when chain resumes

---

## 18. Design Intent

YieldLoop does not attempt to eliminate blockchain risk.

YieldLoop attempts to:

- Prevent insolvency
- Prevent hidden leverage
- Prevent admin extraction
- Prevent deadlocks
- Make failure states explicit

Survivability > heroics.

---

End of Specification.
