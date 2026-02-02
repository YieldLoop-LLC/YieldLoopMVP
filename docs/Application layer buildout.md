# YieldLoop Product Specification (Application-Layer Build Contract)
**Version:** 1.0  
**Author:** Todd Koletsky  
**Applies To:** YieldLoop v1.2 MVP  
**Purpose:**  
This document defines the complete application-layer specification required to transform the YieldLoop protocol documents into a functioning user-facing product using an AI builder such as Lovable.  

This document intentionally focuses on UI, UX, backend architecture, data models, integrations, and deployment.  
Protocol logic is defined in existing YieldLoop documents and must not be altered.

---

## 1. Product Goals

- Provide a simple, safe interface to interact with YieldLoop smart contracts.
- Hide complexity while preserving transparency.
- Prevent user actions that violate protocol rules.
- Reflect vault state machine accurately.
- Make all user actions deterministic and auditable.

---

## 2. Supported Platforms

- Web (desktop and mobile responsive)
- No native mobile apps in v1

---

## 3. Supported Wallets

- MetaMask  
- WalletConnect  
- Coinbase Wallet  

Behavior:
- If wallet is not connected → prompt connect  
- If wrong chain → prompt automatic network switch to BNB Chain  
- If user refuses → block interaction  

---

## 4. Application Pages (Screen Map)

1. Landing  
2. Connect Wallet  
3. Dashboard (Vault List)  
4. Create Vault  
5. Strategy Proposal  
6. Vault Details  
7. Withdraw Profit  
8. Close Vault  
9. LOOP Rewards  
10. Governance Status  
11. Admin Console (restricted)  
12. Legal / Disclaimer  

---

## 5. Global Layout

- Top navigation bar  
  - Logo  
  - Dashboard  
  - Create Vault  
  - LOOP Rewards  
  - Governance  
  - Connect Wallet  

- Footer  
  - Docs  
  - Disclaimer  
  - Terms  

---

## 6. Landing Page

Purpose: Explain what YieldLoop is and route users to connect wallet.

Elements:
- Headline
- Short description
- Key facts
- Connect Wallet button

---

## 7. Connect Wallet Page

- Wallet selection buttons  
- Network status indicator  
- Continue button  

---

## 8. Dashboard (Vault List)

Displays all user vaults.

Each vault card shows:
- Vault ID  
- Status  
- NAV (USDT)  
- Principal Remaining  
- Withdrawable Profit  
- Payout Mode (USDT / LOOP)  
- Button: View Vault  

Also display:
- Button: Create New Vault  

---

## 9. Create Vault Page

Step 1: Enter Deposit Amount (USDT)  
Step 2: Select Allocation Weights (must sum to 100%)  
Step 3: Select Compounding Option  
Step 4: Select Profit Payout Method  
Step 5: Accept Disclaimer  
Step 6: Confirm Transaction  

Validation:
- Minimum $250  
- Only whitelisted assets  
- Weights must equal 100  

---

## 10. Strategy Proposal Page

Shows generated strategy before execution.

Display:
- Strategy Type  
- Target Assets  
- Guardrail Summary  
- Expiration Timer  

Buttons:
- Approve Strategy  
- Reject Strategy  

If expired → regenerate

---

## 11. Vault Details Page

Sections:

Overview
- Status  
- NAV  
- Principal Remaining  
- Realized Profit (lifetime)  
- Withdrawable Profit  
- Suspense Profit  
- Fees Paid  

Configuration
- Allocations  
- Compounding Option  
- Payout Method  

Actions
- Withdraw Profit  
- Request Close Vault  

History
- Trade list  
- Settlement events  
- Strategy approvals  

---

## 12. Withdraw Profit Modal

- Input amount (max = withdrawableProfitUSDT)  
- Confirm button  

---

## 13. Close Vault Modal

- Warning message  
- Confirm button  

---

## 14. LOOP Rewards Page

Displays:
- Suspense Profit Balance  
- LOOP Balance  
- Claimable LOOP  
- Claim History  

Button:
- Claim LOOP  

---

## 15. Governance Status Page

Read-only:
- Timelock queue  
- Pending actions  
- Executed actions  
- Emergency status  

---

## 16. Admin Console (Restricted)

- Pause system  
- Resume system  
- Queue timelock action  

Access controlled by admin wallet list.

---

## 17. Vault UI Data Model

VaultUI:
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

---

## 18. Strategy UI Data Model

StrategyUI:
- strategyHash  
- strategyType  
- createdAt  
- validUntil  
- guardrailsSummary  

---

## 19. Transaction UX Rules

- Show pending state  
- Show success confirmation  
- Show revert message  

---

## 20. Error Message Mapping

ORACLE_STALE → Pricing feed unavailable. Trading paused.  
DEX_OUTAGE → Trading venue unavailable.  
TX_FAILURE_LIMIT → Vault paused due to repeated failures.  
SLIPPAGE_EXCEEDED → Market conditions unsafe.  

---

## 21. Backend Architecture

- Frontend: Next.js  
- Backend API: Node.js  
- Indexer: Event listener for contracts  
- Database: PostgreSQL  

Backend Responsibilities:
- Cache vault snapshots  
- Cache history  
- Serve dashboard queries  

No backend may move user funds.

---

## 22. Environment Types

- Local  
- BSC Testnet  
- BSC Mainnet  

---

## 23. Environment Variables

- RPC_URL  
- INDEXER_DB_URL  
- BACKEND_API_URL  

---

## 24. Design System

Theme: Dark  
Primary Color: Neon Green  
Secondary Color: Cyan  
Background: Black  
Font: Inter  

---

## 25. Copy Dictionary

Buttons:
- Connect Wallet  
- Create Vault  
- Approve Strategy  
- Reject Strategy  
- Withdraw Profit  
- Close Vault  
- Claim LOOP  

---

## 26. Legal Requirements

User must accept disclaimer before vault activation.

---

## 27. Non-Negotiable Rules

- UI must reflect vault state machine exactly  
- UI must not allow illegal actions  
- UI must not fabricate data  
- Protocol logic must not be modified  

---

## 28. Acceptance Criteria

If a user can:
- Connect wallet  
- Create vault  
- Approve strategy  
- See status  
- Withdraw profit  
- Close vault  
Then MVP is complete.

---

END OF DOCUMENT
