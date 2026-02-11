# Section I — Protocol Foundation

---

## 1.1 Purpose and Philosophy

YieldLoop is a fixed-parameter, conservative, non-custodial arbitrage vault protocol.

It is designed to automate disciplined execution within strict, immutable guardrails.  
It is not designed to maximize yield at the expense of stability.  
It is not designed to adapt risk dynamically.  
It is not designed to compete with speculative DeFi strategies.

YieldLoop prioritizes structural durability over short-term performance.

The protocol is intentionally boring.

YieldLoop exists to provide:

- Deterministic execution
- Realized-profit-only accounting
- Vault-level capital isolation
- Immutable guardrails
- Long-term credibility

The system does not rely on governance to adjust risk.
The system does not expand parameters during bull markets.
The system does not loosen controls under pressure.

YieldLoop is built to remain stable through volatility cycles.

---

## 1.2 Design Priorities

YieldLoop operates under the following priority order:

1. **Credibility**
2. **Survivability**
3. **Capital Growth**
4. **Protocol Revenue**

All architectural decisions follow this order.

### Credibility

Credibility is achieved through:

- Hardcoded guardrails
- Transparent execution logic
- No hidden parameter changes
- No mid-cycle rule mutation
- Clear profit accounting
- Predictable system behavior

YieldLoop does not promise performance.
YieldLoop does not advertise target returns.
YieldLoop does not adjust risk to satisfy market expectations.

### Survivability

Survivability is prioritized over opportunity capture.

The protocol avoids:

- Leverage
- Lending exposure
- Yield farming dependency
- Cross-protocol cascading risk
- Complex multi-leg routing
- Governance-driven risk expansion

Capital preservation is more important than yield maximization.

### Capital Growth

YieldLoop is structured to grow capital steadily when arbitrage conditions permit.

Growth is market-dependent.

The protocol does not manufacture yield.
It captures spread inefficiencies within defined limits.

### Protocol Revenue

Protocol revenue is derived solely from realized profit via a fixed performance fee.

Revenue does not precede user profit.

---

## 1.3 What YieldLoop Is

YieldLoop is:

- A vault-based arbitrage execution protocol
- Non-custodial
- Deterministic in behavior
- Guardrail-constrained
- Realized-profit-accounted
- Non-leveraged
- Non-governance-adjustable
- Designed for conservative automation

Each vault operates independently.

Each vault:

- Holds user capital
- Receives a proposed strategy
- Requires user approval
- Executes within fixed constraints
- Settles realized profit only
- Pays a performance fee only on profit

YieldLoop does not pool capital across users.
YieldLoop does not socialize risk across vaults.
YieldLoop does not cross-subsidize losses.

Every vault stands alone.

---

## 1.4 What YieldLoop Is Not

YieldLoop is not:

- A yield farm
- A staking platform
- A lending protocol
- A liquidity mining scheme
- A governance-driven trading DAO
- A leverage engine
- A speculative token emission model
- A passive income guarantee

YieldLoop does not:

- Mint rewards for participation
- Distribute inflationary emissions
- Guarantee APY
- Adjust risk parameters in pursuit of higher yield
- React to social pressure

The protocol does not attempt to compete with high-risk DeFi systems.

It is intentionally narrower in scope.

---

## 1.5 System Identity and Immutability Doctrine

YieldLoop v1.x operates under an immutability doctrine.

Core execution guardrails are embedded directly in contract code and cannot be modified after deployment.

There is:

- No multisig guardrail editing
- No community parameter voting
- No runtime slippage widening
- No liquidity threshold reduction
- No deployment percentage changes
- No hidden risk escalation

If structural changes are required, a new version of the protocol must be deployed.

Existing vaults remain governed by their original rules.

YieldLoop prefers versioned migration over live mutation.

This doctrine exists to prevent:

- Governance drift
- Bull-market risk creep
- Emotional decision making
- Founder-driven parameter escalation
- Erosion of long-term credibility

Immutability is not a limitation.

It is a design choice.

---

## 1.6 Operational Scope (v1.x)

YieldLoop v1.x operates under the following scope:

- Supported DEX venues: PancakeSwap and BiSwap
- Strategy type: Spot arbitrage only
- Execution routing: Limited hop count
- Oracle validation required
- Fixed slippage and liquidity thresholds
- Fixed capital deployment caps
- Realized-profit-only accounting

No leverage.
No margin.
No yield farming.
No lending.

The protocol remains within its defined boundaries.

Expansion, if ever introduced, will occur via new version deployment.

---

End of Section I
