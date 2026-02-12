# Section IV — LOOP Token Mechanics

---

## 4.1 Purpose of LOOP

LOOP is an internal profit-receipt and redemption token.

It represents realized net trading profit that has been allocated to LOOP payout mode.

LOOP is:

- Minted only from realized net profit
- Backed by USDT in the Redemption Pool
- Subject to ratchet-based floor escalation
- Transferable between wallets
- Non-governance
- Non-emission based
- Not publicly issued for speculative distribution

LOOP does not represent protocol ownership.

LOOP does not modify vault guardrails.

---

## 4.2 No Maximum Supply

LOOP has no fixed maximum supply.

Supply expands only when:

- A vault completes settlement
- Net profit exists after performance fee
- User selects LOOP payout mode
- Equivalent USDT is transferred into the LOOP Redemption Pool

Supply contracts only when:

- LOOP is redeemed
- LOOP is burned

No realized profit → no new LOOP minted.

---

## 4.3 Profit Allocation Flow

At vault settlement:

1. Gross profit calculated.
2. Performance fee (20%) deducted.
3. Net profit assigned to user.
4. User selects payout mode:
   - Compound
   - Withdraw USDT
   - Mint LOOP

If LOOP selected:

- Net profit USDT transferred into Redemption Pool.
- Equivalent LOOP minted to user.

Mint amount:

Minted LOOP = Net Profit USDT allocated

Minting and reserve transfer occur atomically.

---

## 4.4 Redemption Pool

The LOOP Redemption Pool:

- Holds USDT backing LOOP supply.
- Is segregated from operational funds.
- Is not deployed in arbitrage.
- Is not used for protocol expenses.

Backing invariant:

Redemption Pool USDT >= LOOP Total Supply

This invariant must hold at all times.

---

## 4.5 Ratchet Reserve

Ratchet Reserve is separate from Redemption Pool.

It accumulates a defined portion of protocol performance fees.

Ratchet Reserve:

- Does not mint LOOP.
- Does not belong to individual vaults.
- Exists to increase LOOP floor backing.
- Is independent from user mint events.

---

## 4.6 Ratchet Step Mechanism

Ratchet operates under:

- Fixed step size increment
- Dynamic cooldown window

### Fixed Step Size

Each ratchet trigger increases floor backing by a fixed increment per LOOP.

Example:

Ratchet Step Increment = 0.000X USDT per LOOP

Step size is immutable.

Ratchet only moves upward.

---

## 4.7 Dynamic Cooldown

Ratchet triggers are limited by a dynamic cooldown window.

Cooldown:

- Prevents rapid consecutive ratchet escalation.
- Adjusts based on reserve growth rate.
- Enforces structural pacing.

Cooldown does not:

- Reduce step size.
- Reverse floor.
- Allow negative adjustment.

---

## 4.8 Ratchet Transfer Mechanism (Model A)

When ratchet conditions are satisfied:

1. USDT from Ratchet Reserve is transferred into Redemption Pool.
2. Floor backing per LOOP increases.
3. LOOP supply remains unchanged.
4. Redemption Pool balance increases.
5. Ratchet Reserve decreases accordingly.

This increases overcollateralization.

Ratchet cannot violate backing invariant.

---

## 4.9 Floor Definition

Floor per LOOP:

Redemption Pool USDT / LOOP Total Supply

Because Ratchet feeds Redemption Pool:

- Floor increases over time.
- Floor cannot decrease.
- Redemption reduces supply, which may further increase floor.

Floor movement is mechanical, not market-driven.

---

## 4.10 Redemption

Upon redemption:

1. LOOP is burned.
2. Equivalent USDT transferred from Redemption Pool.
3. LOOP supply decreases.
4. Floor recalculates automatically.

Redemption does not impact:

- Guardrails
- Vault logic
- Ratchet parameters

Redemption is 1:1 with backing.

---

## 4.11 Deterministic Solvency

LOOP guarantees:

- No unbacked minting.
- No fractional reserve.
- No discretionary inflation.
- No governance supply control.

All LOOP in circulation corresponds to USDT in Redemption Pool.

Ratchet only increases backing.

---

## 4.12 Failure Conditions

If vault performance declines:

- No new LOOP is minted.
- Ratchet Reserve growth slows.
- Redemption Pool remains solvent.
- Existing LOOP remains backed.

If Ratchet Reserve is empty:

- Ratchet simply does not trigger.
- Floor remains stable.

Protocol never inflates supply to compensate.

---

End of Section IV
