## Failure Modes and Emergency Behavior

### Purpose

This section defines how YieldLoop behaves under adverse conditions, including market stress, infrastructure failure, oracle malfunction, and execution anomalies.

The goal is to ensure:

- User funds are never placed into undefined states.
- Losses are contained.
- Failures degrade safely.
- No hidden behavior occurs during abnormal conditions.

YieldLoop is designed to **fail closed**, not fail open.

---

### General Failure Philosophy

When in doubt:

- Stop trading.
- Preserve current balances.
- Allow withdrawals of realized profit.
- Allow vault closeout when safe.

YieldLoop never forces risky recovery actions.

---

### Market Volatility Spike

**Trigger Conditions**

- Volatility exceeds configured thresholds.
- Rapid price swings detected.
- Slippage estimates exceed limits.

**System Response**

- New trades blocked.
- Vaults enter PAUSED state.
- Existing positions remain untouched.
- No forced liquidation occurs.

**User Impact**

- Profit withdrawals remain available.
- Vault closeout remains available under guardrails.

---

### DEX Liquidity Collapse

**Trigger Conditions**

- Pool liquidity below minimum.
- Price impact exceeds cap.
- Routing failures.

**System Response**

- Block execution for affected routes.
- Pause vault execution.
- No routing to alternative unsupported venues.

---

### Oracle Failure or Staleness

**Trigger Conditions**

- Oracle stale beyond max age.
- Oracle deviation exceeds bounds.

**System Response**

- Trading blocked.
- Vaults paused.
- No fallback trading using unsafe pricing.

---

### Repeated Transaction Failures

**Trigger Conditions**

- Failed swaps exceed threshold.

**System Response**

- Vault auto-paused.
- No retries beyond limit.
- Manual intervention via timelock required.

---

### Gas Price Spike

**Trigger Conditions**

- Gas estimate exceeds buffer.

**System Response**

- Trade rejected by Trade Quality Gate.
- No execution until gas normalizes.

---

### Smart Contract Bug Discovery

**Trigger Conditions**

- Verified exploit or critical bug.

**System Response**

- Immediate emergency pause.
- New deposits disabled.
- Trading disabled.
- Vault closeout enabled when safe.

---

### Partial System Outage

**Examples**

- Indexer outage
- Frontend outage
- RPC provider outage

**System Response**

- On-chain contracts continue functioning.
- Users may interact directly via blockchain tools.
- No dependency on centralized UI.

---

### LOOP Redemption Pool Stress

**Trigger Conditions**

- Redemption pool balance approaches zero.

**System Response**

- LOOP minting stops.
- LOOP redemptions temporarily paused.
- Trading may continue.
- Ratchet reserve may top-up pool (if enabled).

---

### Prolonged No-Profit Period

**Trigger Conditions**

- No profitable trades for extended period.

**System Response**

- No LOOP minted.
- Vaults remain idle.
- Users may withdraw profits or close vaults.

---

### Admin Key Compromise

**Trigger Conditions**

- Suspected key breach.

**System Response**

- Emergency pause.
- Timelocked key rotation.
- Public incident disclosure.

---

### Non-Negotiable Rules

Under no failure mode may the system:

- Mint LOOP without realized profit.
- Move funds from user vaults.
- Force liquidation.
- Charge fees on losses.
- Bypass timelock for parameter changes.

---

### Design Intent

YieldLoop does not attempt to “save” positions.

YieldLoop attempts to **avoid making things worse**.

Survival > heroics.
