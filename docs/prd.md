# SplitPay Mobile PRD

## 1. Objective

Build the mobile application for SplitPay using React Native.

The mobile application provides the same core SplitPay functionality as the web application while being optimized for mobile interaction, wallet usage, transaction signing, notifications, and quick access to pools and payments.

The mobile application is a client of the SplitPay Soroban contract.

It must follow the global architecture defined in:

```text
docs/documentation.md
```

That document is the authoritative source for:

* Product architecture
* Contract behavior
* Financial rules
* Pool model
* Payment model
* Distribution model
* Security requirements
* Stellar integration

This PRD only defines mobile-specific requirements.

---

# 2. Technology

Use:

* React Native
* TypeScript
* Expo if appropriate for the current Stellar wallet ecosystem
* React Navigation or Expo Router
* Stellar JavaScript SDK where appropriate
* Current Stellar wallet integration
* Stellar RPC
* SplitPay Soroban contract

Use current versions.

Do not introduce a separate backend specifically for mobile.

---

# 3. Architecture

The mobile application should follow:

```text
Mobile UI
   ↓
Mobile application services
   ↓
Stellar / SplitPay adapter
   ↓
Stellar Network
   ↓
SplitPay Soroban Contract
```

The mobile application must not create its own financial ledger.

The blockchain remains the source of truth.

---

# 4. Wallet

Wallet interaction is central to the mobile application.

Users should be able to:

* Connect a Stellar wallet
* View connected address
* Disconnect
* Approve transactions
* Reject transactions
* View balances
* View transaction status

The application must never request or store private keys.

Wallet integration must follow the current Stellar wallet ecosystem rather than implementing custom private-key management.

---

# 5. Mobile Navigation

Initial navigation:

```text
Home
Pools
Payments
Wallet
Settings
```

Authenticated/connected users should have quick access to:

```text
Home
Pools
Payments
Wallet
```

Settings can contain:

* Network
* Connected wallet
* App preferences
* About
* Contract information

---

# 6. Home

The home screen should provide a concise financial overview.

Show:

* Connected wallet
* Relevant asset balance
* Active pools
* Recent payments
* Recent distributions
* Pending transactions

Avoid fake statistics.

If data is unavailable, show an honest empty state.

---

# 7. Pools

Users should be able to:

* View pools
* Create a pool
* Open pool details
* View members
* View shares
* Manage pool configuration where authorized
* View pool activity

Pool cards should show:

```text
Pool name
Asset
Member count
Status
Recent activity
```

---

# 8. Create Pool

The mobile flow should allow:

```text
Pool name
Asset
Initial configuration
```

The pool name may be stored as application metadata.

The blockchain transaction should contain only data required by the contract.

Flow:

```text
Enter pool details
      ↓
Review
      ↓
Connect/confirm wallet
      ↓
Submit transaction
      ↓
Confirm on Stellar
      ↓
Pool created
```

---

# 9. Pool Details

Display:

```text
Pool name
Pool ID
Owner
Asset
Status
Members
Shares
Payment activity
```

Member display:

```text
Wallet address
Share percentage
```

Use shortened wallet addresses with an option to copy the full address.

Example:

```text
GABC...XYZ9
60%
```

---

# 10. Member Management

Authorized pool owners should be able to:

* Add member
* Remove member
* Update member share

Show the split total prominently.

Example:

```text
Alice      60%
Bob        30%
Charlie     5%

Total       95%
```

Display:

```text
Invalid split
```

until the total reaches 100%.

Frontend validation improves UX.

The contract remains authoritative.

---

# 11. Payment Creation

Users should be able to create a payment associated with a pool.

Fields:

```text
Pool
Amount
Payment reference
```

Before submitting, show the expected distribution:

```text
100 USDC

Alice
60 USDC

Bob
40 USDC
```

The displayed calculation must be consistent with the contract's rules.

The frontend must not become the authority for the final distribution.

---

# 12. Payment Flow

The mobile payment flow should be:

```text
Create Payment
      ↓
Review
      ↓
Wallet Approval
      ↓
Submitting
      ↓
Confirming
      ↓
Settled
```

After settlement:

```text
Payment
Amount
Asset
Pool
Payer
Recipients
Distribution
Transaction
```

---

# 13. Transaction Status

All blockchain operations must communicate their state clearly.

States:

```text
Ready
Awaiting wallet
Signing
Submitted
Confirming
Confirmed
Failed
Rejected
```

Never display a transaction as successful simply because a transaction was submitted.

Only display confirmed state after appropriate network confirmation.

---

# 14. Transaction Details

Each transaction detail screen should show:

```text
Transaction hash
Status
Amount
Asset
Pool
Date
Sender
Recipients
```

Provide a link/deep link to the appropriate Stellar explorer when available.

---

# 15. Payments

The Payments section should show relevant payment activity.

Each item should display:

```text
Payment
Pool
Amount
Asset
Status
Date
```

Users should be able to open a payment and inspect its distribution.

---

# 16. Wallet

The Wallet screen should show:

```text
Connected address
Network
XLM balance
Supported SplitPay asset balances
Recent transactions
```

The application is not intended to become a complete standalone wallet.

The external Stellar wallet remains responsible for key management and transaction signing.

---

# 17. Notifications

Mobile should eventually support notifications for:

* Payment received
* Distribution completed
* Pool invitation
* Pool configuration changes
* Transaction confirmation
* Transaction failure

For V1, notification infrastructure may remain out of scope if the required backend/indexing infrastructure does not yet exist.

Do not create fake notifications.

---

# 18. Offline Behavior

The mobile app should handle temporary network loss gracefully.

When offline:

* Show cached non-sensitive application data where appropriate
* Disable blockchain actions that require network access
* Clearly indicate offline status
* Never pretend a transaction succeeded

After reconnecting, refresh blockchain state.

---

# 19. Security

Never:

* request private keys
* store private keys
* store seed phrases
* store wallet secrets
* create a hidden custodial wallet
* trust frontend financial calculations
* assume transaction success before confirmation

Use secure platform storage only for appropriate non-secret application preferences or wallet connection state supported by the wallet integration.

---

# 20. Error Handling

Handle:

* wallet unavailable
* wallet rejection
* insufficient balance
* wrong network
* contract error
* RPC failure
* transaction timeout
* transaction failure
* invalid pool
* invalid split
* unsupported asset
* disconnected wallet

Errors should be understandable to users.

Avoid exposing raw technical errors unless useful.

---

# 21. UI/UX

The mobile application should feel like the mobile version of the same SplitPay product.

Visual direction:

* Clean
* Minimal
* Premium
* Technical
* Financial
* Fast
* Accessible

Avoid:

* Excessive gradients
* Excessive glass effects
* Crypto clichés
* Excessive animations
* Huge cards
* Fake financial charts
* Cluttered screens

Prioritize:

* Clear hierarchy
* Large readable amounts
* Obvious transaction states
* Simple navigation
* Fast actions

---

# 22. Responsive Device Support

Support common:

* iOS devices
* Android devices

The UI should account for:

* small screens
* large screens
* safe areas
* keyboard interaction
* different aspect ratios
* dark/light system preferences if supported by the design system

---

# 23. Contract Integration

The mobile application must interact with the same SplitPay contract as the web application.

Do not create mobile-specific contract logic.

Do not create a separate mobile financial system.

Conceptually:

```text
                  SplitPay Contract
                    /           \
                   /             \
            splitpay-web    splitpay-mobile
```

Both clients must implement the same protocol semantics.

---

# 24. Shared Logic

When a reusable `splitpay-sdk` exists, mobile should consume it.

Until the SDK exists, isolate blockchain logic inside a dedicated mobile service/adapter layer.

Do not duplicate low-level contract interaction code across every screen.

---

# 25. V1 Scope

V1 should support:

```text
Wallet connection
Home/dashboard
Pool list
Create pool
Pool details
Member management
Share configuration
Create payment
Payment review
Transaction signing
Payment settlement
Distribution view
Payment history
Wallet balances
Transaction details
Testnet
```

---

# 26. V1 Non-Goals

Do not implement:

* fiat payments
* Paystack
* bank withdrawals
* custom token
* multi-chain support
* lending
* staking
* DAO governance
* advanced analytics
* social features
* full standalone wallet functionality
* complex notification infrastructure

---

# 27. Testing

Test:

### UI

* navigation
* loading states
* empty states
* error states
* responsive layouts

### Wallet

* connect
* disconnect
* rejected transaction
* successful transaction

### Contract

* pool creation
* member management
* split configuration
* payment creation
* settlement
* distribution retrieval

### Network

* offline state
* RPC failure
* transaction timeout
* wrong network

---

# 28. Definition of Done

The mobile application is complete when a user can:

```text
Connect Stellar wallet
       ↓
Create a pool
       ↓
Add another wallet
       ↓
Configure 60/40
       ↓
Create payment
       ↓
Approve transaction
       ↓
Settle through SplitPay contract
       ↓
View distribution
       ↓
View transaction
```

The complete flow must work on Stellar Testnet.

The mobile app must use the same SplitPay protocol and financial rules as the web application.

---

# 29. Guiding Principle

Mobile is another client of SplitPay, not another implementation of SplitPay.

The protocol lives in the contract.

The web and mobile applications provide different interfaces to the same protocol.

Financial truth comes from Stellar.


---

# Brand Color Palette

| Token             | Hex       | Usage                       |
|-------------------|-----------|-----------------------------|
| Ink / Background  | `#0B1A33` | Page background             |
| Surface           | `#0F2340` | Cards, nav, modals          |
| Border            | `#1E3358` | Dividers, input borders     |
| Text Primary      | `#FFFFFF` | Headings, body text         |
| Text Muted        | `#94A3B8` | Labels, secondary text      |
| Accent Teal       | `#14B8A6` | CTAs, highlights, icons     |
| Accent Teal Hover | `#0D9488` | Hover / active states       |
| White             | `#FFFFFF` | Pure white where needed     |
