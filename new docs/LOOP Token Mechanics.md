# Section IV — LOOP Token Mechanics

---

## 4.1 Purpose of LOOP

LOOP is a profit-receipt and redemption token.

It represents realized net trading profit that has been allocated to LOOP payout mode instead of direct USDT withdrawal or compounding.

LOOP is:

- Minted only from realized net profit
- Backed 1:1 by USDT in the LOOP Redemption Pool
- Burned upon redemption
- Non-inflationary
- Non-governance
- Non-emission based

LOOP does not represent ownership of the protocol.

LOOP does not control guardrails.

LOOP does not grant voting authority.

---

## 4.2 No Maximum Supply

LOOP has no fixed maximum supply.

Total LOOP supply expands only when:

- A vault realizes net profit
- The user selects LOOP payout mode
- Net USDT profit (after performance fee) is transferred into the LOOP Redemption Pool
- Equivalent LOOP is minted to the user

Total LOOP supply contracts only when:

- LOOP is redeemed for USDT
- LOOP is burned
- USDT is transferred out of the Redemption Pool

Supply is profit-driven.

No profit → no minting.

---

## 4.3 Minting Rules

LOOP may only be minted when ALL of the following are true:

1. Vault has completed settlement.
2. Net profit > 0 after performance fee.
3. User has selected LOOP payout mode.
4. Equivalent USDT is transferred into the LOOP Redemption Pool.

Mint amount = Net Profit USDT allocated to LOOP.

Minting is atomic with reserve transfer.

There is no scenario where LOOP supply exceeds USDT in the Redemption Pool.

---

## 4.4 Redemption Pool

The LOOP Redemption Pool:

- Holds USDT backing LOOP supply.
- Is segregated from operational funds.
- Is not deployed in arbitrage.
- Is not used for gas.
- Is not used for marketing.
- Is not used for development expenses.

Redemption Pool balance must always satisfy:

LOOP Total Supply <= Redemption Pool USDT Balance

This invariant is enforced at contract level.

---

## 4.5 Redemption Mechanism

LOOP holders may redeem LOOP for USDT subject to protocol-defined redemption rules.

Upon redemption:

1. LOOP is burned.
2. Equivalent USDT is transferred from Redemption Pool.
3. LOOP total supply decreases.
4. Redemption Pool balance decreases.

Redemption does not impact:

- Other vaults
- Other LOOP holders
- Guardrails
- Execution engine

There is no fractional redemption.
There is no reserve dilution.
There is no dynamic haircut.

Redemption is 1:1 with backing USDT.

---

## 4.6 No Algorithmic Price Targeting

YieldLoop does not attempt to:

- Control market price of LOOP
- Enforce a trading price
- Peg LOOP to external markets
- Guarantee appreciation

LOOP backing is mechanical, not market-managed.

Market price may trade above backing.
Market price may trade below backing.

Protocol logic does not intervene in secondary market trading.

---

## 4.7 Ratchet Interaction (If Enabled)

If ratchet logic is implemented in the protocol:

- Ratchet mechanics operate only on protocol-level reserve allocation.
- Ratchet does not modify LOOP minting rules.
- Ratchet does not inflate supply.
- Ratchet does not create synthetic yield.

Ratchet may affect:

- Floor signaling
- Internal accounting models
- Redemption policy thresholds

But ratchet cannot violate:

LOOP Supply <= Redemption Pool Balance

Ratchet cannot create unbacked LOOP.

---

## 4.8 Performance Fee Interaction

Performance fee is 20% of realized profit.

LOOP minting occurs only after performance fee deduction.

Minted LOOP reflects net user profit only.

Protocol does not mint LOOP from:

- Gross profit
- Unrealized PnL
- Speculative projections
- Emissions
- Incentives

---

## 4.9 Failure Conditions

If vault performance declines:

- No LOOP is minted.
- Existing LOOP remains fully backed.
- Redemption Pool remains solvent.
- LOOP supply does not inflate.

LOOP supply growth is directly tied to realized profitability.

No realized profit → no LOOP growth.

---

## 4.10 Deterministic Accounting Model

The LOOP model guarantees:

- No dilution
- No hidden minting
- No emissions
- No discretionary supply control
- No governance inflation
- No fractional reserve model

All LOOP in existence corresponds to USDT already held in Redemption Pool.

---

End of Section IV
