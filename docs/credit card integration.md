# YieldLoop — Card Payments (Global) + “Normie” Onboarding (No MetaMask)

## Goal
Enable users to fund a YieldLoop vault using **debit/credit card globally** (where permitted), while keeping the UX “walletless” (no MetaMask / Trust / seed phrases).

**Reality check:** card → crypto requires *some* wallet address. The trick is we create an **embedded wallet** for the user behind the scenes, then route the on-ramp payout to that address.

YieldLoop vaults are **owned/controlled by the originating wallet address**.  [oai_citation:0‡YieldLoop Consolidated.pdf](sediment://file_000000003e9871fd91fcf17af2bb14c1)  
Vault deposit requirements: **500 USDT (BEP-20) minimum to create a vault**; subsequent deposits **250 USDT (BEP-20)**.  [oai_citation:1‡Appendix.pdf](sediment://file_00000000ca8471f89f8c33e14fc1852b)

---

## Compliance requirements (must-have)
We will be global but must block sanctioned users.

### Sanctions / OFAC
We must block:
- OFAC SDN list entities and sanctioned jurisdictions
- Any other required sanctions lists depending on provider / legal counsel

**Implementation requirement:**
- We will rely on **on-ramp provider KYC/sanctions screening as primary**
- We also implement **our own “blocklist gate”** (defense-in-depth) to prevent access and to stop the on-ramp flow early.

Minimum enforcement:
1) Block access by **country** (GeoIP) for sanctioned jurisdictions.
2) Block known bad actors by **wallet address** (and optionally email/phone hash) if flagged.
3) If on-ramp returns “failed compliance,” the user cannot proceed.

> Note: Exact sanctions list integration method depends on legal counsel + vendor (Chainalysis/TRM/etc). Build the plumbing now.

---

## High-level user flow (normie UX)
1) User lands on YieldLoop and taps **“Start”**
2) User signs up with **email / phone OTP / Google / Apple**
3) App creates an **embedded self-custody wallet** for the user (no seed phrase shown)
4) User clicks **“Add funds”**
5) App opens on-ramp widget (Transak or MoonPay) prefilled for:
   - Network: **BNB Chain**
   - Asset: **USDT (BEP-20)**
   - Destination address: user embedded wallet address
   - Amount: at least **500 USDT** for new vault  [oai_citation:2‡Appendix.pdf](sediment://file_00000000ca8471f89f8c33e14fc1852b)
6) App detects funds arrival in embedded wallet
7) App guides user:
   - “Approve USDT” → “Create Vault / Deposit”
8) Vault is created and controlled by that same wallet address  [oai_citation:3‡YieldLoop Consolidated.pdf](sediment://file_000000003e9871fd91fcf17af2bb14c1)

---

## Provider strategy (phase approach)
### Phase 1 (ship fast)
Implement **one** on-ramp first:
- Either **Transak** OR **MoonPay** (pick the one that onboards our entity fastest and has strongest global coverage for USDT on BNB Chain)

### Phase 2 (reliability + conversion)
Add second on-ramp or an aggregator.
- Goal: reduce payment failures (banks, regions, card types)

---

## System architecture (what dev must build)

### Components
1) **Frontend (Next.js/React)**
   - Login UI (email/OTP or social)
   - “Add funds” UI (launch on-ramp widget)
   - “Waiting for funds” detector + status
   - “Approve + Deposit” on-chain transaction UI

2) **Backend API**
   - User account + embedded wallet mapping
   - Compliance gate endpoints (geo + blocklist)
   - On-ramp session creation (server-side signing if required)
   - On-ramp webhook receiver (payment status)
   - Funds-arrival watcher (optional) or indexer integration
   - Audit logs

3) **Wallet Layer (Embedded wallet provider)**
   - Create wallet per user
   - Provide signing for on-chain txs in-app (approve/deposit)

4) **On-ramp provider**
   - Embedded widget checkout
   - KYC + sanctions screening
   - Sends USDT (BEP-20) to the embedded wallet address

---

## Required backend endpoints (minimal)
### Auth / User
- `POST /api/auth/start` (send OTP / start social)
- `POST /api/auth/verify`
- `GET /api/me` (returns user + walletAddress)

### Compliance gate
- `GET /api/compliance/precheck`
  - Inputs: IP, optional country from headers, optional wallet/email hash
  - Output: `{ allowed: boolean, reason?: string }`
- `POST /api/compliance/blocklist`
  - Admin-only: add/remove blocked wallet/email hash/country

### On-ramp session
- `POST /api/onramp/session`
  - Checks compliance first
  - Creates provider “session” or signed widget URL/params
  - Returns `{ provider, widgetUrl | widgetParams }`

### On-ramp webhook
- `POST /api/onramp/webhook/:provider`
  - Verify signature
  - Store event
  - Update `deposit_intents` table

### Funds detection / balance
- `GET /api/wallet/balance?asset=USDT`
  - Returns embedded wallet USDT balance
- Optional: `POST /api/wallet/watch` to start monitoring a new deposit intent

---

## Data model (minimum tables)
### `users`
- `id`
- `email` (or phone)
- `createdAt`

### `wallets`
- `userId`
- `address` (BNB chain)
- `walletProvider`
- `createdAt`

### `deposit_intents`
- `id`
- `userId`
- `walletAddress`
- `provider` (moonpay|transak)
- `fiatAmount`
- `cryptoAsset` (USDT)
- `network` (BSC)
- `status` (created|kyc_pending|paid|crypto_sent|crypto_confirmed|failed|blocked)
- `providerRef` (session id)
- `createdAt`

### `compliance_blocks`
- `type` (country|wallet|user)
- `value`
- `reason`
- `createdAt`

---

## UX requirements (non-negotiable)
### “Normie mode”
- Never mention “seed phrase”
- Never require MetaMask
- Do not show chain jargon unless “Advanced” is enabled

### Funding screen copy (simple)
- “Buy USDT with card”
- “Funds show up in 1–10 minutes (sometimes longer)”
- “Minimum to start: 500 USDT”  [oai_citation:4‡Appendix.pdf](sediment://file_00000000ca8471f89f8c33e14fc1852b)

### Gas handling (important)
Approving USDT and depositing requires gas on BNB chain.
We need one of:
- On-ramp includes a small amount of **BNB** (preferred for MVP), OR
- We sponsor gas (more complex; later phase)

For MVP:
- Add a checkbox: “Include network fee buffer (recommended)”
- Or do a second small on-ramp purchase for BNB if needed

---

## Smart contract interaction notes (frontend)
Because the vault is controlled by the wallet, the app must:
1) Ensure wallet is connected (embedded)
2) If vault creation:
   - require >= 500 USDT (BEP-20) available  [oai_citation:5‡Appendix.pdf](sediment://file_00000000ca8471f89f8c33e14fc1852b)
3) Execute:
   - `USDT.approve(vaultFactoryOrVault, amount)`
   - `VaultFactory.createVault(amount, config...)` OR `Vault.deposit(amount)` depending on contract design
4) After tx confirms:
   - show vault dashboard

---

## Error handling (must implement)
- KYC rejected → show clear message and stop
- Sanctions blocked → show “Unavailable in your region / account”
- Payment succeeds but crypto delayed → keep “waiting for funds” with retry
- User buys wrong network/asset → warn + instructions (we can’t fix their mistake)
- Chargebacks → provider handles; we should mark the intent as “reversed/failed” if notified

---

## Acceptance criteria (definition of done)
- User can create account, get embedded wallet address
- User can open on-ramp checkout and purchase **USDT (BEP-20)** into that address
- App detects arrival and enables deposit
- User deposits into vault without installing any wallet app
- Sanctioned regions/users are blocked *before* checkout
- All on-ramp webhooks are verified + logged
- Vault created requires **500 USDT minimum**  [oai_citation:6‡Appendix.pdf](sediment://file_00000000ca8471f89f8c33e14fc1852b)
- Vault ownership remains the originating wallet address  [oai_citation:7‡YieldLoop Consolidated.pdf](sediment://file_000000003e9871fd91fcf17af2bb14c1)

---

## Dev notes / implementation order
1) Embedded wallet integration (create wallet per user)
2) Compliance precheck gate (geo + blocklist)
3) On-ramp session creation + widget launch
4) Webhooks + deposit intent statuses
5) Balance detection / funds arrival
6) Approve + deposit tx flow
7) Add 2nd on-ramp provider (optional)

---

## Open items (don’t guess — implement as config)
- Which embedded wallet provider (Privy/Magic/Web3Auth/etc.)
- Which on-ramp provider first (Transak vs MoonPay)
- Sanctions vendor (if any) for our own screening layer
- Country allow/deny list (config)
- Whether we sponsor gas (later phase)
