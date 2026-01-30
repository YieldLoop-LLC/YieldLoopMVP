## LOOP Redemption Token (Receipt Token Model)

### Purpose

This section formally defines LOOP as a **receipt / redemption token** representing a claim on realized trading profit allocated to LOOP payout mode.

LOOP is **not** an emission token, **not** an inflationary reward token, and **not** a speculative yield instrument.

LOOP exists solely as an accounting receipt for user-earned profit that remains redeemable for USDT through the protocol.

---

### Core Definition

LOOP is a **mint-and-burn receipt token**:

- LOOP is **minted** only when realized net trading profit is allocated to LOOP payout mode.
- LOOP is **burned** when redeemed for USDT.
- LOOP cannot be minted without realized profit.
- LOOP has no scheduled emissions.
- LOOP has no block-based issuance.
- LOOP has no inflation schedule.

If no realized profit exists, **no LOOP is minted**.

---

### LOOP Redemption Pool

YieldLoop maintains a dedicated on-chain **LOOP Redemption Pool** denominated in USDT.

#### Funding Source

The LOOP Redemption Pool is funded exclusively by:

- Net realized trading profit allocated to LOOP payout mode.

No other source may be treated as primary backing.

#### Accounting Rule

For each vault:

If user selects LOOP payout:
netProfitUSDT → LOOP Redemption Pool
LOOP minted = netProfitUSDT

1 LOOP represents a claim on 1 USDT within the LOOP Redemption Pool.

---

### Minting Rules (Hard Lock)

LOOP may be minted only when:

- A trade cycle closes.
- Realized gross profit > 0.
- Performance fee has been deducted.
- User has selected LOOP payout mode.

All four conditions must be true.

LOOP minting is rejected in all other cases.

---

### Burning and Redemption

When a user redeems LOOP:

1. LOOP amount is burned.
2. Equivalent USDT amount is transferred from the LOOP Redemption Pool to the user wallet.

Redemption rate:

1 LOOP = 1 USDT

---

### Redemption Pool Solvency Rule

At all times:

Total LOOP supply ≤ USDT balance of LOOP Redemption Pool

Protocol must enforce this invariant at the contract level.

---

### Ratchet Stability Reserve Interaction

The Ratchet Stability Reserve:

- May optionally top-up the LOOP Redemption Pool.
- May never be the primary backing source.
- May never be drained to zero to support LOOP redemptions.
- Cannot be used to mint LOOP.

The Ratchet system functions only as **supplemental resilience**, not as LOOP backing.

---

### No Market-Buy LOOP in v1

YieldLoop v1 does **not**:

- Buy LOOP from DEX markets for reward distribution.
- Support LOOP farming.
- Support LOOP emissions.

All LOOP issuance occurs only through minting against realized profit.

---

### No Guarantee of Profit

LOOP represents a claim on profit that has already occurred.

LOOP does **not**:

- Guarantee future profits.
- Guarantee system profitability.
- Represent ownership of protocol revenue.

If YieldLoop produces no profit, LOOP issuance stops.

---

### Non-Negotiable Invariants

The following rules must be enforced in code:

- LOOP minting without realized profit is impossible.
- LOOP supply cannot exceed Redemption Pool USDT.
- LOOP redemption cannot exceed Redemption Pool USDT.
- LOOP cannot be minted by admin.
- LOOP cannot be minted via governance.
- LOOP cannot be minted via upgrade.

Any violation is a protocol-breaking error.

---

### Design Intent

LOOP exists to:

- Provide users an optional profit receipt.
- Align long-term users with protocol performance.
- Avoid inflationary reward mechanics.
- Avoid Ponzi-shaped emissions.

LOOP is intentionally boring.

Boring is survivable.

