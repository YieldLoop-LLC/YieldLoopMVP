# YieldLoop — User Experience & Dashboard Guide  
**How Users Interact With the System (and Why It Looks the Way It Does)**

**Author:** Todd Koletsky  
**Date:** February 9, 2026  
**Version:** v1.0 — UX & Product Specification

---

## Document Purpose

This document explains **how YieldLoop feels to use**.

It is not a marketing document.  
It is not a technical architecture document.

It exists to:
- Define the user journey
- Explain dashboard design decisions
- Set expectations around visibility and control
- Clarify what users see, what they don’t, and why

If users are confused, this document is the answer.

---

## 1. UX Philosophy

### 1.1 Boring Is a Feature

YieldLoop intentionally avoids:
- Flashing numbers
- Live PnL dopamine loops
- APR projections
- “Performance theater”

The system is designed to feel:
- Calm
- Predictable
- Account-like
- Serious

YieldLoop should feel closer to a brokerage account than a trading terminal.

---

### 1.2 Explicit > Implicit

The UX favors:
- Explicit actions
- Clear confirmations
- Deliberate pacing

Nothing happens automatically without:
- User awareness
- User consent
- Clear acknowledgment

---

## 2. Entry Experience (First-Time User)

### 2.1 Wallet Connection

On first entry:
- User connects a self-custody wallet
- No deposit is required to browse
- Read-only mode is available

The system immediately communicates:
- Minimum deposit rules
- Risk framing
- No guarantees
- No APY promises

---

### 2.2 Initial Deposit Rules (Reinforced)

Before first deposit, the user is clearly informed:
- **$1,000 minimum initial deposit per wallet**
- **$100 minimum for subsequent deposits**
- Deposits are not pooled
- Funds remain user-owned at all times

No fine print.

---

## 3. Dashboard Overview (Primary Screen)

The main dashboard shows:

- Total deposited balance
- Vault status (Idle / Active / Settling / Withdrawable)
- Current cycle phase
- Last completed cycle summary
- Pending actions (if any)

It does **not** show:
- Live PnL
- Projected returns
- APR
- Intraday performance graphs

This is intentional.

---

## 4. Vault View (Per-Vault Detail)

Each vault has its own dedicated view.

### 4.1 Visible Information

Users can see:
- Vault balance
- Asset composition
- Strategy status
- Execution activity (summarized)
- Current lifecycle state
- Risk tier classification

---

### 4.2 Hidden or Abstracted Information

Users do **not** see:
- Raw trade-by-trade execution logs (by default)
- Algorithmic parameters
- Micro-optimizations
- Internal guardrail thresholds

This prevents:
- Misinterpretation
- Overreaction
- Incentive to micromanage

Advanced users may opt into deeper views.

---

## 5. Strategy Proposal UX

### 5.1 Proposal Presentation

Before a cycle begins:
- The system presents a **strategy proposal**
- The proposal explains:
  - Strategy category
  - General behavior
  - Risk posture
  - Lock conditions
  - Exit conditions

It explicitly does **not** promise outcomes.

---

### 5.2 User Approval

Users must:
- Review the proposal
- Acknowledge risk
- Approve execution

No approval → no execution.

This approval is logged.

---

## 6. Cycle Awareness

### 6.1 Cycle Phases (User-Facing)

Users see cycles as:
- Upcoming
- Active
- Settling
- Completed

Exact execution timing is abstracted.

---

### 6.2 Why Execution Is Not Real-Time Visible

YieldLoop hides real-time execution because:
- It encourages bad behavior
- It invites emotional reactions
- It distorts perception of risk

The system is designed for **outcomes**, not entertainment.

---

## 7. Settlement Experience

At cycle completion:
- Results are finalized
- Gains or losses are displayed
- Fees are shown transparently
- Incentives (if any) are applied

Settlement is:
- Discrete
- Clear
- Final

No retroactive changes occur.

---

## 8. Withdrawals & Continuation

After settlement, users may:
- Withdraw all
- Withdraw partially
- Leave funds for next cycle (if enabled)

Withdrawals:
- Are explicit
- Do not penalize users
- Do not affect future eligibility

---

## 9. Emergency Controls (User View)

### 9.1 Emergency Withdrawal Button

If available, users may see:
- “Emergency Exit” option

Clear warnings explain:
- Possible losses
- Orderly unwind attempts
- FIFO processing (if system-wide)

Emergency actions are never hidden.

---

## 10. Genesis & Early Access UX

### 10.1 Genesis Recognition

If a user holds:
- Basic Genesis NFT
- Lifetime Genesis NFT

The dashboard reflects:
- Fee discount eligibility
- Incentive eligibility
- Access channel status

No “prestige UI.”  
Just clarity.

---

### 10.2 Discord Access Integration

Genesis holders see:
- Clear instructions for private access channels
- Tier-appropriate visibility
- Direct communication expectations

Access ≠ control.

---

## 11. Notifications & Messaging

YieldLoop communicates via:
- On-dashboard alerts
- Email (optional)
- Wallet notifications (where supported)

Messages focus on:
- Required actions
- State changes
- Settlement completion

No hype notifications.

---

## 12. UX Non-Goals (Explicit)

The UX will not:
- Gamify performance
- Encourage frequent interaction
- Push deposits aggressively
- Obscure risk
- Reward impatience

YieldLoop is not designed to be “sticky.”  
It is designed to be **reliable**.

---

## 13. Summary

YieldLoop’s UX is intentionally restrained.

It:
- Reduces cognitive load
- Discourages emotional decisions
- Reinforces discipline
- Reflects system maturity

If the UI ever feels boring, it is working as designed.

Proceed next to:

**Document 5 — Fees, LOOP, Redemption & Incentives**
