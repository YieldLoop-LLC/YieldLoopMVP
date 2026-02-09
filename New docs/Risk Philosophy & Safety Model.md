# YieldLoop — Risk Philosophy & Safety Model  
**Why the System Is Built to Survive, Not Impress**

**Author:** Todd Koletsky  
**Date:** February 9, 2026  
**Version:** v1.0 — Canonical Risk & Safety Specification

---

## Document Purpose & Authority

This document defines **how YieldLoop thinks about risk** and **how that philosophy is enforced in practice**.

It exists to:
- Make risk explicit, not implied
- Explain why certain things are intentionally not optimized
- Define safety invariants that cannot be violated
- Set expectations for loss, failure, and system behavior under stress

If there is tension between profit and safety, **safety wins**.

This document is normative.  
If a behavior contradicts this document, it is a bug.

---

## 1. Core Risk Philosophy

### 1.1 Survival First

YieldLoop is built on a single premise:

> The system must survive long enough to be useful.

This means YieldLoop explicitly accepts:
- Lower peak returns
- Missed opportunities
- Periods of inactivity

In exchange for:
- Durability
- Predictability
- Contained failure

---

### 1.2 Boring Is a Risk Control

“Boring” is not aesthetic.  
It is a **risk-management choice**.

YieldLoop avoids:
- Constant trading
- Leverage amplification
- Reflexive incentives
- APR signaling

Excitement is treated as a **risk factor**, not a feature.

---

## 2. Explicit Risk Acceptance

All users must understand and accept:

- Capital can be lost
- Strategies can fail
- Markets can break
- Liquidity can disappear
- Smart contracts can behave unexpectedly

YieldLoop does not pretend otherwise.

There is **no insurance** and **no guarantee**.

---

## 3. Risk Isolation (Primary Control)

### 3.1 Vault-Level Isolation

Each vault:
- Holds only one user’s funds
- Executes independently
- Settles independently
- Fails independently

Losses in one vault **cannot**:
- Drain another vault
- Trigger forced liquidations elsewhere
- Socialize losses across users

This is the most important safety feature in the system.

---

### 3.2 No Cross-Vault Dependencies

There is:
- No shared margin
- No pooled collateral
- No system-level leverage

The system may fail locally without failing globally.

---

## 4. Cycle-Based Risk Containment

### 4.1 Fixed Cycles

All execution occurs within **fixed cycles**.

Cycles:
- Have clear start and end boundaries
- Prevent perpetual exposure
- Create natural checkpoints
- Force periodic accounting

This limits:
- Runaway strategies
- Silent compounding
- Drift from user intent

---

### 4.2 Settlement as a Risk Reset

At settlement:
- Positions are closed
- Profit or loss is finalized
- Fees are assessed
- Incentives are applied

Nothing carries forward implicitly.

Every cycle is a reset.

---

## 5. Strategy-Level Risk Controls

### 5.1 Strategy Eligibility

A strategy is eligible only if it:
- Fits within defined risk classes
- Can be bounded by explicit guardrails
- Has a known failure mode
- Can be unwound

Strategies that cannot be exited are rejected.

---

### 5.2 No Leverage Assumption

YieldLoop assumes:
- Zero leverage by default
- No rehypothecation
- No recursive exposure

If leverage is ever introduced, it must be:
- Explicit
- Opt-in
- Hard-capped
- Separately documented

---

## 6. Guardrails (Non-Negotiable)

YieldLoop enforces multiple guardrail classes.

### 6.1 Capital Guardrails
- Max drawdown per vault
- Max exposure per asset
- Max position size relative to liquidity

---

### 6.2 Execution Guardrails
- Slippage caps
- Gas cost ceilings
- Time-based aborts
- Venue availability checks

---

### 6.3 Market Data Guardrails
- Price sanity checks
- Oracle deviation limits
- Volume and depth validation

If inputs are suspect, execution stops.

---

## 7. Incentives as Risk

### 7.1 Incentives Are Not Yield

Incentives are treated as **expenses**, not returns.

They:
- Do not scale with vault performance
- Do not compound
- Do not justify additional risk

YieldLoop does not “earn back” incentives by taking risk.

---

### 7.2 Separation of Incentives

Risk is not transferred between:
- Users
- Genesis holders
- Team and family wallets

Funding separation prevents:
- Incentive-driven risk escalation
- Hidden cross-subsidies
- Moral hazard

---

## 8. Emergency Philosophy

### 8.1 Exit Over Optimization

In an emergency:
1. Access matters
2. Order matters
3. Profit does not matter

YieldLoop prioritizes **exit paths**, not best prices.

---

### 8.2 Controlled Failure

The system is allowed to:
- Stop trading
- Freeze new deposits
- Enter emergency modes

It is not allowed to:
- Hide failures
- Block exits indefinitely
- Rewrite history

---

## 9. Liveness Guarantees

YieldLoop guarantees:
- Users can always request an exit
- Vaults cannot be trapped forever
- Governance can halt execution

Liveness is a safety requirement.

---

## 10. Human Risk (Explicitly Acknowledged)

YieldLoop assumes:
- Humans make mistakes
- Operators can fail
- Governance can be slow

The system is designed to:
- Minimize human discretion
- Require explicit actions
- Log and audit decisions

No single human can override safety unilaterally.

---

## 11. Non-Goals (Explicit)

YieldLoop will not:
- Maximize APY
- Chase trends
- Obscure risk
- Gamify behavior
- Optimize for social engagement

If those are priorities, this is not the right system.

---

## 12. Summary

YieldLoop is built to be:
- Predictable
- Contained
- Transparent
- Boring when necessary

Risk is not hidden.  
Risk is structured.

Losses may happen.  
Catastrophic failure is what the system exists to prevent.

---

**End of Risk Philosophy & Safety Model**

This completes the core documentation set.

The remaining document, if needed, is:

**Document 8 — Expansion & Addenda (Living Specification)**
