# Appendix A — Protocol Parameters (v1.2 Required Defaults)

**Document:** YieldLoop — Automated Vault Trading Protocol + LOOP Rewards (BNB Chain)  
**Applies To:** v1.2 MVP Spec  
**Status:** REQUIRED IMPLEMENTATION DEFAULTS (Developer must not alter without Owner approval)

---

## A.1 Purpose

This appendix defines the **exact default parameter values** required to implement YieldLoop v1.2 without ambiguity.

- These parameters are **mandatory** unless explicitly marked Optional.
- Developers must treat these values as the **source-of-truth build defaults**.
- Any changes require explicit owner approval.

---

## A.2 Global Constants

### A.2.1 Chain + Venues

| Parameter | Type | Default | Notes |
|---|---:|---:|---|
| chainId | uint256 | 56 | BNB Chain |
| dexVenueAllowed | set<string> | {PCS, BISWAP} | **Only these venues allowed** |
| allowThirdDexRouting | bool | false | No third venue in v1 |

### A.2.2 Supported Vault Assets (Whitelist)

| Parameter | Type | Default |
|---|---:|---:|
| vaultAssetWhitelist | set<address> | {BTCB, ETH, SOL, XRP, BNB, USDT} |

**Rule:** Any asset not in `vaultAssetWhitelist` is **rejected** at deposit and cannot be traded into.

---

## A.3 Execution Scan + Trading Throttles

### A.3.1 Market Scan Parameters

| Parameter | Type | Default | Notes |
|---|---:|---:|---|
| quoteScanIntervalSeconds | uint32 | 60 | How often to scan for opportunities |
| quoteScanRandomJitterSeconds | uint16 | 10 | Random jitter to reduce predictable MEV patterns |
| maxConcurrentVaultExecutions | uint16 | 10 | Max vault executions active at once |
| maxTxRetriesPerOpportunity | uint8 | 1 | 0 = no retry, 1 = single retry |

### A.3.2 Trading Limits (Anti-Churn / Safety)

| Parameter | Type | Default | Notes |
|---|---:|---:|---|
| minSecondsBetweenTrades | uint32 | 900 | 15 minutes cooldown per vault |
| maxTradesPerDayPerVault | uint8 | 12 | Hard cap per vault |
| maxFailedTxPerHourPerVault | uint8 | 3 | Fail-safe trigger threshold |
| vaultAutoPauseOnRepeatedFailure | bool | true | If `maxFailedTxPerHourPerVault` exceeded |

---

## A.4 Trade Quality Gate (Profit Threshold Model)

### A.4.1 Required Net Profit Threshold

**Trade Quality Gate Rule:** Execute only if:

`ExpectedNetProfitUSDT >= MinimumRequiredNetProfitUSDT`

| Parameter | Type | Default |
|---|---:|---:|
| minNetProfitUSDT | uint256 | 3.00 USDT |

### A.4.2 Buffers (Cost + Risk)

| Parameter | Type | Default | Notes |
|---|---:|---:|---|
| gasBufferUSDT | uint256 | 0.50 USDT | Conservative gas buffer |
| slippageBufferBps | uint16 | 30 | 0.30% |
| mevPenaltyBps | uint16 | 20 | 0.20% implied penalty |
| executionUncertaintyBufferBps | uint16 | 15 | 0.15% |

### A.4.3 Hard Safety Caps (Do Not Execute Beyond)

| Parameter | Type | Default | Notes |
|---|---:|---:|---|
| maxSlippageBpsPerSwap | uint16 | 50 | 0.50% max per swap |
| priceImpactCapBps | uint16 | 50 | 0.50% max |
| maxRouteHops | uint8 | 2 | 0–2 hops only |
| allowExoticRoutes | bool | false | No complex multi-hop routing |

---

## A.5 Liquidity + Sizing Guardrails

| Parameter | Type | Default | Notes |
|---|---:|---:|---|
| minPoolLiquidityUSDT | uint256 | 250000 USDT | Reject routes below this |
| minTradeSizeUSDT | uint256 | 25 USDT | No dust trades |
| maxTradeSizePctOfPoolBps | uint16 | 20 | 0.20% of pool liquidity max |

---

## A.6 Vault Ledger + Profit Accounting Rules

### A.6.1 Realized Profit Definition

**Realized Profit:** Profit only exists when trades are closed and net value is confirmed.

| Parameter | Type | Default |
|---|---:|---:|
| allowUnrealizedProfitCredit | bool | false |

### A.6.2 Withdrawable Profit Balance Policy (MANDATORY)

**Rule (Hard Lock):** All profit credited to withdrawable balance must be held as USDT.

| Parameter | Type | Default | Notes |
|---|---:|---:|---|
| withdrawableProfitAsset | address | USDT | Must be USDT |
| autoConvertProfitToUSDT | bool | true | Convert immediately on profit credit |
| profitWithdrawAllowedAnytime | bool | true | Profit withdraw anytime |
| principalWithdrawRequiresCloseout | bool | true | Principal requires unwind + closeout |

---

## A.7 Vault Closeout Rules

| Parameter | Type | Default | Notes |
|---|---:|---:|---|
| maxCloseoutDurationSeconds | uint32 | 86400 | 24 hours max |
| allowPartialCloseout | bool | false | v1: full closeout only |
| closeoutMayEnterPendingState | bool | true | CLOSE_PENDING state allowed |
| forcedLiquidationAllowed | bool | false | Never force liquidation outside guardrails |

---

## A.8 Config / Weight Changes (Timing + Cooldown)

| Parameter | Type | Default | Notes |
|---|---:|---:|---|
| configCooldownSeconds | uint32 | 86400 | 24-hour cooldown |
| configAppliesImmediately | bool | false | Applies only to future execution cycle |
| weightChangesForceRebalance | bool | false | No forced rebalance |

---

## A.9 LOOP Receipt Token System (v1.2 Implementation Lock)

### A.9.1 LOOP Core Definition (Hard Lock)

LOOP is a **mint-and-burn receipt token** representing a claim on realized trading profit
allocated to LOOP payout mode.

Hard rules:
- LOOP is minted **only** when realized net trading profit exists.
- LOOP is burned **only** when redeemed for USDT.
- LOOP cannot be minted without realized profit.
- LOOP has no emissions, schedules, or inflation.
- LOOP cannot be minted by admin, governance, or upgrade logic.

If no realized profit exists, **no LOOP is minted**.

---

### A.9.2 LOOP Redemption Pool (USDT-Backed)

YieldLoop maintains a dedicated **LOOP Redemption Pool** denominated in USDT.

Rules:
- The redemption pool is funded exclusively by realized net trading profit
  allocated to LOOP payout mode.
- 1 LOOP represents a claim on **1 USDT** in the redemption pool.
- Total LOOP supply must never exceed the USDT balance of the redemption pool.

Invariant (must be enforced at contract level):

Total LOOP Supply ≤ LOOP Redemption Pool USDT Balance

---

### A.9.3 Minting and Redemption Rules (Non-Negotiable)

Minting:
- Occurs only at trade-cycle settlement.
- Occurs only after performance fees are deducted.
- Occurs only if the user selected LOOP payout mode.

Redemption:
- LOOP may be redeemed at any time, subject to pool liquidity.
- Redeeming LOOP burns the token and transfers equivalent USDT.
- Redemption rate is fixed: **1 LOOP = 1 USDT**.

---

### A.9.4 Explicitly Disallowed (v1.2)

The protocol must NOT:
- Market-buy LOOP for rewards.
- Emit LOOP on a schedule.
- Farm or stake LOOP.
- Use reserves as primary LOOP backing.
- Mint LOOP without realized profit.

Any violation of these rules is a protocol-breaking error.

---

## A.10 Emergency Safeties

| Parameter | Type | Default | Notes |
|---|---:|---:|---|
| globalKillSwitchEnabled | bool | true | Admin/governance emergency |
| autoPauseOnDexOutage | bool | true | If PCS/Biswap routing fails |
| autoPauseOnOracleStale | bool | true | If reference pricing stale |
| autoPauseOnExcessSlippage | bool | true | If repeated high slippage events |

---

## A.11 Parameter Governance Notes (v1.2)

| Parameter | Type | Default |
|---|---:|---:|
| governanceCanEditParameters | bool | false |
| adminCanEditParameters | bool | true |
| adminEditsRequireTimelock | bool | true |

---

## A.12 Optional (Not Implemented in v1.2)

The following are explicitly **out of scope** for v1.2 MVP unless later added:

- Multi-chain support
- Fiat rails (Transak/MoonPay/Stripe UX)
- Yield farming pools
- Lending integrations
- Index baskets / rebalancing portfolios
- MEV private relays / bundle auctions
- Insurance / coverage systems
- Full automation of dynamic parameter tuning via ML

**Rule:** Developer must not ship these as “bonus features” in v1.2.

# Appendix B — Vault State Machine + Event List (Dev Implementation Spec)

**Document:** YieldLoop — Automated Vault Trading Protocol + LOOP Rewards (BNB Chain)  
**Version:** v1.2 MVP  
**Appendix:** B  
**Status:** REQUIRED IMPLEMENTATION SPEC (Developer must not improvise states)

---

## B.1 Purpose

YieldLoop must operate as a **deterministic state machine** so that:
- funds are never traded during restricted phases,
- users can withdraw profits safely,
- principal withdrawals require closeout settlement,
- vault accounting remains audit-safe.

This appendix defines:
1) the **only allowed vault states**,  
2) the **only allowed state transitions**, and  
3) the **required event emission list**.

---

## B.2 State Definitions

Each vault is always in exactly one state.

### B.2.1 Vault States (ENUM)

| State | Name | Description |
|---:|---|---|
| 0 | VAULT_CREATED | Vault exists but has no active deposit |
| 1 | DEPOSIT_PENDING | Deposit initiated, awaiting confirmation |
| 2 | FUNDED_IDLE | Vault funded; no active execution in progress |
| 3 | STRATEGY_PROPOSED | Strategy computed and awaiting user approval |
| 4 | STRATEGY_APPROVED | User approved strategy; awaiting execution window |
| 5 | EXECUTING | Execution engine actively routing/swapping |
| 6 | HOLDING_POSITION | Vault holds traded assets awaiting exit conditions |
| 7 | SETTLEMENT_PENDING | Post-trade accounting reconciliation queued |
| 8 | PROFIT_CREDITED | Realized profit credited to withdrawable ledger |
| 9 | WITHDRAW_PROFIT_PENDING | Profit withdrawal requested; payout queued |
| 10 | CLOSE_REQUESTED | User requested vault closure (principal withdraw) |
| 11 | CLOSE_PENDING | Vault unwinding / liquidating to USDT |
| 12 | CLOSED | Vault closed; funds withdrawable / returned |
| 13 | PAUSED | Vault paused (failure guardrail, oracle issues, admin stop) |
| 14 | ERROR_LOCK | Fatal error; requires admin intervention |

---

## B.3 High-Level Rule Set (Non-Negotiable)

### B.3.1 Execution Permission Rule

**Trades/swaps may only occur in:**
- EXECUTING
- CLOSE_PENDING

All other states must reject any trade execution attempt.

### B.3.2 Profit Credit Rule

Profit can only be credited in:
- SETTLEMENT_PENDING → PROFIT_CREDITED

Profit must be:
- realized only
- net of fees
- credited as USDT only  
(see Appendix A withdrawableProfitAsset rule)

### B.3.3 User Withdrawal Rule

- User may withdraw **profit** anytime after PROFIT_CREDITED.
- User may withdraw **principal** only after CLOSED.

---

## B.4 Valid State Transitions (ONLY THESE)

### B.4.1 Deposit + Initialize Path

| From | Event | To |
|---|---|---|
| VAULT_CREATED | DepositInitiated | DEPOSIT_PENDING |
| DEPOSIT_PENDING | DepositConfirmed | FUNDED_IDLE |

### B.4.2 Strategy Proposal + Approval Path

| From | Event | To |
|---|---|---|
| FUNDED_IDLE | StrategyGenerated | STRATEGY_PROPOSED |
| STRATEGY_PROPOSED | UserApprovedStrategy | STRATEGY_APPROVED |
| STRATEGY_PROPOSED | UserRejectedStrategy | FUNDED_IDLE |

### B.4.3 Execution + Holding + Settlement Path

| From | Event | To |
|---|---|---|
| STRATEGY_APPROVED | ExecutionStarted | EXECUTING |
| EXECUTING | ExecutionCompleted | HOLDING_POSITION |
| HOLDING_POSITION | ExitSignalTriggered | EXECUTING |
| EXECUTING | TradeCycleFinished | SETTLEMENT_PENDING |
| SETTLEMENT_PENDING | SettlementCompleted | PROFIT_CREDITED |
| PROFIT_CREDITED | ReturnToIdle | FUNDED_IDLE |

> **NOTE:** YieldLoop is opportunistic, not forced-cycle.  
> This means:
> - HOLDING_POSITION may last minutes or days.
> - FUNDED_IDLE may exist even while scanning.
> - “Cycle” events are only triggered when trades close and settlement completes.

### B.4.4 Profit Withdrawal Path

| From | Event | To |
|---|---|---|
| PROFIT_CREDITED | ProfitWithdrawRequested | WITHDRAW_PROFIT_PENDING |
| WITHDRAW_PROFIT_PENDING | ProfitWithdrawConfirmed | PROFIT_CREDITED |

> Profit withdrawal does not reset the vault or require closing the vault.

### B.4.5 Principal Closeout Path (Vault Close)

| From | Event | To |
|---|---|---|
| FUNDED_IDLE | CloseoutRequested | CLOSE_REQUESTED |
| PROFIT_CREDITED | CloseoutRequested | CLOSE_REQUESTED |
| HOLDING_POSITION | CloseoutRequested | CLOSE_REQUESTED |
| CLOSE_REQUESTED | CloseoutStarted | CLOSE_PENDING |
| CLOSE_PENDING | CloseoutCompleted | CLOSED |

**Rules:**
- CLOSE_PENDING must unwind positions into USDT under guardrails.
- Closeout may remain pending up to `maxCloseoutDurationSeconds`.

### B.4.6 Pause / Resume / Failure Path

| From | Event | To |
|---|---|---|
| ANY (except ERROR_LOCK) | PauseTriggered | PAUSED |
| PAUSED | ResumeTriggered | FUNDED_IDLE |
| ANY | FatalErrorDetected | ERROR_LOCK |
| ERROR_LOCK | AdminRecovered | FUNDED_IDLE |

**Pause triggers include:**
- repeated tx failure thresholds
- oracle stale / invalid
- DEX outage
- admin kill switch

---

## B.5 Illegal Transitions (MUST REJECT)

These actions must revert at contract level and be logged:

- trading in any state other than EXECUTING or CLOSE_PENDING
- profit credit in any state other than SETTLEMENT_PENDING
- principal withdrawals in any state other than CLOSED
- deposits while vault is EXECUTING, CLOSE_PENDING, PAUSED, or ERROR_LOCK
- closeout completion while any non-USDT asset balance remains (unless dust threshold explicitly defined)

---

## B.6 Required Events (Emit All)

### B.6.1 Vault Lifecycle Events

| Event | Emitted When |
|---|---|
| VaultCreated(vaultId, owner) | vault deployed |
| DepositInitiated(vaultId, amount, asset) | deposit request begins |
| DepositConfirmed(vaultId, amount, asset) | deposit final onchain |
| StrategyGenerated(vaultId, strategyHash, params) | strategy proposal is created |
| UserApprovedStrategy(vaultId, strategyHash) | user accepts |
| UserRejectedStrategy(vaultId, strategyHash) | user rejects |
| ExecutionStarted(vaultId, strategyHash) | trading starts |
| ExecutionCompleted(vaultId) | trading ends |
| ExitSignalTriggered(vaultId, reasonCode) | exit condition hit |
| TradeCycleFinished(vaultId, netResultUSDT) | cycle closed |
| SettlementStarted(vaultId) | settlement begins |
| SettlementCompleted(vaultId, realizedProfitUSDT, feeUSDT) | settlement done |
| ProfitCredited(vaultId, profitUSDT) | profit ledger updated |
| ProfitWithdrawRequested(vaultId, amountUSDT, recipient) | profit payout requested |
| ProfitWithdrawConfirmed(vaultId, amountUSDT, recipient) | profit paid |
| CloseoutRequested(vaultId) | user requests close |
| CloseoutStarted(vaultId) | unwind begins |
| CloseoutCompleted(vaultId, finalPrincipalUSDT) | vault closed |
| VaultPaused(vaultId, reasonCode) | pause triggered |
| VaultResumed(vaultId) | resumed |
| FatalErrorDetected(vaultId, code) | error lock |
| AdminRecovered(vaultId) | recovered |

### B.6.2 Accounting Events (Mandatory for Auditability)

| Event | Emitted When |
|---|---|
| LedgerSnapshot(vaultId, principalUSDT, withdrawableProfitUSDT, navUSDT) | after settlement |
| TradeDetail(vaultId, venueIn, venueOut, tokenIn, tokenOut, amountIn, amountOut, gasUsed) | each executed trade |

---

## B.7 Reason Codes (Standardize)

### B.7.1 Pause ReasonCode ENUM

| Code | Meaning |
|---:|---|
| 1 | ORACLE_STALE |
| 2 | EXCESS_SLIPPAGE |
| 3 | DEX_OUTAGE |
| 4 | TX_FAILURE_LIMIT |
| 5 | ADMIN_KILL_SWITCH |
| 6 | RISK_GUARDRAIL_BREACH |

### B.7.2 ExitSignal ReasonCode ENUM

| Code | Meaning |
|---:|---|
| 1 | TARGET_SPREAD_REACHED |
| 2 | ARB_OPPORTUNITY_VALID |
| 3 | TIME_DECAY_EXIT |
| 4 | RISK_EXIT |
| 5 | USER_CLOSEOUT |

---

## B.8 Dust Handling (Required)

To prevent “cannot close vault due to dust” edge cases:

| Parameter | Type | Default |
|---|---:|---:|
| dustThresholdUSDT | uint256 | 0.50 USDT |

**Rule:** balances smaller than `dustThresholdUSDT` may be treated as dust and ignored for close completion, but must be logged.

---

## B.9 Dev Enforcement Clause

Developer must implement explicit reverts for:
- illegal state transitions
- attempts to execute trades in blocked states
- attempts to credit profit outside settlement phase
- attempts to withdraw principal before CLOSED

Failure to enforce these conditions is a protocol-breaking error.

# Appendix C — Strategy Object Spec (Proposal → Approval → Execution Binding)

**Document:** YieldLoop — Automated Vault Trading Protocol + LOOP Rewards (BNB Chain)  
**Version:** v1.2 MVP  
**Appendix:** C  
**Status:** REQUIRED IMPLEMENTATION SPEC (No Strategy Improvisation Allowed)

---

## C.1 Purpose

This appendix defines the exact specification for the **Strategy Object** in YieldLoop v1.2.

The Strategy Object exists to:
- make system behavior deterministic,
- prevent developer “interpretation,”
- bind execution to user-approved constraints,
- ensure vault execution remains auditable.

In v1.2:
- strategies are generated by protocol logic,
- presented to user,
- **user must approve** before execution,
- execution must conform to the approved object.

---

## C.2 Strategy Object Overview

A **Strategy Object** is a structured record containing:
1) routing constraints,
2) execution limits,
3) asset whitelist enforcement,
4) trade-quality thresholds,
5) expiry rules.

It is treated as a signed/approved intent.

---

## C.3 Strategy Object Structure (Data Model)

### C.3.1 StrategyObject (Core)

**Struct Name (recommended):** `StrategyObject`

| Field | Type | Required | Description |
|---|---|---:|---|
| strategyVersion | uint16 | YES | Hardcode = 120 (represents v1.2.0) |
| vaultId | uint256 | YES | Unique vault identifier |
| owner | address | YES | Vault owner |
| createdAtUTC | uint64 | YES | Strategy generation timestamp |
| validUntilUTC | uint64 | YES | Strategy expiration timestamp |
| strategyNonce | uint64 | YES | Monotonic increment per vault strategy generation |
| strategyType | uint8 | YES | ENUM (see C.4) |
| baseAsset | address | YES | Vault asset at deposit (must be whitelisted) |
| settlementAsset | address | YES | Must equal USDT |
| dexVenueAllowList | bytes32 | YES | Bitmask: PCS/BISWAP only |
| routeRules | RouteRules | YES | Constraints on routing |
| tradeGuardrails | TradeGuardrails | YES | Profit, slippage, impact, liquidity thresholds |
| sizingRules | SizingRules | YES | min/max sizes per trade |
| executionLimits | ExecutionLimits | YES | cooldowns, max trades per day |
| oracleRules | OracleRules | YES | sanity checks |
| feeRules | FeeRules | YES | performance fee routing |
| complianceRules | ComplianceRules | OPTIONAL | blacklist + sanctions gates |

---

## C.4 Strategy Type ENUM (Hard Lock)

| Value | Name | Meaning |
|---:|---|---|
| 1 | SPREAD_CAPTURE | Single-venue spread capture logic |
| 2 | DEX_ARBITRAGE | PCS ↔ BISWAP arbitrage execution |

**Rule:** v1.2 must support at least one.  
If only one is implemented, dev must still preserve enum structure for future expansion.

---

## C.5 Sub-Structures (Required Definitions)

### C.5.1 RouteRules

| Field | Type | Required | Default |
|---|---|---:|---|
| maxHops | uint8 | YES | 2 |
| allowExoticRoutes | bool | YES | false |
| allowedIntermediates | address[] | OPTIONAL | [WBNB, USDT] |
| allowVenueSplitRouting | bool | YES | true |
| maxRouteCandidates | uint8 | YES | 5 |

**Rule:** No routing through non-whitelisted tokens except explicit intermediates.

---

### C.5.2 TradeGuardrails

| Field | Type | Required | Default |
|---|---|---:|---|
| minNetProfitUSDT | uint256 | YES | 3.00 USDT |
| gasBufferUSDT | uint256 | YES | 0.50 USDT |
| maxSlippageBps | uint16 | YES | 50 |
| slippageBufferBps | uint16 | YES | 30 |
| maxPriceImpactBps | uint16 | YES | 50 |
| mevPenaltyBps | uint16 | YES | 20 |
| executionUncertaintyBufferBps | uint16 | YES | 15 |
| minPoolLiquidityUSDT | uint256 | YES | 250000 USDT |
| allowTradeIfOracleMissing | bool | YES | false |

---

### C.5.3 SizingRules

| Field | Type | Required | Default |
|---|---|---:|---|
| minTradeSizeUSDT | uint256 | YES | 25 USDT |
| maxTradeSizePctOfPoolBps | uint16 | YES | 20 |
| maxCapitalDeploymentPct | uint16 | YES | 8000 (80%) |
| reserveHoldPct | uint16 | YES | 2000 (20%) |

**Rule:** v1.2 must never deploy 100% of vault value into active route exposure.

---

### C.5.4 ExecutionLimits

| Field | Type | Required | Default |
|---|---|---:|---|
| quoteScanIntervalSeconds | uint32 | YES | 60 |
| quoteScanJitterSeconds | uint16 | YES | 10 |
| minSecondsBetweenTrades | uint32 | YES | 900 |
| maxTradesPerDay | uint8 | YES | 12 |
| maxFailedTxPerHour | uint8 | YES | 3 |
| autoPauseOnFailure | bool | YES | true |

---

### C.5.5 OracleRules

| Field | Type | Required | Default |
|---|---|---:|---|
| oracleRequired | bool | YES | true |
| maxOracleAgeSeconds | uint32 | YES | 120 |
| maxOracleDeviationBps | uint16 | YES | 75 |
| fallbackToDexTwap | bool | YES | true |
| dexTwapWindowSeconds | uint32 | YES | 300 |

**Rule:** Oracle sanity checks must block execution when stale or deviating.

---

### C.5.6 FeeRules

| Field | Type | Required | Default |
|---|---|---:|---|
| performanceFeeBps | uint16 | YES | 2000 (20%) |
| feeChargedOnlyOnRealizedProfit | bool | YES | true |
| feeAsset | address | YES | USDT |
| feeSplitDevOpsPct | uint16 | YES | 2500 (25%) |
| feeSplitMarketingPct | uint16 | YES | 1000 (10%) |
| feeSplitRatchetPct | uint16 | YES | 5500 (55%) |
| feeSplitLoopLabsPct | uint16 | YES | 1000 (10%) |

**Rule:** Fee splits are expressed as % of the performance fee amount.

---

### C.5.7 ComplianceRules (Optional but Recommended)

| Field | Type | Required | Default |
|---|---|---:|---|
| enforceBlacklist | bool | OPTIONAL | true |
| blacklistContract | address | OPTIONAL | set by admin |
| enforceSanctionsList | bool | OPTIONAL | true |

---

## C.6 Strategy Hash (Execution Binding)

### C.6.1 Strategy Hash Requirement

Every Strategy Object must produce a single deterministic hash:

`strategyHash = keccak256(abi.encode(StrategyObject))`

The system must:
- store `strategyHash`,
- present it to user,
- require approval referencing it,
- reject execution if strategyHash mismatch.

---

## C.7 Approval Flow (Required)

### C.7.1 User Approval Record

When user approves strategy, record:

| Field | Type | Required |
|---|---|---:|
| approvedStrategyHash | bytes32 | YES |
| approvedAtUTC | uint64 | YES |
| approvalExpiryUTC | uint64 | YES |
| approvalNonce | uint64 | YES |

**Rule:** strategy execution must be blocked if:
- `block.timestamp > approvalExpiryUTC`
- strategyHash ≠ approvedStrategyHash

---

## C.8 Strategy Expiration Rules (Required)

| Parameter | Type | Default |
|---|---:|---:|
| strategyValiditySeconds | uint32 | 7200 (2 hours) |

**Rule:** strategies must expire quickly to prevent:
- stale quotes,
- stale market structure,
- users approving old conditions.

If expired:
- state transitions back to FUNDED_IDLE
- new strategy must be generated

---

## C.9 Deterministic Execution Rule

During EXECUTING:
- engine must obey allow-list venues,
- must obey whitelist assets,
- must obey `maxSlippageBps`, `maxPriceImpactBps`,
- must obey `minNetProfitUSDT` thresholds.

If any violation occurs:
- abort trade
- emit `ExecutionGuardrailRejected(...)`

---

## C.10 Required Events (Strategy Layer)

| Event | Emitted When |
|---|---|
| StrategyGenerated(vaultId, strategyHash, strategyType, validUntilUTC) | proposal created |
| StrategyPresented(vaultId, strategyHash) | presented to UI |
| StrategyApproved(vaultId, strategyHash, approvedAtUTC) | user accepted |
| StrategyExpired(vaultId, strategyHash) | expired before execution |
| StrategyRejected(vaultId, strategyHash) | user rejected |
| StrategyInvalidated(vaultId, oldHash, newHash) | superseded |
| ExecutionGuardrailRejected(vaultId, strategyHash, reasonCode) | blocked by guardrails |

---

## C.11 Guardrail Reject Reason Codes (Strategy Layer)

| Code | Meaning |
|---:|---|
| 1 | PROFIT_BELOW_MIN |
| 2 | GAS_TOO_HIGH |
| 3 | SLIPPAGE_EXCEEDED |
| 4 | PRICE_IMPACT_TOO_HIGH |
| 5 | LIQUIDITY_TOO_LOW |
| 6 | ORACLE_STALE |
| 7 | ORACLE_DEVIATION |
| 8 | ROUTE_NOT_ALLOWED |
| 9 | TOKEN_NOT_WHITELISTED |
| 10 | STRATEGY_EXPIRED |
| 11 | STRATEGY_HASH_MISMATCH |

---

## C.12 Non-Negotiable Dev Clause

Developer must NOT:
- invent new strategy types,
- bypass or relax guardrails,
- execute trades without hash-bound approval,
- accept deposits into non-whitelisted assets,
- route through third-party DEX venues.

Any such behavior is a protocol-breaking deviation from v1.2.

# Appendix D — Accounting + Ledger Specification (NAV, Profit, Settlement Math)

**Document:** YieldLoop — Automated Vault Trading Protocol + LOOP Rewards (BNB Chain)  
**Version:** v1.2 MVP  
**Appendix:** D  
**Status:** REQUIRED IMPLEMENTATION SPEC (Audit-Safe Deterministic Accounting)

---

## D.1 Purpose

YieldLoop must maintain deterministic, auditor-grade accounting at all times.

This appendix defines:
- vault ledger fields (what must be stored),
- NAV computation rules,
- realized profit computation rules,
- settlement sequence,
- fee extraction math,
- profit credit rules,
- suspense profit rules (for LOOP),
- event logging requirements.

Developer must implement these rules exactly. No improvisation.

---

## D.2 Core Accounting Principles (Hard Rules)

### D.2.1 Realized Profit Only

YieldLoop never “creates profit” from mark-to-market.

**Hard Rule:**
- Unrealized P&L may be displayed for UI only.
- Only realized profit can be credited to withdrawable balances.

### D.2.2 Profit Asset Is USDT

Withdrawable profit is always stored as USDT only.

**Hard Rule:**
- Profit cannot exist as BTCB/ETH/SOL/XRP/BNB in the withdrawable ledger.

### D.2.3 Performance Fee Charged Only on Realized Profit

**Hard Rule:**
- Protocol takes 20% fee **only** when profit is realized and confirmed by settlement.

---

## D.3 Vault Ledger Data Model (Required Fields)

### D.3.1 VaultLedger Struct (Recommended)

| Field | Type | Required | Meaning |
|---|---|---:|---|
| vaultId | uint256 | YES | Unique vault ID |
| owner | address | YES | Vault owner |
| baseAsset | address | YES | Deposit asset (must be whitelisted) |
| settlementAsset | address | YES | Must be USDT |
| status | uint8 | YES | State machine state |
| createdAtUTC | uint64 | YES | Vault creation time |
| lastSettlementUTC | uint64 | YES | Last settlement timestamp |
| depositPrincipalBase | uint256 | YES | Principal in base asset units at deposit |
| depositPrincipalUSDT | uint256 | YES | Principal valued in USDT at deposit time |
| principalRemainingUSDT | uint256 | YES | Current principal basis remaining (USDT basis) |
| withdrawableProfitUSDT | uint256 | YES | Profit available for anytime withdrawal |
| suspenseProfitUSDT | uint256 | YES | Profit allocated to LOOP buy/distribution |
| feesPaidTotalUSDT | uint256 | YES | Total realized fees paid to protocol |
| grossRealizedProfitUSDT | uint256 | YES | Total realized profit before fees |
| netRealizedProfitUSDT | uint256 | YES | Total realized profit after fees |
| totalWithdrawnProfitUSDT | uint256 | YES | Total profit withdrawn |
| lastNavUSDT | uint256 | YES | NAV snapshot for UI & audit |
| lastOraclePriceHash | bytes32 | YES | Price feed reference at settlement |
| strategyHashActive | bytes32 | YES | Approved strategy hash |
| tradeCountTotal | uint64 | YES | Total number of executed trades |

---

## D.4 NAV Computation Rules (For UI + Risk Checks)

### D.4.1 NAV Definition

**NAV = total market value of all vault-held assets + USDT balances**  
minus any in-flight execution reserved values.

NAV is calculated for:
- UI display
- guardrail decisioning
- reporting

### D.4.2 NAV Must Not Equal Profit

NAV may rise or fall. It is not “profit” until realized.

**Hard Rule:**
- NAV increases do not create withdrawable profit.

---

## D.5 “Realized Profit Event” Definition

A Realized Profit Event occurs when the system has:

1) Exited the trade (position is closed),
2) Converted the results into USDT (settlement asset),
3) Netted out costs and fees,
4) Updated the ledger.

In v1.2, realized profit is only credited during settlement.

---

## D.6 Settlement Process (Mandatory Sequence)

Settlement is a required deterministic sequence.

### D.6.1 Settlement Phase Entry Condition

Settlement begins only when:
- trade cycle has completed
- position is closed
- all required swaps completed

**State change:**
`EXECUTING → SETTLEMENT_PENDING`

### D.6.2 Settlement Required Steps (Exact Order)

1. Snapshot balances (all tokens + USDT)
2. Compute gross realized result in USDT
3. Compute costs and buffers (gas, slippage actuals)
4. Compute realized profit amount (if any)
5. Compute performance fee
6. Route fee allocations
7. Credit profit ledgers
8. Emit ledger snapshot
9. Exit settlement state

---

## D.7 Realized Profit Computation (Hard Math)

### D.7.1 Definitions

Let:

- `USDT_start` = settlementAsset balance at trade-cycle start
- `USDT_end` = settlementAsset balance at settlement completion
- `NAV_start_USDT` = NAV at trade-cycle start (in USDT)
- `NAV_end_USDT` = NAV at settlement completion (in USDT)

However, **realized profit is computed strictly from net settlement outcomes**, not NAV drift.

### D.7.2 Gross Result (Settlement Basis)

`grossResultUSDT = USDT_end - USDT_start`

### D.7.3 Gross Realized Profit

If `grossResultUSDT <= 0` then:
- `grossRealizedProfitUSDT = 0`
- loss applies to principalRemainingUSDT (see D.9)

If `grossResultUSDT > 0` then:
- `grossRealizedProfitUSDT = grossResultUSDT`

---

## D.8 Performance Fee Computation + Routing

### D.8.1 Fee Basis Rule

Fee is computed only if grossRealizedProfitUSDT > 0:

`feeUSDT = grossRealizedProfitUSDT * performanceFeeBps / 10000`

### D.8.2 Default Fee Rate

- performanceFeeBps = **2000** (20%)

### D.8.3 Fee Routing Splits (Of Fee Amount)

Fee splits are percentages of `feeUSDT`:

| Bucket | % of fee | Variable |
|---|---:|---|
| Dev/Ops | 25% | feeSplitDevOpsPct |
| Marketing | 10% | feeSplitMarketingPct |
| Ratchet System | 55% | feeSplitRatchetPct |
| Loop Labs | 10% | feeSplitLoopLabsPct |

### D.8.4 Routing Computation

Let:

- `devFee = feeUSDT * 0.25`
- `marketingFee = feeUSDT * 0.10`
- `ratchetFee = feeUSDT * 0.55`
- `loopLabsFee = feeUSDT * 0.10`

**Hard Rule:**
- all fee routing is executed in USDT
- fees cannot be routed in volatile tokens

---

## D.9 Loss Accounting Rules (Drawdown Handling)

YieldLoop must support losing trades.

### D.9.1 Loss Definition

If `grossResultUSDT < 0`:

`lossUSDT = abs(grossResultUSDT)`

### D.9.2 Loss Application

Loss reduces `principalRemainingUSDT`.

`principalRemainingUSDT = max(principalRemainingUSDT - lossUSDT, 0)`

### D.9.3 Fee on Loss

**Hard Rule:** No performance fee is charged on losses.

`feeUSDT = 0`

---

## D.10 Profit Credit Logic (Withdraw vs LOOP)

YieldLoop supports two profit output modes:

- **USDT Profit Withdraw Anytime**
- **Monthly LOOP Reward Settlement** (optional user election)

### D.10.1 Profit Split Rule

Let:

- `netProfitUSDT = grossRealizedProfitUSDT - feeUSDT`

Then route netProfit into ledgers based on user configuration:

- If user elected USDT profit:
  - `withdrawableProfitUSDT += netProfitUSDT`

- If user elected LOOP:
  - `suspenseProfitUSDT += netProfitUSDT`

- If user elected a split (allowed only if explicitly in v1.2 UI):
  - split proportionally (must be stored in strategy config)

**Hard Rule:**
- suspenseProfit is always USDT
- LOOP is purchased later from suspenseProfit per Appendix A

---

## D.11 Monthly LOOP Settlement Accounting (Suspense Ledger)

### D.11.1 Suspense Ledger Purpose

`suspenseProfitUSDT` represents:
- profit the user earned,
- held temporarily in USDT,
- used to buy LOOP during monthly distribution.

### D.11.2 LOOP Buy Conversion Rule

At distribution time:

1) For each vault with suspenseProfitUSDT > 0:
2) Execute market buy: USDT → LOOP
3) Credit user LOOP allocation
4) Set suspenseProfitUSDT → 0

**Hard Rule:**
- LOOP purchases must respect `loopBuyMaxSlippageBps`

---

## D.12 Withdrawals

### D.12.1 Profit Withdrawal Anytime

User may withdraw up to:

`withdrawableProfitUSDT`

Rules:
- may be called anytime vault is not in EXECUTING or CLOSE_PENDING
- triggers state: PROFIT_CREDITED → WITHDRAW_PROFIT_PENDING → PROFIT_CREDITED

### D.12.2 Principal Withdrawal (Closeout Only)

User may withdraw principal only when vault is CLOSED.

At closeout completion:
- vault must hold only USDT (except dust)
- principal + any remaining withdrawableProfitUSDT can be withdrawn

---

## D.13 Dust Rules

Dust is defined as any non-USDT token residual with USDT value below:

`dustThresholdUSDT = 0.50`

Dust must:
- be logged in settlement
- never block closing

---

## D.14 Required Settlement Events (Audit Trail)

At minimum, the following must be emitted per settlement:

| Event | Required Fields |
|---|---|
| SettlementStarted | vaultId, timestamp |
| SettlementCompleted | vaultId, grossProfitUSDT, feeUSDT, netProfitUSDT |
| FeesRouted | vaultId, devFee, marketingFee, ratchetFee, loopLabsFee |
| ProfitCredited | vaultId, creditedWithdrawableUSDT, creditedSuspenseUSDT |
| LedgerSnapshot | vaultId, principalRemainingUSDT, withdrawableProfitUSDT, suspenseProfitUSDT, navUSDT |

---

## D.15 Non-Negotiable Dev Clause

Developer must NOT:
- credit profit from NAV changes,
- credit profit in non-USDT assets,
- charge fees on losses,
- charge fees before settlement,
- allow principal withdrawal before closeout and CLOSED state,
- bypass suspense ledger accounting for LOOP.

Any violation breaks YieldLoop’s core trust model and invalidates v1.2.

# Appendix E — Engine Specifications (Execution, Router, Oracle, Settlement, Pause)

**Document:** YieldLoop — Automated Vault Trading Protocol + LOOP Rewards (BNB Chain)  
**Version:** v1.2 MVP  
**Appendix:** E  
**Status:** REQUIRED IMPLEMENTATION SPEC (Engines must match this architecture)

---

## E.1 Purpose

YieldLoop v1.2 is built as a modular engine system to ensure:
- deterministic behavior,
- safety-first execution,
- debuggable production operation,
- clean upgrade path.

This appendix defines the **exact engines required in v1.2**, their responsibilities, inputs, outputs, triggers, and non-negotiable rules.

Developer must implement these engines explicitly.  
No engine responsibilities may be merged in a way that changes behavior or reduces auditability.

---

## E.2 Engine List (v1.2 Required)

v1.2 must include at minimum:

1) **Vault Engine**
2) **Strategy Engine**
3) **Execution Engine**
4) **Router Engine**
5) **Oracle Engine**
6) **Settlement Engine**
7) **Fee Routing Engine**
8) **LOOP Rewards Engine**
9) **Pause Engine / Kill Switch Engine**
10) **Telemetry / Event Engine** (onchain event discipline)

---

## E.3 Vault Engine

### E.3.1 Purpose
Manage vault lifecycle, ownership, deposits, withdrawals, and allowed state transitions.

### E.3.2 Inputs
- deposit request (asset, amount)
- withdrawal request (profit)
- closeout request (principal)
- strategy approvals
- engine status updates (paused, resumed)

### E.3.3 Outputs
- vault state updates
- event emissions
- ledger updates (balances, timestamps)

### E.3.4 Hard Rules
- vault asset must be whitelisted
- principal cannot be withdrawn except via closeout
- vault state machine transitions must strictly match Appendix B

---

## E.4 Strategy Engine

### E.4.1 Purpose
Generate Strategy Objects and bind them to user approval.

### E.4.2 Triggers
- vault becomes FUNDED_IDLE
- previous strategy expires
- post-settlement return-to-idle

### E.4.3 Inputs
- vault balances and configuration
- global protocol parameters (Appendix A)
- venue availability status (Pause Engine signals)
- oracle validity (Oracle Engine signals)

### E.4.4 Outputs
- StrategyObject
- strategyHash (keccak256)
- STRATEGY_PROPOSED state update

### E.4.5 Hard Rules
- strategies expire after `strategyValiditySeconds`
- strategy must include all guardrails (no blank strategies)
- strategy must restrict venues to PCS/BISWAP only
- strategy must enforce settlementAsset = USDT

---

## E.5 Execution Engine

### E.5.1 Purpose
Execute approved strategy safely, opportunistically, and only when trade quality gate passes.

### E.5.2 Triggers
- strategy approved
- exit signal triggered
- closeout initiated (CLOSE_PENDING)

### E.5.3 Inputs
- approved strategyHash + StrategyObject
- live route candidates (Router Engine)
- oracle price data (Oracle Engine)
- trade-quality threshold checks (Trade Guardrails)

### E.5.4 Outputs
- swap execution transactions
- execution results (amounts, gas used)
- transition into HOLDING_POSITION or SETTLEMENT_PENDING

### E.5.5 Hard Rules
- may only execute swaps in EXECUTING or CLOSE_PENDING
- must reject stale strategy approval
- must reject if oracle invalid
- must enforce:
  - minNetProfitUSDT
  - maxSlippageBps
  - priceImpactCapBps
  - minPoolLiquidityUSDT
- must obey throttles:
  - minSecondsBetweenTrades
  - maxTradesPerDay

---

## E.6 Router Engine (PCS ↔ BiSwap)

### E.6.1 Purpose
Generate candidate routes and compute expected net results under constraints.

### E.6.2 Inputs
- token pair candidates (baseAsset ↔ USDT routes)
- DEX venue states
- pool liquidity data
- strategy route rules

### E.6.3 Outputs
- ranked route candidates:
  - route path
  - venue selection
  - expected output
  - expected slippage
  - expected price impact
  - expected net profit

### E.6.4 Hard Rules
- only PCS + BiSwap are allowed
- maxRouteHops ≤ 2
- must include route profitability computation net of:
  - gasBufferUSDT
  - slippageBufferBps
  - mevPenaltyBps
  - executionUncertaintyBufferBps
- must reject pools below minPoolLiquidityUSDT

---

## E.7 Oracle Engine

### E.7.1 Purpose
Provide reference pricing to prevent execution under manipulated conditions.

### E.7.2 Inputs
- oracle feed(s) (implementation choice permitted)
- DEX TWAP fallback (allowed)

### E.7.3 Outputs
- price reference + timestamp
- oracle health status:
  - VALID
  - STALE
  - DEVIATION_HIGH
  - UNAVAILABLE

### E.7.4 Hard Rules
- oracleRequired = true
- maxOracleAgeSeconds = 120
- maxOracleDeviationBps = 75
- must block execution if invalid
- must trigger Pause Engine if oracle invalid persists

---

## E.8 Settlement Engine

### E.8.1 Purpose
Perform deterministic settlement math and ledger updates after any trade cycle completion.

### E.8.2 Triggers
- TradeCycleFinished
- CLOSE_PENDING completion readiness

### E.8.3 Inputs
- pre-trade settlement snapshot
- post-trade asset balances
- fees configuration
- profit routing option (USDT vs LOOP suspense)

### E.8.4 Outputs
- gross realized profit
- feeUSDT
- netProfitUSDT
- ledger credits:
  - withdrawableProfitUSDT
  - suspenseProfitUSDT
  - principalRemainingUSDT
- emits LedgerSnapshot

### E.8.5 Hard Rules
- realized profit only (no mark-to-market credit)
- fee only on profit
- profit credits only as USDT

---

## E.9 Fee Routing Engine

### E.9.1 Purpose
Split and route fees from realized profit into protocol accounts.

### E.9.2 Inputs
- feeUSDT computed by Settlement Engine
- fee split parameters

### E.9.3 Outputs
- routed transfers:
  - dev/ops
  - marketing
  - ratchet
  - loop labs
- FeesRouted event

### E.9.4 Hard Rules
- routing must be USDT only
- fee split math must sum exactly to 100% of feeUSDT
- any mismatch must revert settlement

---

## E.10 LOOP Rewards Engine (Monthly)

### E.10.1 Purpose
Convert suspenseProfitUSDT into LOOP rewards via market buys and distribute to users.

### E.10.2 Triggers
- monthly schedule:
  - day 1 @ 00:00 UTC

### E.10.3 Inputs
- suspenseProfitUSDT per vault/user
- DEX venue routes
- LOOP buy slippage constraint

### E.10.4 Outputs
- LOOP purchased
- LOOP credited / transferred to user
- suspenseProfitUSDT reset to 0
- LOOPDistribution events

### E.10.5 Hard Rules
- loopMintingAllowed = false
- loopRewardSource = MARKET_BUY_WITH_SUSPENSE_USDT
- must obey loopBuyMaxSlippageBps
- must not execute if oracle invalid / DEX outage unless explicitly allowed (default: block)

---

## E.11 Pause Engine / Kill Switch Engine

### E.11.1 Purpose
Prevent protocol death by halting execution during unsafe conditions.

### E.11.2 Triggers (Auto-Pause)
- oracle stale/unavailable
- repeated tx failures
- high slippage events
- DEX outage
- admin kill switch

### E.11.3 Inputs
- Oracle Engine health
- Execution Engine failure counters
- Router Engine venue status

### E.11.4 Outputs
- per-vault pause
- global pause (if required)
- reason code events

### E.11.5 Hard Rules
- pausing must block:
  - execution trades
  - closeout unwind trades
- pausing must NOT block:
  - profit withdrawals (if balances are USDT and safe)
- resume must reset failure counters

---

## E.12 Telemetry / Event Engine

### E.12.1 Purpose
Guarantee production observability and audit trail completeness.

### E.12.2 Requirements
Must emit all events from:
- Appendix B (state machine)
- Appendix C (strategy binding)
- Appendix D (accounting settlement)

### E.12.3 Hard Rules
- any settlement without LedgerSnapshot is invalid
- any executed trade must emit TradeDetail
- any pause/resume must emit reason code

---

## E.13 Engine Interaction Diagram (Text Spec)

1) Vault Engine creates vault and accepts deposit
2) Strategy Engine generates StrategyObject + hash
3) User approves strategyHash
4) Execution Engine requests Router Engine candidates
5) Router Engine returns ranked routes
6) Oracle Engine validates price sanity
7) Execution Engine executes trade if gate passes
8) Settlement Engine computes realized profit/loss + credits ledgers
9) Fee Routing Engine routes protocol fees
10) Vault returns to idle for next opportunity
11) LOOP Rewards Engine runs monthly distribution for suspense balances
12) Pause Engine can halt 4–10 anytime based on safety conditions

---

## E.14 Non-Negotiable Dev Clause

Developer must NOT:
- collapse these engines into “one bot contract” without preserving behavior boundaries
- allow execution without strategy hash binding
- allow trading without oracle sanity
- allow profit credit in non-USDT
- allow fee-taking outside settlement

Any deviation breaks v1.2’s core trust model.

# Appendix F — Contract Inventory + Required Interfaces (Dev + Frontend Spec)

**Document:** YieldLoop — Automated Vault Trading Protocol + LOOP Rewards (BNB Chain)  
**Version:** v1.2 MVP  
**Appendix:** F  
**Status:** REQUIRED IMPLEMENTATION SPEC (Contract Map + Function Interfaces — No Improvisation Allowed)

---

## F.1 Purpose

This appendix defines:

1) the **exact contract inventory** required for YieldLoop v1.2, and  
2) the **required external interfaces** (public functions) each contract must expose.

This prevents:
- developer improvisation,
- missing functions needed by frontend,
- hidden admin powers,
- inconsistent vault behavior.

Developer may add additional functions, but must not remove or rename the required ones without owner approval.

---

## F.2 Contract Inventory (v1.2 Required)

### F.2.1 Core Contracts

1) YieldLoopCore  
2) VaultFactory  
3) YieldLoopVault (per-vault isolated contract)  
4) StrategyManager  
5) ExecutionManager  
6) Router (PCS/BiSwap only)  
7) OracleManager  
8) SettlementManager  
9) FeeRouter  
10) LoopRewardsManager  
11) BlacklistManager (optional but recommended)

---

## F.3 Permission + Role Model (Hard Lock)

### F.3.1 Roles

- DEFAULT_ADMIN_ROLE
  - assigns and revokes roles
  - subject to timelock requirement

- PROTOCOL_ADMIN
  - can pause/resume protocol
  - can adjust parameters (timelocked)
  - cannot withdraw user funds

- EXECUTOR_ROLE
  - keeper/bot role
  - can call execute functions

- SETTLER_ROLE
  - can call settlement functions

- REWARDS_OPERATOR_ROLE
  - can run monthly LOOP distribution conversion

- ORACLE_ADMIN_ROLE
  - can adjust oracle feeds (timelocked)

### F.3.2 Hard Rules

- Admin cannot transfer, drain, or seize user funds.
- Admin can pause execution, but cannot “move assets” from vaults.
- Any admin parameter changes must follow timelock rules defined in Appendix A.

---

## F.4 Required Public Interfaces (Plain English Specification)

All interfaces below are REQUIRED.

---

## F.5 Contract: YieldLoopCore

### F.5.1 Read Functions (Public)

- GetProtocolVersion()
  - returns: protocol version code (v1.2.0)

- GetChainId()
  - returns: chainId (BNB Chain)

- GetAllowedDexVenues()
  - returns: list or mask of allowed DEX venues
  - v1.2 allowed: PCS + BiSwap only

- GetWhitelistedVaultAssets()
  - returns: token whitelist (BTCB, ETH, SOL, XRP, BNB, USDT)

- IsProtocolPaused()
  - returns: true/false global pause

- GetProtocolParameters()
  - returns: all Appendix A parameters (or a query-by-key method)

### F.5.2 Admin Functions (Role Restricted, Timelocked)

- PauseProtocol(reasonCode)
- ResumeProtocol()

- SetDexAllowed(dexVenue, allowed)
- SetWhitelistAsset(asset, allowed)

- SetProtocolParameter(key, value)
- SetProtocolAddress(key, contractAddress)

Hard rule: parameter edits must emit audit events:
- ParameterChanged(key, oldValue, newValue, editedBy, timestamp)

---

## F.6 Contract: VaultFactory

### F.6.1 Write Functions (Public)

- CreateVault(baseAsset)
  - creates a new isolated vault
  - returns: vaultId + vaultAddress

### F.6.2 Read Functions (Public)

- GetVaultAddress(vaultId)
  - returns: vault address

- GetVaultsByOwner(owner)
  - returns: list of vaultIds

- TotalVaultCount()
  - returns: total vaults

Hard rules:
- no pooled shared vaults
- no “multi-owner vaults” in v1.2
- baseAsset must be whitelisted or vault creation is rejected

---

## F.7 Contract: YieldLoopVault (Per Vault)

This is the user-facing primary contract.

### F.7.1 Deposits

- Deposit(amount)
  - deposits baseAsset into vault
  - must enforce:
    - whitelisted asset
    - minimum deposit requirement
    - allowed state transitions only

Optional (recommended):
- DepositWithPermit(amount, permitData)

### F.7.2 Strategy Proposal + Approval

- GetProposedStrategyHash()
  - returns: current proposed strategyHash

- GetApprovedStrategyHash()
  - returns: currently approved hash (if any)

- ApproveStrategy(strategyHash)
  - user approval binds execution (Appendix C)

- RejectStrategy(strategyHash)
  - returns vault to FUNDED_IDLE

### F.7.3 Profit Withdrawal Anytime (USDT Only)

- WithdrawProfit(amountUSDT, recipient)
  - sends USDT from withdrawableProfitUSDT
  - must not allow principal withdrawal

- GetWithdrawableProfitUSDT()
  - returns: withdrawableProfitUSDT

Hard rule: withdrawable profit asset must always be USDT.

### F.7.4 Closeout / Principal Withdrawal

- RequestCloseout()
  - transitions vault into closeout flow (Appendix B)

- GetCloseoutStatus()
  - returns: closeoutRequested true/false, requestTimestampUTC

### F.7.5 Vault Read State / Ledger

- GetVaultState()
  - returns vault state enum

- GetVaultLedger()
  - returns the mandatory accounting fields defined in Appendix D:
    - principalRemainingUSDT
    - withdrawableProfitUSDT
    - suspenseProfitUSDT
    - feesPaidTotalUSDT
    - lastNavUSDT
    - lastSettlementUTC

---

## F.8 Contract: StrategyManager

### F.8.1 Strategy Generation

- GenerateStrategy(vaultId)
  - creates StrategyObject
  - returns strategyHash

- GetStrategyObject(strategyHash)
  - returns full strategy fields (encoded object or read-by-field methods)

- GetStrategyExpiry(strategyHash)
  - returns: validUntilUTC

### F.8.2 Strategy Validation

- IsStrategyValid(strategyHash)
  - returns true/false

- ValidateStrategyForVault(vaultId, strategyHash)
  - returns true/false

Hard rule: StrategyObject must contain the full constraints required by Appendix C.

---

## F.9 Contract: ExecutionManager

### F.9.1 Execution

- ExecuteVault(vaultId, strategyHash)
  - performs execution (trade cycle)
  - only allowed when:
    - vault approved strategyHash is valid
    - strategy not expired
    - protocol not paused
    - oracle valid
    - trade-quality gate passes

### F.9.2 Execution Eligibility Checks

- CanExecute(vaultId, strategyHash)
  - returns:
    - true/false
    - rejectReasonCode

### F.9.3 Execution Stats

- GetVaultExecutionStats(vaultId)
  - returns:
    - tradeCountTotal
    - failedTxLastHour
    - lastTradeUTC

Hard rule: execution must emit TradeDetail for each executed swap/arb.

---

## F.10 Contract: Router (PCS + BiSwap only)

### F.10.1 Route Candidates

- GetBestRoutes(tokenIn, tokenOut, amountIn)
  - returns:
    - ranked route candidates
    - expected output
    - expected slippage
    - expected price impact
    - expected net profit

Hard rules:
- max hops ≤ 2
- reject liquidity below minimum
- only PCS/BiSwap are valid venues in v1.2

---

## F.11 Contract: OracleManager

### F.11.1 Price Access

- GetOraclePrice(asset)
  - returns:
    - price value (USDT reference)
    - timestamp
    - status (VALID / STALE / DEVIATION / UNAVAILABLE)

### F.11.2 Oracle Health

- IsOracleValid()
  - returns true/false

Hard rule:
- invalid oracle blocks execution

---

## F.12 Contract: SettlementManager

### F.12.1 Settlement Entry

- SettleVault(vaultId)
  - executes the settlement sequence defined in Appendix D

### F.12.2 Read Settlement

- GetLastSettlement(vaultId)
  - returns:
    - last settlement timestamp
    - grossProfitUSDT
    - feeUSDT
    - netProfitUSDT

Hard rules:
- settlement must be deterministic
- performance fees only apply to realized profit
- ledger snapshot event is mandatory

---

## F.13 Contract: FeeRouter

### F.13.1 Fee Routing

- RouteFees(vaultId, feeUSDT)
  - routes fees into:
    - devOpsBucket
    - marketingBucket
    - ratchetBucket
    - loopLabsBucket

### F.13.2 Fee Buckets Read

- GetFeeBucketAddresses()
  - returns all fee wallet addresses

Hard rule: fee routing must be performed in USDT only.

---

## F.14 Contract: LoopRewardsManager

### F.14.1 Monthly Distribution

- RunMonthlyLoopDistribution()
  - converts suspenseProfitUSDT into LOOP by market buy
  - distributes LOOP to eligible users
  - resets suspenseProfitUSDT to 0

### F.14.2 Read Suspense

- GetVaultSuspenseProfitUSDT(vaultId)
  - returns suspense profit balance

Hard rules:
- LOOP is NOT minted in v1.2
- LOOP is purchased on market using suspense USDT (Appendix A)

---

## F.15 Contract: BlacklistManager (Optional but Recommended)

### F.15.1 Compliance Gate

- IsBlacklisted(address)
- SetBlacklisted(address, true/false)

Hard rule:
- if enforceBlacklist is enabled, blacklisted wallets cannot deposit or withdraw.

---

## F.16 Frontend Required Contract Calls (Minimum)

Frontend must support these functions:

1) Wallet Connect
2) CreateVault(baseAsset)
3) Deposit(amount)
4) View Proposed Strategy
5) ApproveStrategy()
6) RejectStrategy()
7) View vault ledger:
   - principalRemainingUSDT
   - withdrawableProfitUSDT
   - suspenseProfitUSDT
   - lastSettlementUTC
8) WithdrawProfit()
9) RequestCloseout()
10) View global pause + vault pause status

---

## F.17 Non-Negotiable Dev Clause

Developer must NOT:
- remove required functions,
- hide admin powers,
- create pooled vaults in v1.2,
- allow execution without strategy hash binding,
- allow profit in non-USDT ledger,
- mint LOOP in v1.2.

Any violation breaks the v1.2 trust model and invalidates the protocol spec.

# Appendix G — Frontend Screens + Required UI Data (Exact Pages + Fields + Actions)

**Document:** YieldLoop — Automated Vault Trading Protocol + LOOP Rewards (BNB Chain)  
**Version:** v1.2 MVP  
**Appendix:** G  
**Status:** REQUIRED IMPLEMENTATION SPEC (Frontend Must Match This)

---

## G.1 Purpose

This appendix defines the **exact frontend screens** required for YieldLoop v1.2, including:

- page list + navigation
- required UI sections
- required data fields and formatting
- required user actions (buttons)
- required warnings/disclosures

This prevents:
- frontend “guessing,”
- missing user controls,
- unclear financial UX,
- strategy approval mismatch.

---

## G.2 Frontend Design Rules (Hard Lock)

### G.2.1 UX Principles

- YieldLoop must feel like a professional financial app:
  - clean
  - simple
  - not gamified
  - no hype / no APY promises
- Users must always know:
  - vault principal basis
  - withdrawable profit
  - strategy status
  - whether funds are currently exposed
- Any strategy execution requires explicit user approval.

### G.2.2 UI Must Always Show Status

Every vault screen must display:
- vault state (from state machine)
- last activity timestamp
- current vault NAV (if available)
- withdrawable profit amount

---

## G.3 Page List (v1.2 Required Screens)

v1.2 frontend must include these screens:

1) Landing Page / Home
2) Wallet Connect Modal (or page)
3) Dashboard (Vault List)
4) Create Vault Page
5) Vault Detail Page
6) Strategy Proposal Page (inside vault detail)
7) Withdraw Profit Page / Modal
8) Close Vault (Principal Withdrawal) Page / Modal
9) Rewards Page (LOOP)
10) Activity / History Page (per vault)
11) Settings Page
12) Help / FAQ Page
13) Legal / Risk Disclosure Page
14) Pause / Incident Banner UI (global overlay)

---

## G.4 Screen Specifications

---

## 1) Landing Page / Home

### Purpose
Explain YieldLoop in plain language and direct users to connect wallet.

### Required Sections
- Hero summary:
  - “Automated Vault Trading Protocol (BNB Chain)”
  - “Self custody”
  - “Profit-based fees only”
- How it works (3–5 bullets)
- Core constraints:
  - Supported tokens list
  - Supported DEX venues list
- Primary CTA:
  - Connect Wallet

### Required Buttons
- Connect Wallet
- View Docs
- Risk Disclosure

---

## 2) Wallet Connect Modal / Page

### Required Elements
- wallet list (MetaMask, TrustWallet, WalletConnect)
- connected wallet address
- chain status indicator (BNB only)
- “Wrong chain” warning with switch network instruction

### Required Buttons
- Connect
- Disconnect
- Switch to BNB

---

## 3) Dashboard (Vault List)

### Required Table/List Fields Per Vault
- Vault ID
- Vault Asset (BTCB/ETH/SOL/XRP/BNB/USDT)
- Principal Remaining (USDT basis)
- Withdrawable Profit (USDT)
- Suspense Profit (USDT) [if LOOP enabled]
- Vault NAV (USDT) [if available]
- Vault Status (state)
- Last Action Timestamp

### Required Filters
- asset filter
- status filter
- “show closed vaults” toggle

### Required Buttons
- Create New Vault
- View Vault
- Withdraw Profit (quick action if available)

---

## 4) Create Vault Page

### Purpose
Create a new isolated vault and guide deposit.

### Required Inputs
- Base Asset dropdown (whitelist only)
- Deposit Amount input
- Confirmation checkboxes (risk acknowledgements)

### Required Disclosures
- “Profit can be withdrawn anytime (USDT)”
- “Principal requires vault closeout settlement”
- “Execution is opportunistic: may trade frequently or remain idle”
- “Fees apply only on realized profit”

### Required Buttons
- Create Vault
- Approve Token (if needed)
- Deposit

---

## 5) Vault Detail Page

### Purpose
Single vault control center.

### Required Sections

#### A) Vault Summary Panel (Top)
Must show:
- Vault ID
- Base asset
- Vault state (enum label)
- Principal remaining (USDT basis)
- Withdrawable profit (USDT)
- Suspense profit (USDT)
- Last settlement time
- Current NAV (USDT) if available
- Protocol fee rate (20% of realized profit)

#### B) Status + Safety Panel
- Oracle status (valid/stale)
- Venue availability (PCS/BiSwap)
- Pause status (global and vault-level)
- “Last execution attempt” timestamp

#### C) Strategy Panel
- Proposed strategy status:
  - none / proposed / approved / expired
- Proposed strategy hash
- Approved strategy hash
- Strategy expiry timestamp

#### D) Performance Panel
- cumulative realized profit
- cumulative fees paid
- total profit withdrawn

### Required Buttons
- View Strategy Proposal
- Approve Strategy
- Reject Strategy
- Withdraw Profit
- Request Closeout
- Refresh Vault Data

---

## 6) Strategy Proposal Page (Inside Vault)

### Purpose
Show strategy contents in human-readable terms and require explicit approval.

### Required Fields
- strategy type:
  - spread capture OR DEX arbitrage
- approved venues (PCS/BiSwap)
- settlement asset (USDT)
- max slippage
- max price impact
- min net profit required
- max route hops
- strategy expiry timestamp
- guardrail summary

### Required Checkboxes (must require user clicks)
- “I understand YieldLoop may trade or remain idle.”
- “I understand profit is only credited when realized and settled.”
- “I understand principal may decline due to losses.”

### Required Buttons
- Approve Strategy
- Reject Strategy

---

## 7) Withdraw Profit Modal / Page

### Purpose
Allow profit withdrawal anytime.

### Required Fields
- Withdrawable Profit USDT (read-only)
- Amount input
- Recipient wallet address (default = user wallet)
- Estimated gas cost

### Required Buttons
- Withdraw
- Max
- Cancel

### Required Warning
- “This withdrawal affects profits only. Principal remains in the vault.”

---

## 8) Close Vault (Principal Withdrawal) Modal / Page

### Purpose
Trigger vault closeout.

### Required Fields
- Principal remaining (USDT basis)
- Explanation of closeout process:
  - unwind
  - settle
  - close
- Maximum duration warning:
  - “Closeout may take up to 24 hours if conditions are unsafe.”

### Required Buttons
- Request Closeout
- Cancel

### Required Warning
- “Closeout may be delayed to avoid unsafe liquidation.”
- “YieldLoop may pause closeout if oracle/venue safety fails.”

---

## 9) Rewards Page (LOOP)

### Purpose
Explain LOOP rewards and show balances.

### Required Sections
- Explanation (plain language):
  - “If LOOP is selected, profit is held in suspense USDT”
  - “Monthly, that USDT buys LOOP on-market”
  - “LOOP is not minted in v1.2”
- Current suspense balance per vault
- Next distribution time (UTC)
- Claim window details

### Required Buttons
- View Reward History
- View Distribution Schedule

---

## 10) Activity / History Page (Per Vault)

### Purpose
Audit-style event trail.

### Required Filters
- by event type
- by date range

### Required History Items
- Deposit confirmed
- Strategy proposed/approved/rejected
- Execution started/completed
- Settlement completed
- Profit credited
- Fees routed
- Profit withdrawn
- Pause/resume events
- Closeout requested/completed

---

## 11) Settings Page

### Required Controls
- Default recipient address
- Display settings (light/dark)
- Timezone display note: system uses UTC

Optional:
- export data JSON/CSV (read-only)

---

## 12) Help / FAQ Page

### Required Topics
- “What is YieldLoop?”
- “When can I withdraw?”
- “What counts as profit?”
- “What are the risks?”
- “What is a strategy proposal?”
- “Why do I need to approve strategy?”
- “Why does closeout take time?”
- “What is LOOP?”
- “Is LOOP minted?” (Answer: No in v1.2)

---

## 13) Legal / Risk Disclosure Page

Must include (non-negotiable):
- “Not financial advice”
- “DeFi is risky”
- “Loss of principal possible”
- “Protocol may pause”
- “Strategy execution is conditional”
- “Oracle/DEX failure may delay settlement or closeout”
- sanctions/blacklist policy if enabled

---

## 14) Pause / Incident Banner UI (Global Overlay)

### Purpose
Immediate visibility if paused.

### Required Data
- protocol paused status (yes/no)
- pause reason code
- estimated user impact:
  - execution disabled
  - settlement disabled
  - withdrawals enabled (if applicable)

### Required Button
- View Incident Details

---

## G.5 Required Data Formatting Rules

- USDT amounts show:
  - 2 decimals
  - commas
  - label “USDT”
- Time shown:
  - UTC timestamp + local conversion (display both)
- Vault state shown:
  - enum name + short description

---

## G.6 Non-Negotiable Frontend Rule

Frontend must NOT:
- show APY
- show guaranteed returns
- advertise LOOP as profit promise
- allow strategy execution without approval
- allow hiding risk disclosures behind tiny UI

---

## G.7 MVP Completion Gate

Frontend is not “done” unless user can complete:

1) connect wallet  
2) create vault  
3) deposit  
4) review strategy  
5) approve strategy  
6) withdraw profit  
7) request closeout  
8) view activity log  
9) view LOOP suspense status  

# Appendix H — Risk Disclosure + User Contract Signature Page (Mandatory Click-Through)

**Document:** YieldLoop — Automated Vault Trading Protocol + LOOP Rewards (BNB Chain)  
**Version:** v1.2 MVP  
**Appendix:** H  
**Status:** REQUIRED IMPLEMENTATION SPEC (User Must Agree Before Deposit / Approval)

---

## H.1 Purpose

This appendix defines the **mandatory click-through agreement** language for YieldLoop v1.2.

It must be implemented as:
- a **user contract signature page** (checkbox + signature / acceptance button)
- shown **before first deposit**
- shown again (short form) before **strategy approval**
- stored as an onchain/offchain immutable acceptance record:
  - wallet address
  - timestamp
  - version number

This prevents:
- legal ambiguity
- user confusion
- “I didn’t know” disputes

---

## H.2 Mandatory Agreement Rules (Hard Lock)

### H.2.1 User Must Accept Before Using YieldLoop

User must accept ALL:
- General Terms
- Risk Disclosure
- Non-Custodial Understanding
- Strategy Approval Understanding
- LOOP Rewards Understanding
- Blacklist / Sanctions Compliance (if enabled)

### H.2.2 Acceptance Record Must Include

- wallet address
- agreement version: `YieldLoop_Agreement_v1.2`
- timestamp (UTC)
- chain id (BNB chain)
- client signature type:
  - “checkbox acceptance”
  - “wallet signature” (recommended)

---

## H.3 Full Risk Disclosure Page (Long Form)

### Title
**YieldLoop v1.2 — Risk Disclosure + User Agreement**

### Intro (Must Display)
YieldLoop is a self-custody DeFi protocol that executes automated trading strategies under user-approved constraints.  
YieldLoop is **not a bank**, is **not insured**, and does not guarantee profit.  
You may lose some or all of your principal.

---

## H.4 The Agreement (Long Form Text)

### Section 1 — Non-Custodial Design

By using YieldLoop, I acknowledge:

1. YieldLoop is a **non-custodial DeFi protocol**.
2. I connect my own wallet and remain responsible for my wallet security.
3. YieldLoop does not hold my funds in a centralized account.
4. If I lose access to my wallet or seed phrase, YieldLoop cannot recover my funds.
5. I am responsible for securing my devices and private keys.

---

### Section 2 — Not Financial Advice

I acknowledge:

1. YieldLoop does not provide financial advice, investment advice, tax advice, or legal advice.
2. Any protocol output is a deterministic automation outcome, not a recommendation.
3. I am solely responsible for deciding whether to deposit funds and accept any strategy.

---

### Section 3 — Principal Risk / Loss Risk (Critical)

I acknowledge and accept that:

1. YieldLoop trading strategies can lose money.
2. **Loss of principal is possible.**
3. I may receive less than my initial deposit after settlement or closeout.
4. YieldLoop does not promise, guarantee, or imply any future profit.

---

### Section 4 — Realized Profit Only

I acknowledge:

1. “Profit” is not credited from price movement alone.
2. YieldLoop credits profit only when:
   - a position is closed, and
   - settlement is completed.
3. Unrealized changes in NAV are not withdrawable profit.

---

### Section 5 — Profit Withdrawals vs Principal Withdrawals

I acknowledge:

1. YieldLoop allows withdrawal of **realized profit (USDT)** when credited as withdrawable profit.
2. YieldLoop does **not** allow principal withdrawal at any arbitrary time.
3. Principal withdrawal requires:
   - closeout request
   - unwind
   - final settlement
   - vault close confirmation
4. Closeout may take time and may be delayed for safety reasons.

---

### Section 6 — Execution Is Conditional / Opportunistic

I acknowledge:

1. YieldLoop does not trade continuously in all conditions.
2. Execution occurs only when strategy rules and guardrails permit.
3. YieldLoop may remain idle for long periods if no safe profitable opportunities exist.
4. I agree that inactivity is not failure; it is a safety design.

---

### Section 7 — Strategy Approval Gate

I acknowledge:

1. YieldLoop generates a Strategy Proposal.
2. Strategy execution requires explicit user approval.
3. If I do not approve the strategy, no execution can occur.
4. If the strategy expires, a new strategy may be generated and must be approved again.

---

### Section 8 — Smart Contract Risk

I acknowledge:

1. Smart contracts may have vulnerabilities.
2. Exploits may occur even with audits and safety rules.
3. YieldLoop has no obligation or ability to reimburse losses from:
   - exploits
   - hacks
   - logic bugs
   - third-party failures

---

### Section 9 — DEX + Third-Party Protocol Risk

I acknowledge:

1. YieldLoop uses third-party DEX venues (PCS and BiSwap).
2. These venues may fail, be exploited, or behave unexpectedly.
3. Liquidity issues may cause:
   - unfavorable execution
   - slippage
   - inability to unwind quickly
4. YieldLoop may pause execution or closeout if venues are unsafe or unavailable.

---

### Section 10 — Oracle Risk

I acknowledge:

1. YieldLoop uses oracle-based sanity checks.
2. Oracles may become:
   - stale
   - unavailable
   - manipulated
3. Oracle issues may cause:
   - execution pause
   - delay in settlement
   - delay in closeout

---

### Section 11 — Network / Gas Risk

I acknowledge:

1. Transactions require gas and may fail.
2. Congestion may cause:
   - higher gas fees
   - delayed execution
   - failed settlement attempts
3. YieldLoop may pause or delay actions during unsafe network conditions.

---

### Section 12 — LOOP Rewards (Specific Disclosure)

I acknowledge:

1. LOOP rewards are optional.
2. If I select LOOP rewards:
   - my realized profit may be held temporarily as **suspense USDT**
   - monthly, that USDT is used to purchase LOOP on market
3. LOOP value may be volatile.
4. LOOP rewards are not a guarantee of gain and may decline in value after distribution.
5. In v1.2, LOOP is **not minted as guaranteed profit** and does not create profit.

---

### Section 13 — No Insurance / No Recovery

I acknowledge:

1. YieldLoop has no insurance coverage.
2. There is no guarantee of recovery after loss events.
3. The user bears all risk of using DeFi.

---

### Section 14 — Protocol Pause / Emergency Actions

I acknowledge:

1. YieldLoop may pause execution at any time to protect users.
2. Pause may block:
   - trading
   - settlement actions
   - closeout unwind actions
3. Pause is a safety measure and may cause delays.

---

### Section 15 — Compliance / Sanctions (If Enabled)

I acknowledge:

1. YieldLoop may block access for:
   - sanctioned jurisdictions
   - blacklisted addresses
   - flagged wallets
2. Deposits or withdrawals may be refused if compliance rules require it.

---

### Section 16 — User Confirmation

I confirm that:

- I have read and understand this agreement.
- I understand YieldLoop is risky.
- I accept that I can lose money.
- I choose to use YieldLoop anyway.

---

## H.5 Mandatory Checkboxes (User Must Tick All)

UI must require ALL boxes checked before proceeding:

[ ] I understand YieldLoop is non-custodial DeFi and I am responsible for my wallet.  
[ ] I understand this is not financial advice and no profit is guaranteed.  
[ ] I understand I may lose part or all of my principal.  
[ ] I understand profit is only credited when realized and settled.  
[ ] I understand principal withdrawal requires closeout settlement and may be delayed.  
[ ] I understand execution is conditional and may remain idle.  
[ ] I understand smart contracts and DEX venues can fail or be exploited.  
[ ] I understand there is no insurance or reimbursement.  
[ ] If selecting LOOP: I understand my profit may be converted into LOOP and LOOP can lose value.  

---

## H.6 Signature / Acceptance Button (Required)

Button text must be explicit:

**“I ACCEPT — PROCEED TO YIELDLOOP”**

Must not be:
- “Continue”
- “Next”
- “I agree” (too vague)

---

## H.7 Short Form Disclosure (Before Strategy Approval)

Before approving any strategy, user must be shown a short disclosure:

### Strategy Approval Warning (Short Form)
This strategy is an automated execution rule set.

- Profit is not guaranteed.
- Loss of principal is possible.
- Execution only occurs if conditions meet guardrails.
- This strategy expires if not executed in time.

Checkbox:
[ ] I understand and approve this strategy for execution.

Button:
**APPROVE STRATEGY**

---

## H.8 Short Form Disclosure (Before Deposit)

Before deposit, user must be shown:

### Deposit Warning (Short Form)
Deposits are at risk in DeFi trading.

- Loss of principal is possible.
- Withdrawable profit may be available only after settlement.
- Principal withdrawal requires closeout settlement.

Checkbox:
[ ] I understand the deposit risks.

Button:
**CONFIRM DEPOSIT**

---

## H.9 Non-Negotiable Implementation Clause

Frontend must NOT:
- hide this behind a single link
- auto-check boxes
- allow deposit or strategy approval without acceptance

This agreement is a mandatory protocol safety requirement.

# Appendix I — End-to-End User Flow (Happy Path + Failure Paths)

**Document:** YieldLoop — Automated Vault Trading Protocol + LOOP Rewards (BNB Chain)  
**Version:** v1.2 MVP  
**Appendix:** I  
**Status:** REQUIRED IMPLEMENTATION SPEC (Full UX Flow + System Behavior)

---

## I.1 Purpose

This appendix defines the complete end-to-end flow of YieldLoop v1.2, including:

- Happy path (standard success flow)
- Strategy gating flow
- Execution + settlement flow
- Profit withdrawal flow
- Closeout flow
- Common failure paths (oracle, slippage, DEX outage, pause)
- What the user sees and what the system does

Developer must implement these flows exactly.  
Frontend must reflect the flow exactly.

---

## I.2 System Design Summary (User Reality)

YieldLoop is:

- user-controlled (self custody)
- deterministic
- guardrail-driven
- profit credited only when realized
- profit withdrawable anytime (USDT only)
- principal withdrawable only via closeout settlement

Execution is opportunistic:
- may trade often
- may trade rarely
- may remain idle

This is expected and correct behavior.

---

## I.3 Happy Path Flow (Standard Use)

### Step 1 — User Visits App
**User sees:**
- landing page
- “Connect Wallet”

**System does:**
- loads protocol version
- checks chain/network status

---

### Step 2 — User Connects Wallet
**User sees:**
- wallet connected
- BNB chain verified
- dashboard available

**System does:**
- stores session
- checks blacklist status (if enabled)

---

### Step 3 — User Accepts Risk Agreement
**User sees:**
- Risk Disclosure + Agreement page (Appendix H)

**User must do:**
- check all boxes
- click: “I ACCEPT — PROCEED TO YIELDLOOP”

**System does:**
- records acceptance:
  - wallet
  - timestamp
  - agreement version

---

### Step 4 — Create Vault
**User sees:**
- Create Vault page
- whitelist assets dropdown

**User selects:**
- base asset (ex: USDT)

**User action:**
- Create Vault

**System does:**
- VaultFactory deploys isolated vault
- returns vaultId + vaultAddress
- vault enters:
  - VAULT_CREATED

---

### Step 5 — Deposit Into Vault
**User sees:**
- deposit amount input
- minimum deposit enforcement

**User action:**
- deposit funds

**System does:**
- vault enters:
  - DEPOSIT_PENDING
- deposit confirmed:
  - FUNDED_IDLE
- emits DepositConfirmed

---

### Step 6 — Strategy Proposal Appears
**User sees:**
- “Strategy Proposed”
- strategy details and hash
- guardrails summary

**System does:**
- StrategyManager generates strategy object
- vault enters:
  - STRATEGY_PROPOSED

---

### Step 7 — User Approves Strategy
**User action:**
- reads strategy
- checks “I understand” box
- clicks “Approve Strategy”

**System does:**
- stores approvedStrategyHash
- vault enters:
  - STRATEGY_APPROVED

---

### Step 8 — System Executes When Opportunity Exists
**User sees (Vault Detail):**
- status changes:
  - EXECUTING
- “Execution running”
- then:
  - HOLDING_POSITION

**System does:**
- Router finds ranked routes
- Oracle validates
- ExecutionManager trades only if:
  - trade-quality gate passes
- emits TradeDetail for every swap

---

### Step 9 — System Settles
**User sees:**
- “Settling”
- then:
  - “Profit Credited” OR “No Profit”

**System does:**
- vault enters:
  - SETTLEMENT_PENDING
- SettlementManager computes:
  - grossProfitUSDT
  - feeUSDT
  - netProfitUSDT
- FeeRouter routes protocol fee
- credits:
  - withdrawableProfitUSDT OR suspenseProfitUSDT
- vault enters:
  - PROFIT_CREDITED
- emits LedgerSnapshot

---

### Step 10 — User Withdraws Profit Anytime
**User sees:**
- withdrawable profit balance
- “Withdraw” button

**User action:**
- withdraw some or all profit

**System does:**
- transitions:
  - WITHDRAW_PROFIT_PENDING → PROFIT_CREDITED
- transfers USDT to user wallet

---

## I.4 LOOP Rewards Flow (Optional Path)

### Step A — User Selects LOOP Rewards Option
**User sees:**
- “Select Profit Type: USDT or LOOP”

If user chooses LOOP:
- profit is routed to suspenseProfitUSDT

**System does:**
- credits suspense ledger during settlement

---

### Step B — Monthly Distribution
**User sees:**
- Rewards page:
  - suspense profit amount
  - next distribution date (UTC)

**System does (monthly day 1 @ 00:00 UTC):**
- buys LOOP on market using suspense USDT
- distributes LOOP
- suspenseProfitUSDT reset to 0
- emits LOOPDistributionCompleted

---

## I.5 Closeout Flow (Principal Withdrawal)

### Step 1 — User Requests Closeout
**User sees:**
- warning:
  - “Closeout may take up to 24h”
  - “May delay to avoid unsafe liquidation”

**User action:**
- Request Closeout

**System does:**
- state changes:
  - CLOSE_REQUESTED → CLOSE_PENDING

---

### Step 2 — Unwind + Settlement
**User sees:**
- “Closeout Pending”

**System does:**
- liquidates/unwinds all positions into USDT
- must obey guardrails:
  - max slippage
  - oracle sanity
  - liquidity checks
- settles final ledger

---

### Step 3 — Vault Closed
**User sees:**
- “Vault CLOSED”
- principal withdraw button enabled

**System does:**
- sets state:
  - CLOSED
- makes principal withdrawable

---

## I.6 Failure Paths (System Must Handle)

---

## I.6.1 Strategy Expired (Common)
**Cause:**
- user did not approve in time
- market conditions changed

**User sees:**
- “Strategy Expired — New Strategy Required”

**System does:**
- emits StrategyExpired
- returns to:
  - FUNDED_IDLE
- generates a new proposal

---

## I.6.2 Oracle Stale / Oracle Deviation
**Cause:**
- oracle feed stale
- oracle price deviates beyond threshold

**User sees:**
- “Execution Paused — Oracle Unsafe”

**System does:**
- Pause Engine triggers
- vault enters:
  - PAUSED
- execution blocked
- settlement blocked if unsafe
- withdrawals of credited profit may remain available if safe

---

## I.6.3 Slippage Exceeded
**Cause:**
- route slippage beyond cap

**User sees:**
- “Execution Blocked — Slippage Too High”

**System does:**
- no execution occurs
- emits ExecutionGuardrailRejected(SLIPPAGE_EXCEEDED)
- remains in:
  - FUNDED_IDLE or HOLDING_POSITION depending on step

---

## I.6.4 DEX Outage / PCS-BiSwap Failure
**Cause:**
- router cannot quote
- swap fails repeatedly

**User sees:**
- “Venue Unavailable — Execution Paused”

**System does:**
- Pause Engine triggers
- vault enters:
  - PAUSED
- retries only after cooldown

---

## I.6.5 Repeated TX Failure
**Cause:**
- failed swaps
- failed settlement calls
- out-of-gas patterns

**User sees:**
- “Execution Paused — Repeated Failures”

**System does:**
- failure counter increments
- after threshold:
  - PAUSED

---

## I.6.6 Market Conditions Never Meet Gate
**Cause:**
- not enough edge
- not enough liquidity
- gas too high

**User sees:**
- “No Valid Opportunities — Vault Idle”

**System does:**
- no execution
- remains in FUNDED_IDLE

This is correct and expected behavior.

---

## I.6.7 Closeout Delayed For Safety
**Cause:**
- liquidity too thin
- oracle invalid
- slippage too high

**User sees:**
- “Closeout delayed to avoid unsafe liquidation”

**System does:**
- remains in CLOSE_PENDING
- pauses if required
- resumes once safe

---

## I.7 User Messaging Rules (Non-Negotiable)

Frontend copy must not claim:
- guaranteed profit
- predictable execution frequency
- “APY”

Frontend must say:
- “Execution is conditional”
- “Loss of principal possible”
- “Profit credited only when realized and settled”
- “Closeout may delay for safety”

---

## I.8 Completion Checklist (Dev QA Gate)

A build is not complete until testnet QA confirms:

- user can create vault
- user can deposit
- system generates strategy
- user can approve strategy
- execution occurs only when gate passes
- settlement credits profit correctly
- fees are routed correctly
- profit withdrawal works anytime (USDT only)
- closeout works (principal withdrawn only at end)
- oracle failure pauses execution
- slippage failure blocks execution
- DEX outage pauses execution
- full event log is emitted for all steps

# Appendix J — Test Cases + Acceptance Criteria (Dev Must Pass Before Launch)

**Document:** YieldLoop — Automated Vault Trading Protocol + LOOP Rewards (BNB Chain)  
**Version:** v1.2 MVP  
**Appendix:** J  
**Status:** REQUIRED IMPLEMENTATION SPEC (QA / Audit / Launch Gate)

---

## J.1 Purpose

This appendix defines the required **test cases** and **acceptance criteria** for YieldLoop v1.2.

Developer must implement automated tests and demonstrate:
- unit test coverage for core logic
- integration tests for real DEX routing behavior
- simulation tests for pause/failure conditions

**Launch is prohibited** until these acceptance criteria are passed.

---

## J.2 Testing Environment Requirements

### J.2.1 Networks

Required testing environments:
- Local fork (BNB chain fork)
- BNB testnet (integration)
- Dry-run simulation mode (no swaps executed, but full routing + checks performed)

### J.2.2 Required External Components

- PCS Router mock + fork integration
- BiSwap Router mock + fork integration
- Oracle feed mock + stale/deviation simulation
- Gas estimator mock + congestion simulation

---

## J.3 Unit Test Suite (Required)

---

## J.3.1 Vault Creation & Whitelist Tests

### TEST 001 — Create vault with whitelisted asset
**Precondition:** baseAsset = USDT  
**Expected:**
- vault created
- vault state = VAULT_CREATED
- emits VaultCreated

✅ PASS CRITERIA:
- VaultFactory returns vaultId + vaultAddress
- ledger initialized with zeros, owner set

---

### TEST 002 — Create vault with non-whitelisted asset
**Precondition:** baseAsset = random token  
**Expected:**
- transaction reverts

✅ PASS CRITERIA:
- revert reason: ASSET_NOT_WHITELISTED

---

## J.3.2 Deposit Tests

### TEST 010 — Deposit valid amount
**Precondition:** vault exists  
**Action:** deposit 250 USDT  
**Expected:**
- DEPOSIT_PENDING → FUNDED_IDLE
- DepositConfirmed event emitted

✅ PASS CRITERIA:
- vault receives funds
- ledger deposit values updated correctly

---

### TEST 011 — Deposit below minimum
**Action:** deposit 100 USDT  
**Expected:**
- revert

✅ PASS CRITERIA:
- revert reason: MIN_DEPOSIT_NOT_MET

---

### TEST 012 — Deposit in invalid state
**Precondition:** vault status = EXECUTING  
**Expected:**
- revert

✅ PASS CRITERIA:
- revert reason: INVALID_STATE_FOR_DEPOSIT

---

## J.3.3 Strategy Generation + Expiry Tests

### TEST 020 — Strategy generated in FUNDED_IDLE
**Precondition:** vault FUNDED_IDLE  
**Expected:**
- STRATEGY_PROPOSED
- proposedStrategyHash set

✅ PASS CRITERIA:
- StrategyObject created
- strategyHash stored
- StrategyGenerated event emitted

---

### TEST 021 — Strategy expires after validity window
**Precondition:** strategy created  
**Action:** advance time > strategyValiditySeconds  
**Expected:**
- StrategyExpired event
- vault returns FUNDED_IDLE

✅ PASS CRITERIA:
- expired strategy cannot be executed

---

## J.3.4 Strategy Approval Binding Tests

### TEST 030 — Approve valid strategy
**Expected:**
- STRATEGY_APPROVED
- approvedStrategyHash set

✅ PASS CRITERIA:
- approval timestamp stored
- approval binds execution eligibility

---

### TEST 031 — Reject strategy
**Expected:**
- STRATEGY_PROPOSED → FUNDED_IDLE

✅ PASS CRITERIA:
- strategy no longer executable

---

### TEST 032 — Approve wrong strategy hash
**Action:** approve invalid strategyHash  
**Expected:**
- revert

✅ PASS CRITERIA:
- revert reason: STRATEGY_HASH_INVALID

---

## J.3.5 Execution Guardrail Tests

### TEST 040 — Execute without approval
**Expected:**
- revert

✅ PASS CRITERIA:
- revert reason: STRATEGY_NOT_APPROVED

---

### TEST 041 — Execute with expired approval
**Expected:**
- revert

✅ PASS CRITERIA:
- revert reason: STRATEGY_EXPIRED

---

### TEST 042 — Reject trade below minNetProfitUSDT
**Setup:** route net profit = $2.50  
**Expected:**
- no trade executed
- ExecutionGuardrailRejected(PROFIT_BELOW_MIN)

✅ PASS CRITERIA:
- vault remains safe state
- no asset movement occurs

---

### TEST 043 — Reject trade above slippage cap
**Setup:** expected slippage > maxSlippageBpsPerSwap  
**Expected:**
- reject

✅ PASS CRITERIA:
- ExecutionGuardrailRejected(SLIPPAGE_EXCEEDED)

---

### TEST 044 — Reject trade above impact cap
**Setup:** price impact > maxPriceImpactBps  
**Expected:**
- reject

✅ PASS CRITERIA:
- ExecutionGuardrailRejected(PRICE_IMPACT_TOO_HIGH)

---

### TEST 045 — Reject trade below liquidity threshold
**Setup:** pool liquidity < minPoolLiquidityUSDT  
**Expected:**
- reject

✅ PASS CRITERIA:
- ExecutionGuardrailRejected(LIQUIDITY_TOO_LOW)

---

## J.3.6 Oracle Tests

### TEST 050 — Execute blocked when oracle stale
**Setup:** oracle age > maxOracleAgeSeconds  
**Expected:**
- execution blocked
- vault paused if persistent

✅ PASS CRITERIA:
- reject reason: ORACLE_STALE

---

### TEST 051 — Execute blocked when oracle deviation high
**Setup:** deviation > maxOracleDeviationBps  
**Expected:**
- block

✅ PASS CRITERIA:
- reject reason: ORACLE_DEVIATION

---

## J.3.7 Settlement Tests (Profit / Fee / Ledger)

### TEST 060 — Profit settlement credits withdrawableProfitUSDT
**Setup:** trade closes with $10 gross profit  
**Expected:**
- fee = $2.00
- net profit = $8.00
- withdrawableProfitUSDT += 8.00
- feesPaidTotalUSDT += 2.00

✅ PASS CRITERIA:
- LedgerSnapshot emitted
- FeeRouter receives correct splits

---

### TEST 061 — Loss settlement reduces principalRemainingUSDT
**Setup:** trade ends with -$20  
**Expected:**
- no fee
- principalRemainingUSDT decreases by 20

✅ PASS CRITERIA:
- no fees routed
- ledger consistent

---

### TEST 062 — Fee split routing math correct
**Setup:** feeUSDT = 10  
**Expected:**
- devOps = 2.50
- marketing = 1.00
- ratchet = 5.50
- loopLabs = 1.00

✅ PASS CRITERIA:
- transfers match exactly
- sum equals feeUSDT

---

### TEST 063 — Profit credited only in USDT
**Expected:**
- withdrawableProfit asset always USDT
- no profit ledger entries in other tokens

✅ PASS CRITERIA:
- strict enforcement

---

## J.3.8 Profit Withdrawal Tests

### TEST 070 — Withdraw profit partial
**Setup:** withdrawableProfitUSDT = 10  
**Action:** withdraw 3  
**Expected:**
- transfer 3 to user
- withdrawableProfitUSDT = 7

✅ PASS CRITERIA:
- state transitions correct
- event emitted

---

### TEST 071 — Withdraw profit full
**Action:** withdraw 10  
**Expected:**
- withdrawableProfitUSDT = 0

✅ PASS CRITERIA:
- clean zero state

---

### TEST 072 — Withdraw profit above balance
**Expected:**
- revert

✅ PASS CRITERIA:
- revert reason: INSUFFICIENT_WITHDRAWABLE_PROFIT

---

## J.3.9 Closeout Tests

### TEST 080 — Closeout request from FUNDED_IDLE
**Expected:**
- CLOSE_REQUESTED → CLOSE_PENDING

✅ PASS CRITERIA:
- closeout timestamp stored

---

### TEST 081 — Closeout completes only when positions unwound
**Setup:** some token exposure exists  
**Expected:**
- closeout not marked complete

✅ PASS CRITERIA:
- requires USDT-only balances except dust

---

### TEST 082 — Closeout delayed for unsafe conditions
**Setup:** oracle invalid OR slippage too high  
**Expected:**
- closeout remains pending
- pause triggered if required

✅ PASS CRITERIA:
- no forced liquidation

---

### TEST 083 — Principal withdrawal blocked before CLOSED
**Expected:**
- revert

✅ PASS CRITERIA:
- revert reason: PRINCIPAL_WITHDRAW_NOT_ALLOWED

---

## J.3.10 Pause Engine Tests

### TEST 090 — Pause triggered by repeated tx failure
**Setup:** 3 failed tx within hour  
**Expected:**
- PAUSED state

✅ PASS CRITERIA:
- VaultPaused(reason TX_FAILURE_LIMIT)

---

### TEST 091 — Resume resets failure counters
**Expected:**
- failure counters reset
- returns FUNDED_IDLE

✅ PASS CRITERIA:
- vault executable again when safe

---

## J.3.11 LOOP Rewards Tests

### TEST 100 — Suspense profit credits correctly
**Setup:** user selected LOOP, profit net = 8  
**Expected:**
- suspenseProfitUSDT += 8
- withdrawableProfitUSDT unchanged

✅ PASS CRITERIA:
- strict separation

---

### TEST 101 — Monthly distribution converts suspense USDT to LOOP
**Setup:** suspenseProfitUSDT > 0  
**Expected:**
- LOOP purchased
- credited to user
- suspense reset to 0

✅ PASS CRITERIA:
- loopMintingAllowed must remain false

---

### TEST 102 — LOOP buy respects slippage cap
**Setup:** slippage exceeds loopBuyMaxSlippageBps  
**Expected:**
- distribution pauses or rejects

✅ PASS CRITERIA:
- does not buy under unsafe slippage

---

## J.4 Integration Tests (Required)

---

### INTEGRATION 001 — PCS ↔ BiSwap real quoting
**Expected:**
- Router returns valid candidate routes
- quotes match real pools

✅ PASS CRITERIA:
- deterministic quoting consistent across runs

---

### INTEGRATION 002 — End-to-end cycle on testnet
Flow:
- create vault
- deposit
- generate strategy
- approve
- execute
- settle
- withdraw profit
- closeout

✅ PASS CRITERIA:
- all events present
- ledger consistent

---

## J.5 Acceptance Criteria (Launch Gate)

YieldLoop v1.2 is not eligible for launch until ALL are true:

1) All unit tests pass  
2) Integration 001 + 002 pass  
3) No execution without:
   - approved strategy
   - valid oracle
   - trade-quality gate
4) Profit ledger cannot exist in non-USDT  
5) Performance fee never charged on loss  
6) Closeout cannot force liquidation outside guardrails  
7) Pause engine correctly blocks unsafe behavior  
8) Event log is complete:
   - TradeDetail emitted for every swap
   - LedgerSnapshot after every settlement  
9) LOOP is never minted in v1.2  
10) User acceptance agreement is enforced before deposit + approval  

# Appendix K — Deployment + Ops Runbook (Keepers, Monitoring, Incidents)

**Document:** YieldLoop — Automated Vault Trading Protocol + LOOP Rewards (BNB Chain)  
**Version:** v1.2 MVP  
**Appendix:** K  
**Status:** REQUIRED IMPLEMENTATION SPEC (Production Operations + Incident Handling)

---

## K.1 Purpose

This appendix defines the required operational procedures for YieldLoop v1.2 in production.

It specifies:
- deployment process (testnet → mainnet)
- keeper/bot operations
- monitoring + alerts
- incident response playbooks
- pause/resume rules
- audit log requirements

This prevents:
- “we didn’t know what to do”
- silent failures
- slow response to exploit conditions
- reputational death

---

## K.2 Deployment Stages (Hard Lock)

### Stage 0 — Local Fork Validation
Must pass:
- Appendix J Unit Tests
- simulated swap execution
- state machine transitions

### Stage 1 — BNB Testnet
Must validate:
- Vault creation & deposits
- Strategy proposal + approval
- ExecutionManager behavior
- Settlement correctness
- Profit withdrawal
- Closeout behavior

### Stage 2 — Mainnet Soft Launch (Restricted)
Must enforce:
- limited vault count
- limited total deposits
- admin-only allowlist for early users (recommended)

### Stage 3 — Mainnet Open Launch
Only after:
- 30-day incident-free operations (recommended)
- monitoring proven stable

---

## K.3 Keeper / Executor Operations

### K.3.1 Keeper Architecture (Required)

YieldLoop must run at least 2 independent keeper services:
1) Execution Keeper (trading)
2) Settlement Keeper (settlement + closeout finalization)
3) Rewards Keeper (monthly LOOP distribution)

Keepers must run redundant:
- Keeper A (primary)
- Keeper B (fallback)

### K.3.2 Keeper Rules

Keepers must:
- only call ExecuteVault if CanExecute() returns allowed
- stop execution during global pause
- stop execution during vault pause
- log every action

Keepers must not:
- bypass strategy approval
- attempt repeated execution spam
- attempt swaps under unsafe oracle status

---

## K.4 Monitoring Requirements (Non-Negotiable)

### K.4.1 Monitoring Targets

Must monitor:

#### Protocol Health
- protocol paused flag
- number of paused vaults
- total vault count
- total deposits TVL (USDT basis)
- total withdrawable profit balances

#### Execution Health
- trades/day
- failed tx count per hour
- average gas per action
- slippage exceed events
- price impact exceed events

#### Oracle Health
- oracle stale events
- oracle deviation events
- oracle unavailable events

#### DEX Health
- PCS quote failure rate
- BiSwap quote failure rate
- swap reverts count
- pool liquidity threshold violations

#### Settlement Health
- settlement failure count
- average settlement duration
- any ledger mismatch events

#### Rewards Health
- suspenseProfit totals
- successful LOOP distributions
- distribution failures
- slippage failures during LOOP buy

---

## K.5 Alerting Thresholds (Default)

### K.5.1 Critical Alerts (Immediate Pager)

Trigger CRITICAL alert if any:

- oracle invalid persists > 5 minutes
- DEX quote failure rate > 20% for 10 minutes
- more than 5 vaults enter PAUSED in 30 minutes
- any vault enters ERROR_LOCK
- settlement failures > 3 in 60 minutes
- unexpected token balance in FeeRouter / protocol wallets
- unexpected contract upgrade attempt (if upgradeable)

---

## K.6 Pause + Resume Policy (Hard Lock)

### K.6.1 Automatic Pause Triggers

Auto-pause must occur if:
- oracle invalid
- DEX outage
- repeated failures
- slippage abuse patterns

Pause types:
1) Per-vault pause (preferred)
2) Global pause (if systemic risk)

### K.6.2 Resume Conditions

Resume only allowed if:
- oracle valid sustained for 2 minutes
- DEX quotes stable
- keeper health stable
- system confirms safe execution parameters

Resume must emit:
- VaultResumed
- ProtocolResumed

---

## K.7 Incident Response Playbooks

---

## K.7.1 Incident Type A — Oracle Failure

**Symptoms**
- ORACLE_STALE / ORACLE_DEVIATION_HIGH events
- executions blocked
- closeouts delayed

**Immediate Actions**
1) Auto-pause execution
2) Notify operator
3) Validate oracle feed source
4) Confirm TWAP fallback status

**Recovery**
- restore oracle validity
- resume per-vault execution
- do not force catch-up trading

---

## K.7.2 Incident Type B — DEX Outage / Swap Failures

**Symptoms**
- swap reverts
- quote failures
- failures above threshold

**Immediate Actions**
1) Pause affected vaults
2) Disable affected DEX venue temporarily (admin)
3) Ensure users can still withdraw credited profits

**Recovery**
- re-enable venue after stable quotes

---

## K.7.3 Incident Type C — Abnormal Slippage / MEV Patterns

**Symptoms**
- repeated slippage exceed events
- abnormal execution losses

**Immediate Actions**
1) Pause affected vault(s)
2) Raise slippage buffers temporarily ONLY by owner approval
3) Investigate transaction ordering

**Recovery**
- tighten thresholds back down
- publish incident statement (recommended)

---

## K.7.4 Incident Type D — Suspected Exploit / Drain Attempt

**Symptoms**
- unexpected balance movement
- unexpected token approvals
- invariant mismatch

**Immediate Actions (Non-Negotiable)**
1) Trigger global kill switch
2) Freeze all execution
3) Freeze all closeouts if risk of forced liquidation
4) Collect logs
5) Notify auditor / security partner

**Recovery**
- only after confirmed containment

---

## K.7.5 Incident Type E — Settlement Bug / Accounting Mismatch

**Symptoms**
- ledger balances inconsistent
- profit credited incorrectly
- fee splits mismatch

**Immediate Actions**
1) Pause settlement engine
2) Pause execution engine
3) Snapshot affected vault states

**Recovery**
- patch + redeploy if necessary
- validate ledger consistency before resume

---

## K.8 Operational Reporting

### K.8.1 Daily Report (Operator)

Daily must include:
- total vaults
- total TVL (USDT basis)
- realized profit total
- fees collected total
- # of trades executed
- # of guardrail rejects
- # of paused vaults
- incidents summary

---

## K.9 Public Communication Policy (Simple)

If incident affects users:
- do not hide it
- post a short statement:
  - what happened
  - what actions taken (pause)
  - what user impact is
  - what’s being done next

---

## K.10 Production Readiness Gate

Protocol cannot be considered production-ready until:

- monitoring & alerting running 24/7
- keeper redundancy proven
- pause/resume tested on testnet
- incident playbook rehearsal completed

# Appendix L — Glossary + Definitions (Precision Terms for Legal + Dev)

**Document:** YieldLoop — Automated Vault Trading Protocol + LOOP Rewards (BNB Chain)  
**Version:** v1.2 MVP  
**Appendix:** L  
**Status:** REQUIRED IMPLEMENTATION SPEC (Definitions Are Binding)

---

## L.1 Purpose

This glossary defines the exact meaning of key YieldLoop terms used in the whitepaper and appendices.

These definitions are binding for:
- developers
- frontend copy
- audit documentation
- legal disclosures

No party may reinterpret these terms to broaden or narrow meaning.

---

## L.2 Core Protocol Terms

### YieldLoop
A self-custody DeFi protocol deployed on BNB Chain that creates isolated user vaults and executes automated trading strategies subject to strict guardrails and user-approved strategy proposals.

---

### Protocol
All YieldLoop smart contracts, configuration parameters, and system engines (Execution, Settlement, Oracle, Router, etc.) acting together to provide the YieldLoop service.

---

### Vault
An isolated smart contract instance created for a user.  
A vault holds deposited funds and can execute strategies only under rules defined in the v1.2 spec.

**Hard rule:** Vaults are not pooled. Every vault is isolated.

---

### Vault ID
A unique identifier assigned to each vault at creation.

---

### Vault Owner
The wallet address that created the vault and controls:
- deposit actions
- strategy approvals/rejections
- profit withdrawals
- closeout requests

---

### Base Asset
The asset selected at vault creation and deposit. Must be whitelisted.

---

### Settlement Asset
The asset used for accounting settlement and profit ledgers.

**Hard rule:** In v1.2 the settlement asset is always USDT.

---

### USDT (BEP-20)
Tether token on BNB Chain used as the settlement denomination in YieldLoop v1.2.

---

## L.3 Accounting & Value Terms

### Principal
The user’s deposited capital value basis stored and tracked in the ledger.

**Hard rule:** Principal can decrease due to losses.

---

### Principal Remaining (USDT basis)
The remaining principal balance tracked in USDT value terms, net of realized losses.

This is not a guarantee of future withdrawal value.

---

### NAV (Net Asset Value)
The total market value of the vault’s held assets + USDT balances, calculated for UI and internal guardrail checks.

**Hard rule:** NAV is not “profit” and cannot be withdrawn as profit unless realized.

---

### Unrealized P&L
Change in NAV due to market price movement while positions are open.

**Hard rule:** Unrealized P&L is informational only.

---

### Realized Profit
Profit confirmed only after:
- a trade position is closed
- settlement is completed
- net profit is computed in USDT terms
- fees are applied

Only realized profit can be credited as withdrawable profit or suspense profit.

---

### Profit
A user-facing term meaning realized profit only.

**Hard rule:** Profit in YieldLoop does not include unrealized gains.

---

### Withdrawable Profit (USDT)
Profit credited to the user ledger and held as USDT that the user may withdraw at any time (subject to state restrictions).

**Hard rule:** Withdrawable profit is always USDT.

---

### Suspense Profit (USDT)
Profit credited into a suspense ledger as USDT to support optional LOOP conversion at monthly distribution time.

Suspense profit is not LOOP itself; it is USDT waiting to be converted.

---

### Performance Fee
A protocol fee charged only on realized profit.

**Hard rule:** No fee is charged on losses.

---

### Fee Splits
The required routing of the performance fee amount into the protocol buckets:
- Dev/Ops
- Marketing
- Ratchet System
- Loop Labs

---

### Settlement
A deterministic accounting phase where:
- realized profit or loss is computed
- performance fee is computed (if profit)
- fees are routed
- profit ledgers are credited
- audit events are emitted

---

### Ledger
The vault’s accounting record of:
- principal remaining
- withdrawable profit
- suspense profit
- fees paid
- settlement history

---

### Ledger Snapshot
A mandatory post-settlement emitted record of the vault accounting state.

Used for audit and user transparency.

---

## L.4 Strategy / Execution Terms

### Strategy
A structured, rule-bound execution plan generated by the protocol and required to be approved by the user before trading.

---

### Strategy Proposal
A proposed strategy shown to the user prior to execution.

**Hard rule:** No execution may occur without user approval.

---

### Strategy Hash
A deterministic hash of the Strategy Object used to bind approval to execution.

---

### Strategy Expiry
A strict timestamp after which a strategy proposal is invalid and must be regenerated.

---

### Execution
The protocol performing trades, swaps, or routing actions under strict constraints.

---

### Opportunistic Execution
YieldLoop’s execution style where trading occurs only if safe, profitable conditions exist.

**Hard rule:** YieldLoop may remain idle for long periods.

---

### Trade Quality Gate
A decision rule requiring:
Expected Net Profit ≥ Minimum Required Net Profit  
after accounting for:
- fees
- gas
- slippage
- risk buffers

If it fails, no trade is executed.

---

### Guardrails
Hard safety constraints such as:
- max slippage
- max price impact
- min pool liquidity
- oracle sanity checks
- max hops
- trade throttles

Guardrails prevent unsafe execution even if theoretical profit exists.

---

### Spread Capture
A strategy type where the protocol attempts to profit from favorable price movements/spreads on allowed venues under strict gates.

---

### DEX Arbitrage
A strategy type where the protocol attempts to profit from PCS ↔ BiSwap price discrepancies under strict gates.

---

## L.5 Withdrawal Terms

### Profit Withdrawal
A user action withdrawing credited withdrawable profit (USDT).

**Hard rule:** Profit withdrawals do not withdraw principal.

---

### Principal Withdrawal
Withdrawal of user principal. Only possible after vault closeout and CLOSED state.

---

### Closeout
A user-requested process where the vault:
- unwinds positions
- converts exposure to USDT (except dust)
- settles final accounting
- transitions to CLOSED

Closeout exists to allow safe principal withdrawal.

---

### Closeout Pending
A vault state where unwinding is in progress and may be delayed if conditions are unsafe.

---

### Forced Liquidation
Selling assets outside guardrails to satisfy immediate withdrawal.

**Hard rule:** Forced liquidation is not permitted in v1.2.

---

## L.6 Safety / Risk Terms

### Pause
A safety state where execution is halted due to:
- oracle invalid
- DEX outage
- repeated failures
- admin kill switch

---

### Global Pause
Protocol-wide pause affecting all vaults.

---

### Per-Vault Pause
Pause applied only to a single vault.

---

### Kill Switch
Emergency admin ability to pause execution globally.

---

### Oracle
Reference price system used to validate execution sanity and detect manipulation.

---

### Oracle Stale
Oracle data older than the allowed max age.

---

### Oracle Deviation
Oracle price diverges from expected reference beyond allowed deviation threshold.

---

### MEV (Miner/Maximal Extractable Value)
Risk of execution being manipulated by transaction ordering and sandwich attacks.

In v1.2, MEV is addressed through guardrail buffers and execution discipline.

---

## L.7 LOOP Terms

### LOOP
YieldLoop optional rewards token.

In v1.2:
- LOOP is not minted from “guaranteed profit”
- LOOP distribution is sourced from market purchases using suspense USDT

---

### LOOP Rewards Option
A user selection where profit is routed into suspense USDT to be converted into LOOP at the monthly distribution schedule.

---

### Monthly Distribution
The scheduled monthly conversion of suspense USDT into LOOP by market purchases and distribution to eligible wallets.

---

### Claim Window
The time during which users can claim LOOP rewards if claim-based.

Default v1.2 claim window: 365 days.

---

## L.8 Compliance Terms

### Blacklist
A mechanism to prevent specified wallet addresses from using YieldLoop.

---

### Sanctions Compliance
Blocking or restricting access for sanctioned addresses/jurisdictions if enabled.

---

## L.9 Frontend / UX Terms

### Vault Status
A user-facing representation of vault state machine status, including:
- FUNDED_IDLE
- STRATEGY_PROPOSED
- STRATEGY_APPROVED
- EXECUTING
- HOLDING_POSITION
- SETTLEMENT_PENDING
- PROFIT_CREDITED
- CLOSE_PENDING
- CLOSED
- PAUSED

---

### Audit Trail
The event emissions + ledger snapshots enabling reconstruction of protocol behavior.

---

## L.10 Final Interpretation Clause

If any part of YieldLoop documentation is ambiguous, the controlling interpretation is:

1) Safety over profit
2) Guardrails over execution frequency
3) Realized profit only
4) USDT-only profit ledger
5) Principal withdraw only by closeout

# Appendix M — One-Page Developer Summary (v1.2 Build Snapshot)

**Document:** YieldLoop — Automated Vault Trading Protocol + LOOP Rewards (BNB Chain)  
**Version:** v1.2 MVP  
**Appendix:** M  
**Status:** REQUIRED IMPLEMENTATION SUMMARY (Pin This to Dev Board)

---

## M.1 What YieldLoop v1.2 Is (In One Sentence)

YieldLoop v1.2 is a **non-custodial automated vault trading protocol** on BNB Chain that executes **guardrail-limited opportunistic PCS ↔ BiSwap trading**, credits **realized profit only**, allows **profit withdrawal anytime (USDT)**, and allows **principal withdrawal only via closeout settlement**.

---

## M.2 Core Hard Rules (Non-Negotiable)

1) **Self custody only** (user wallet owns access)
2) Vaults are **isolated per user** (no pooled funds)
3) Supported venues: **PCS + BiSwap only**
4) Supported vault assets whitelist only:
   - BTCB, ETH, SOL, XRP, BNB, USDT
5) Strategy requires **proposal + explicit user approval**
6) Execution must be **strategy-hash bound**
7) Execution is **opportunistic**
   - may trade often or remain idle
8) Profit is credited only when **realized + settled**
9) Withdrawable profit is **USDT only**
10) Performance fee:
   - **20% on realized profit only**
   - **no fee on loss**
11) Closeout:
   - required for principal withdrawal
   - may delay to avoid unsafe liquidation
   - max closeout duration: 24 hours
12) LOOP:
   - optional profit routing
   - LOOP is **not minted**
   - LOOP rewards funded by **market buy using suspense USDT**
   - monthly distribution day 1 @ 00:00 UTC

---

## M.3 Required Contract Inventory (v1.2 MVP)

- YieldLoopCore (parameters + pause)
- VaultFactory
- YieldLoopVault (per vault)
- StrategyManager (proposal generator)
- Router (PCS/Biswap route candidates only)
- OracleManager (sanity checks + TWAP fallback)
- ExecutionManager (keeper executor)
- SettlementManager (accounting math)
- FeeRouter (fee split transfers)
- LoopRewardsManager (monthly conversion + distribution)
- BlacklistManager (optional, recommended)

---

## M.4 Required Engine System

1) Vault Engine
2) Strategy Engine
3) Execution Engine
4) Router Engine
5) Oracle Engine
6) Settlement Engine
7) Fee Routing Engine
8) LOOP Rewards Engine
9) Pause/Kill Switch Engine
10) Telemetry/Event Engine

---

## M.5 Default Core Parameters (Must Match)

- quoteScanIntervalSeconds: 60
- minSecondsBetweenTrades: 900
- maxTradesPerDayPerVault: 12
- maxFailedTxPerHourPerVault: 3
- minNetProfitUSDT: 3.00
- gasBufferUSDT: 0.50
- maxSlippageBpsPerSwap: 50
- maxPriceImpactBps: 50
- minPoolLiquidityUSDT: 250000
- maxRouteHops: 2
- configCooldownSeconds: 86400
- dustThresholdUSDT: 0.50

---

## M.6 Vault State Machine (Only These)

0 VAULT_CREATED  
1 DEPOSIT_PENDING  
2 FUNDED_IDLE  
3 STRATEGY_PROPOSED  
4 STRATEGY_APPROVED  
5 EXECUTING  
6 HOLDING_POSITION  
7 SETTLEMENT_PENDING  
8 PROFIT_CREDITED  
9 WITHDRAW_PROFIT_PENDING  
10 CLOSE_REQUESTED  
11 CLOSE_PENDING  
12 CLOSED  
13 PAUSED  
14 ERROR_LOCK  

---

## M.7 Keeper / Bot Rules

- Execution keeper must call:
  - CanExecute(vaultId, strategyHash) before ExecuteVault
- Must stop on:
  - global pause
  - vault pause
  - oracle invalid
- Must emit:
  - TradeDetail for every swap
  - LedgerSnapshot after every settlement

---

## M.8 Frontend Must Support (Minimum)

User must be able to:

1) Connect wallet
2) Create vault
3) Deposit
4) View strategy proposal
5) Approve/reject strategy
6) Watch vault status + guardrails status
7) Withdraw profit anytime (USDT only)
8) Request closeout for principal
9) View full activity log
10) View LOOP suspense + distribution schedule

---

## M.9 Test & Launch Gate

Launch is blocked until Appendix J passes:
- unit tests
- integration tests
- end-to-end vault cycle
- pause engine
- slippage/oracle failure tests
- LOOP distribution rules

---

## M.10 “If Ambiguous, Interpret Like This”

Order of priority:
1) Safety > profit
2) Guardrails > frequency
3) Realized profit only
4) USDT-only profit ledger
5) Closeout required for principal

# Appendix N — Token + Reward Disclosures (LOOP Language / Positioning / Compliance Safety)

**Document:** YieldLoop — Automated Vault Trading Protocol + LOOP Rewards (BNB Chain)  
**Version:** v1.2 MVP  
**Appendix:** N  
**Status:** REQUIRED IMPLEMENTATION SPEC (Frontend + Docs Must Use This Language)

---

## N.1 Purpose

This appendix defines the exact disclosure language and positioning requirements for LOOP rewards in YieldLoop v1.2.

Goal:
- prevent misleading marketing
- prevent profit/price promises
- reduce Howey-style risk framing
- keep user messaging consistent and defensible
- make LOOP clearly optional and non-guaranteed

This language must be used in:
- frontend UI text
- FAQ
- whitepaper sections
- onboarding flow
- reward selection flow

---

## N.2 Required Positioning (Hard Rules)

### N.2.1 LOOP Is Not an Investment Promise

YieldLoop must never imply:
- LOOP is an investment
- LOOP will rise in value
- LOOP has “guaranteed floor price”
- LOOP creates profit
- LOOP is “backed returns”
- LOOP is “interest”
- LOOP is “APY”

### N.2.2 LOOP Is Optional

YieldLoop must always state:
- LOOP rewards are optional
- users can receive profits in USDT instead
- selecting LOOP introduces extra volatility risk

### N.2.3 LOOP Is Not Minted as Guaranteed Profit (v1.2)

Hard rule:
- LOOP rewards in v1.2 are sourced only by **market purchases using suspense profit USDT**
- LOOP is not minted as a promise of value
- LOOP distributions may be delayed or skipped if unsafe conditions occur

---

## N.3 Required “LOOP Rewards” Definition (Use This Everywhere)

### LOOP Rewards (Definition)

LOOP rewards are an **optional reward format** for realized profit.

If selected:
- realized profit is held temporarily as **Suspense Profit (USDT)**
- on a monthly schedule, the protocol uses that USDT to **buy LOOP on the open market**
- LOOP is then distributed to the user

LOOP may increase or decrease in value after distribution.

---

## N.4 Required UI Copy (Exact Wording Blocks)

### N.4.1 Profit Output Selection UI (Mandatory)

Title:
**Choose Profit Output**

Body text (must display):
You can receive realized profit in:
- **USDT** (stable, withdraw anytime)
- **LOOP** (optional rewards, volatile)

If you select LOOP:
- your profit will be held temporarily as USDT (“Suspense Profit”)
- monthly, that USDT is used to buy LOOP on-market
- LOOP can gain or lose value after distribution

Buttons:
- Select USDT Profit
- Select LOOP Rewards

---

### N.4.2 LOOP Warning Banner (Mandatory if LOOP selected)

Banner text:
**LOOP is volatile. Selecting LOOP exposes your profit to token price risk.**

Subtext:
YieldLoop does not guarantee LOOP price performance.

---

### N.4.3 Rewards Page Text (Mandatory)

Title:
**LOOP Rewards**

Body text (must display):
LOOP rewards are optional and not guaranteed.

- Your realized profit is first credited as USDT into a suspense ledger.
- On the monthly distribution schedule, YieldLoop buys LOOP on-market using that suspense USDT.
- LOOP is then distributed to your wallet.
- LOOP value may change after distribution.

YieldLoop does not promise LOOP price increases.

---

### N.4.4 Strategy Approval Page (Short Token Disclosure)

Checkbox:
[ ] If I select LOOP rewards, I understand my profit may be converted into LOOP, and LOOP may lose value.

---

## N.5 Forbidden Phrases (Must Never Be Used)

Frontend and docs must not use these phrases:

- “Guaranteed”
- “Risk-free”
- “Passive income”
- “Interest”
- “Yield”
- “APY”
- “Floor price guaranteed”
- “Token price grows”
- “Backed returns”
- “Safe profit”
- “Bank-like”
- “Savings account”
- “Compound interest”
- “Guaranteed redemption”
- “Stable growth token”

---

## N.6 Required Risk Disclosures (Token-Specific)

Whenever LOOP is shown, at least one of the following statements must appear:

1) **LOOP is volatile and may lose value.**
2) **YieldLoop does not guarantee LOOP performance.**
3) **Selecting LOOP changes the risk profile of profit distribution.**
4) **LOOP rewards may be delayed if execution is paused or unsafe.**

---

## N.7 Required FAQ Entries (Token Section)

### FAQ: Is LOOP guaranteed to go up?
Answer (required):
No. LOOP can increase or decrease in value. YieldLoop does not promise price appreciation.

---

### FAQ: Is LOOP minted from profits?
Answer (required):
In v1.2, no. YieldLoop uses suspense profit USDT to buy LOOP on the market and distribute it.

---

### FAQ: Can I take USDT instead of LOOP?
Answer (required):
Yes. You can choose USDT profit at any time. LOOP rewards are optional.

---

### FAQ: Is LOOP “interest” or “yield”?
Answer (required):
No. YieldLoop does not pay interest. Any profit depends on realized trading outcomes.

---

## N.8 Classification Guardrails (Policy for Dev + Founder)

### N.8.1 LOOP Must Not Be Positioned As Security-Like

Do not present LOOP as:
- equity
- dividends
- ownership
- profit share rights
- governance (unless explicitly specified elsewhere)

### N.8.2 No Marketing Claims

LOOP marketing must not include:
- profit charts implying guaranteed trend
- language suggesting inevitability
- “strong floor” statements
- “sustainable pump” nonsense
- token performance comparisons that imply prediction

---

## N.9 Monthly Distribution Operational Disclosure

YieldLoop must display:
- distribution schedule uses UTC
- distribution may be skipped if:
  - DEX quotes unsafe
  - slippage too high
  - oracle invalid
  - protocol paused

Required line:
**Safety overrides distribution timing.**

---

## N.10 Required Legal Framing (Short Form)

Use this short form on LOOP UI sections:

**LOOP rewards are optional and speculative. YieldLoop does not guarantee token value or profit.**

---

## N.11 Non-Negotiable Implementation Clause

Developer must:
- implement LOOP selection as an explicit user choice
- show token risk warnings before selection
- store user selection preference per vault
- never default users into LOOP without explicit selection

