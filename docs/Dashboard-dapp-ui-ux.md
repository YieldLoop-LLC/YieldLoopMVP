# YieldLoop v1.2 MVP — Dashboard + DApp UI/UX Build Spec (GitHub Copy)

Version: 1.2 (MVP)
Scope: Web DApp (desktop + mobile responsive)

---

## 1. Product Goal

Build a simple, safe interface to interact with YieldLoop smart contracts:
- Hide complexity while preserving transparency
- Prevent user actions that violate protocol rules
- Reflect the vault state machine accurately
- Make actions deterministic and auditable

---

## 2. Supported Platform + Wallets

### 2.1 Platform
- Web only (desktop + mobile responsive)

### 2.2 Supported Wallets
- MetaMask
- WalletConnect
- Coinbase Wallet

### 2.3 Network Rules
- If wallet not connected: block all contract actions and prompt connect
- If wrong chain: prompt network switch to BNB Chain
- If user refuses switch: block interaction

---

## 3. Global Layout + Navigation

### 3.1 Persistent Layout
Top nav:
- Logo
- Dashboard
- Create Vault
- LOOP Rewards
- Governance
- Connect Wallet

Footer:
- Docs
- Disclaimer
- Terms

### 3.2 Global UX Rules (Hard Lock)
- Professional financial app feel: clean, simple, not gamified
- No hype, no APY promises, no “guaranteed profit” language
- Users must always know:
  - principal basis
  - withdrawable profit
  - strategy status
  - whether funds are currently exposed
- Any strategy execution requires explicit user approval
- UI must reflect vault state machine exactly
- UI must not allow illegal actions
- UI must not fabricate data
- Protocol logic must not be modified

### 3.3 Global Status Always Visible
Every vault-related screen must show:
- vault state (from state machine)
- last activity timestamp
- current NAV (if available)
- withdrawable profit amount

---

## 4. Required Page List (v1.2)

1) Landing Page / Home  
2) Wallet Connect Modal (or page)  
3) Dashboard (Vault List)  
4) Create Vault  
5) Strategy Proposal  
6) Vault Details  
7) Withdraw Profit (page or modal)  
8) Close Vault (principal withdrawal) (page or modal)  
9) LOOP Rewards  
10) Governance Status  
11) Admin Console (restricted)  
12) Legal / Risk Disclosure  
13) Pause / Incident Banner UI (global overlay)

---

## 5. Landing Page / Home

### Purpose
Explain YieldLoop in plain language and route users to connect wallet.

### Required Sections
- Hero summary:
  - “Automated Vault Trading Protocol (BNB Chain)”
  - “Self custody”
  - “Profit-based fees only”
- How it works (3–5 bullets)
- Core constraints:
  - Supported tokens list
  - Supported DEX venues list
- Primary CTA: Connect Wallet

### Required Buttons
- Connect Wallet
- View Docs
- Risk Disclosure

---

## 6. Connect Wallet Page / Modal

### Required Elements
- Wallet list (MetaMask / WalletConnect / Coinbase Wallet)
- Connected wallet address (when connected)
- Chain status indicator (BNB only)
- Wrong chain warning + switch network instruction

### Required Buttons
- Connect
- Disconnect
- Switch to BNB
- Continue

### Blocking Behavior
If not connected or wrong chain: user cannot proceed into protected pages/actions.

---

## 7. Dashboard (Vault List)

### Purpose
Display all user vaults and expose primary actions.

### Vault Card / Row Fields (Required)
- Vault ID
- Status
- NAV (USDT)
- Principal Remaining (USDT)
- Withdrawable Profit (USDT)
- Payout Mode (USDT / LOOP)
- Button: View Vault

### Dashboard-Level Controls (Required)
- Create New Vault button

### Recommended Additions (Still MVP-safe)
- Totals summary strip:
  - Total NAV (USDT)
  - Total Principal Remaining (USDT)
  - Total Withdrawable Profit (USDT)
  - Total Suspense Profit (USDT) (if LOOP enabled)

---

## 8. Create Vault Page

### Purpose
Create a new isolated vault and guide deposit.

### Step Flow (Required)
Step 1: Enter deposit amount (USDT)  
Step 2: Select allocation weights (must sum to 100%)  
Step 3: Select compounding option  
Step 4: Select profit payout method  
Step 5: Accept disclaimer  
Step 6: Confirm transaction

### Required Inputs
- Deposit amount (USDT)
- Allocation weights selector (whitelisted assets only)
- Compounding option selector
- Profit payout method selector
- Disclaimer acceptance checkbox

### Validation (Hard Lock)
- Minimum deposit: $250
- Only whitelisted assets allowed
- Allocation weights must equal 100%

### Transaction Sequence
1) Approve token (if required)
2) Deposit / Create Vault transaction
3) Route user to Strategy Proposal page for that vault (or vault details with “strategy pending” state)

---

## 9. Strategy Proposal Page

### Purpose
Show generated strategy before execution and require explicit approval.

### Display (Required)
- Strategy Type
- Target Assets
- Guardrail Summary
- Expiration Timer

### Buttons (Required)
- Approve Strategy
- Reject Strategy

### Expiration Behavior
- If expired: show “expired” state and require regenerate (protocol-defined mechanism)
- No execution allowed without approved strategy

---

## 10. Vault Details Page (Single Vault Control Center)

### Layout Sections (Required)

#### 10.1 Overview
Must display:
- Status
- NAV
- Principal Remaining
- Realized Profit (lifetime)
- Withdrawable Profit
- Suspense Profit
- Fees Paid

#### 10.2 Configuration
Must display:
- Allocations
- Compounding Option
- Payout Method

#### 10.3 Actions
- Withdraw Profit
- Request Close Vault

#### 10.4 History
- Trade list
- Settlement events
- Strategy approvals

### Strategy Panel (Required)
- Strategy status: none / proposed / approved / expired
- Proposed strategy hash
- Approved strategy hash
- Strategy expiry timestamp

### Status + Safety Panel (Required)
- Oracle status (valid / stale)
- Venue availability (PCS / BiSwap)
- Pause status (global + vault-level)
- Last execution attempt timestamp

### Required Buttons
- View Strategy Proposal
- Approve Strategy
- Reject Strategy
- Withdraw Profit
- Request Closeout
- Refresh Vault Data

---

## 11. Withdraw Profit (Modal or Page)

### Required Inputs
- Amount input
  - max = withdrawableProfitUSDT

### Required Button
- Confirm

### UX Rules
- Show estimated network fee
- Show expected receive amount
- Show pending state, success state, revert state

---

## 12. Close Vault (Principal Withdrawal) (Modal or Page)

### Required Elements
- Warning message that principal withdrawal requires closeout settlement (per protocol rules)
- Confirm button

### Required Button
- Confirm Closeout Request

### UX Rules
- Show current vault state and whether close is allowed
- If close not allowed: show reason and blocked state

---

## 13. LOOP Rewards Page

### Purpose
Expose LOOP-related balances and claiming.

### Required Display
- Suspense Profit Balance
- LOOP Balance
- Claimable LOOP
- Claim History

### Required Button
- Claim LOOP

### UX Rules
- If claiming blocked due to settlement window or state: disable and explain

---

## 14. Governance Status Page (Read-only)

### Required Display
- Timelock queue
- Pending actions
- Executed actions
- Emergency status

---

## 15. Admin Console (Restricted)

### Access Control
- Restricted to admin wallet list

### Required Controls
- Pause system
- Resume system
- Queue timelock action

### UX Rules
- Must clearly show:
  - current pause state
  - last pause/resume event
  - queued actions and activation timestamps

---

## 16. Global Pause / Incident Banner UI (Overlay)

### Purpose
Make emergency status unmissable.

### Behavior
- If global pause active: show persistent top banner on all pages
- If venue pause active: show banner + venue impacted
- If vault paused: show banner within vault screens

### Banner Content
- Pause type (global / venue / vault)
- Reason code (if available)
- Timestamp
- Link to “Governance Status” page

---

## 17. UI Data Models (Frontend)

### 17.1 VaultUI
- vaultId
- owner
- status
- navUSDT
- principalRemainingUSDT
- withdrawableProfitUSDT
- suspenseProfitUSDT
- payoutMode
- compoundingMode
- allocations
- strategyHashActive

### 17.2 StrategyUI
- strategyHash
- strategyType
- createdAt
- validUntil
- guardrailsSummary

---

## 18. Transaction UX Rules (Global)

For every contract interaction:
- Show pending state
- Show success confirmation
- Show revert message (mapped to user-safe text)

---

## 19. Error Message Mapping (User-facing)

- ORACLE_STALE → Pricing feed unavailable. Trading paused.
- DEX_OUTAGE → Trading venue unavailable.
- TX_FAILURE_LIMIT → Vault paused due to repeated failures.
- SLIPPAGE_EXCEEDED → Market conditions unsafe.

---

## 20. Backend Architecture (Application Layer)

### Components
- Frontend: Next.js
- Backend API: Node.js
- Indexer: Contract event listener
- Database: PostgreSQL

### Backend Responsibilities
- Cache vault snapshots
- Cache vault history
- Serve dashboard queries

### Hard Rule
- No backend may move user funds.

---

## 21. Environments

- Local
- BSC Testnet
- BSC Mainnet

### Environment Variables
- RPC_URL
- INDEXER_DB_URL
- BACKEND_API_URL

---

## 22. Design System

Theme: Dark  
Primary: Neon Green  
Secondary: Cyan  
Background: Black  
Font: Inter

---

## 23. Legal Requirements

- User must accept disclaimer before vault activation

---

## 24. MVP Acceptance Criteria

MVP is complete if a user can:
- Connect wallet
- Create vault
- Approve strategy
- See status
- Withdraw profit
- Close vault
