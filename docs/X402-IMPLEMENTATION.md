# x402 Payment Protocol Implementation

Barzakh AI implements the **x402 gasless payment protocol** for USDC.e transactions on Cronos. This document details our implementation of EIP-3009 TransferWithAuthorization for automated, gas-free subscription payments.

## 🎯 Overview

The x402 protocol enables **gasless USDC.e transfers** where users pay only the token amount—no CRO gas fees required. The Cronos x402 Facilitator handles gas costs and on-chain settlement.

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                              x402 PAYMENT FLOW                              │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│   ┌─────────────┐         ┌─────────────┐         ┌─────────────┐           │
│   │    User     │         │   Barzakh   │         │    x402     │           │
│   │   Wallet    │         │     AI      │         │ Facilitator │           │
│   └──────┬──────┘         └──────┬──────┘         └──────┬──────┘           │
│          │                       │                       │                  │
│          │    1. Request Sub     │                       │                  │
│          │──────────────────────▶│                       │                  │
│          │                       │                       │                  │
│          │    2. EIP-712 Data    │                       │                  │
│          │◀──────────────────────│                       │                  │
│          │                       │                       │                  │
│          │    3. Sign w/ Wallet  │                       │                  │
│          │──────────────────────▶│                       │                  │
│          │                       │                       │                  │
│          │                       │   4. Verify Payment   │                  │
│          │                       │──────────────────────▶│                  │
│          │                       │                       │                  │
│          │                       │   5. Settlement OK    │                  │
│          │                       │◀──────────────────────│                  │
│          │                       │                       │   ┌──────────┐   │
│          │                       │   6. Settle On-Chain  │   │  Cronos  │   │
│          │                       │──────────────────────▶│──▶│   EVM   │   │
│          │                       │                       │   │ (Chain)  │   │
│          │                       │      7. TX Hash       │   └──────────┘   │
│          │                       │◀──────────────────────│                  │
│          │                       │                       │                  │
│          │   8. Sub Activated    │                       │                  │
│          │◀──────────────────────│                       │                  │
│          │                       │                       │                  │
│   ───────┴───────────────────────┴───────────────────────┴───────           │
│                                                                             │
│    💡 User signs ONCE  ▶  Facilitator pays gas  ▶  Transfer executes       │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 🔐 EIP-3009: TransferWithAuthorization

We implement [EIP-3009](https://eips.ethereum.org/EIPS/eip-3009) which enables meta-transactions for ERC-20 tokens. Users sign an off-chain authorization that allows a third party to execute the transfer.

### EIP-712 Typed Data Structure

```typescript
const TRANSFER_WITH_AUTHORIZATION_TYPES = {
  TransferWithAuthorization: [
    { name: "from", type: "address" },
    { name: "to", type: "address" },
    { name: "value", type: "uint256" },
    { name: "validAfter", type: "uint256" },
    { name: "validBefore", type: "uint256" },
    { name: "nonce", type: "bytes32" },
  ],
};
```

### EIP-712 Domain

```typescript
const EIP712_DOMAIN = {
  name: "Bridged USDC (Stargate)",
  version: "1",
  chainId: 25,  // Cronos Mainnet
  verifyingContract: "0xf951eC28187D9E5Ca673Da8FE6757E6f0Be5F77C" // USDC.e
};
```

---

## 💰 USDC.e Token Details

We use **Bridged USDC (Stargate)** which is the canonical USDC on Cronos.

### Mainnet
| Property | Value |
|----------|-------|
| Token Name | Bridged USDC (Stargate) |
| Symbol | USDC.e |
| Decimals | 6 |
| Contract | `0xf951eC28187D9E5Ca673Da8FE6757E6f0Be5F77C` |
| Chain ID | 25 |

### Testnet
| Property | Value |
|----------|-------|
| Symbol | devUSDC.e |
| Decimals | 6 |
| Contract | `0xc01efAaF7C5C61bEbFAeb358E1161b537b8bC0e0` |
| Chain ID | 338 |

---

## 🔄 Payment Flow Implementation

### Step 1: Create Payment Requirements

```typescript
function createPaymentRequirements(
  payTo: string,
  usdAmount: number,
  network: "mainnet" | "testnet" = "testnet"
): PaymentRequirements {
  return {
    scheme: "exact",
    network: network === "mainnet" ? "cronos-mainnet" : "cronos-testnet",
    payTo,
    asset: USDC_ADDRESS[network],
    maxAmountRequired: usdToUsdcUnits(usdAmount), // Convert to 6 decimals
    maxTimeoutSeconds: 300,
    description: "Barzakh AI Subscription",
    mimeType: "application/json",
  };
}
```

### Step 2: Generate EIP-712 Typed Data

```typescript
function createTransferAuthorizationTypedData(
  from: string,
  to: string,
  value: string,
  network: "mainnet" | "testnet"
) {
  const now = Math.floor(Date.now() / 1000);
  const nonce = generateNonce(); // Random 32-byte hex

  return {
    domain: getEIP712Domain(network),
    types: TRANSFER_WITH_AUTHORIZATION_TYPES,
    primaryType: "TransferWithAuthorization",
    message: {
      from,
      to,
      value,
      validAfter: "0",
      validBefore: (now + 300).toString(), // 5 minute validity
      nonce,
    },
  };
}
```

### Step 3: User Signs with Wallet

```typescript
// Frontend - using wagmi/viem
const signature = await signTypedDataAsync({
  domain: typedData.domain,
  types: typedData.types,
  primaryType: typedData.primaryType,
  message: typedData.message,
});
```

### Step 4: Build Payment Header

```typescript
function buildPaymentHeader(
  signature: string,
  authorization: AuthorizationParams,
  asset: string,
  network: "mainnet" | "testnet"
): string {
  const header: X402PaymentHeader = {
    x402Version: 1,
    scheme: "exact",
    network: CRONOS_NETWORKS[network].network,
    payload: {
      from: authorization.from,
      to: authorization.to,
      value: authorization.value,
      validAfter: authorization.validAfter,
      validBefore: authorization.validBefore,
      nonce: authorization.nonce,
      signature,
      asset,
    },
  };
  
  return base64Encode(JSON.stringify(header));
}
```

### Step 5: Verify with Facilitator

```typescript
const verifyResponse = await fetch(
  "https://facilitator.cronoslabs.org/v2/x402/verify",
  {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
      "X402-Version": "1",
    },
    body: JSON.stringify({
      x402Version: 1,
      paymentHeader,
      paymentRequirements,
    }),
  }
);

// Response: { isValid: true, payer: "0x..." }
```

### Step 6: Settle On-Chain

```typescript
const settleResponse = await fetch(
  "https://facilitator.cronoslabs.org/v2/x402/settle",
  {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
      "X402-Version": "1",
    },
    body: JSON.stringify({
      x402Version: 1,
      paymentHeader,
      paymentRequirements,
    }),
  }
);

// Response: { success: true, txHash: "0x...", blockNumber: 12345 }
```

---

## 📅 Subscription Lifecycle Management

We implement a complete subscription system with automated expiry handling.

### Subscription Tiers

| Tier | Monthly | Yearly | Message Limit |
|------|---------|--------|---------------|
| Free | $0 | - | 10/day |
| Pro | $25 | $239 | 50-150/day |
| Ultimate | $49 | $469 | 250-500/day |

### Subscription Activation Flow

```typescript
// 1. User pays via x402
const settlement = await settlePayment(paymentHeader, requirements);

// 2. Calculate subscription period
const now = new Date();
const periodEnd = billingCycle === "yearly"
  ? new Date(now.setFullYear(now.getFullYear() + 1))
  : new Date(now.setMonth(now.getMonth() + 1));

// 3. Update user record
await db.update(user).set({
  tier: "pro",
  billingCycle: "monthly",
  x402PeriodEnd: periodEnd,
  x402TxHash: settlement.txHash,
  dailyMessageRemaining: getMessageLimit("pro", "monthly"),
});
```

### Automated Expiry Check (Cron Job)

```typescript
// Runs every 6 hours via Vercel Cron
export async function GET(request: NextRequest) {
  // Verify cron secret
  if (request.headers.get("Authorization") !== `Bearer ${CRON_SECRET}`) {
    return new Response("Unauthorized", { status: 401 });
  }

  // Find expired subscriptions
  const expiredUsers = await db
    .select()
    .from(user)
    .where(
      and(
        isNotNull(user.x402PeriodEnd),
        lt(user.x402PeriodEnd, new Date())
      )
    );

  // Downgrade expired users
  for (const expiredUser of expiredUsers) {
    await db.update(user).set({
      tier: "free",
      x402PeriodEnd: null,
      dailyMessageRemaining: FREE_MESSAGE_LIMIT,
    }).where(eq(user.id, expiredUser.id));
  }

  return NextResponse.json({ 
    processed: expiredUsers.length 
  });
}
```

### Real-Time Expiry Check

We also check subscription status on every API request:

```typescript
// In /api/chat route
if (user.tier !== "free" && user.x402PeriodEnd) {
  if (new Date(user.x402PeriodEnd) < new Date()) {
    // Immediately downgrade expired user
    await db.update(user).set({
      tier: "free",
      dailyMessageRemaining: FREE_MESSAGE_LIMIT,
    });
  }
}
```

---

## 🔒 Security Considerations

### Nonce Generation

```typescript
function generateNonce(): string {
  const array = new Uint8Array(32);
  crypto.getRandomValues(array);
  return "0x" + Array.from(array)
    .map(b => b.toString(16).padStart(2, "0"))
    .join("");
}
```

- 32-byte cryptographically random nonce
- Prevents replay attacks
- Each signature can only be used once

### Validity Window

- `validAfter: 0` - Valid immediately
- `validBefore: now + 300` - Expires in 5 minutes
- Prevents stale signatures from being used

### User Custody

- Users sign with their own wallet (MetaMask, WalletConnect, etc.)
- Private keys never leave the user's device
- Platform never has access to user funds

---

## 🌐 API Endpoints

### POST `/api/billing/x402/verify-wallet`
Verify a wallet address is valid for x402 payments.

### POST `/api/billing/x402/subscribe`
Initiate a subscription payment flow.

### POST `/api/billing/x402/settle`
Settle a signed payment on-chain.

### POST `/api/billing/x402/verify`
Verify a payment signature before settling.

---

## 📚 References

- [EIP-3009: Transfer With Authorization](https://eips.ethereum.org/EIPS/eip-3009)
- [EIP-712: Typed Structured Data Hashing](https://eips.ethereum.org/EIPS/eip-712)
- [Cronos x402 Facilitator Documentation](https://docs.cronos.org/cronos-x402-facilitator/introduction)
- [Cronos x402 API Reference](https://docs.cronos.org/cronos-x402-facilitator/api-reference)

---

## 🎯 Hackathon Track Alignment

This implementation directly addresses the **x402 Agentic Finance/Payment Track** requirements:

| Requirement | Our Implementation |
|-------------|-------------------|
| Automated settlement pipelines | ✅ Full verify → settle → activate flow |
| Multi-step x402 automation | ✅ Subscription lifecycle with cron expiry |
| Programmatic payment flows | ✅ API-driven payment initiation |
| Recurring instruction sets | ✅ Monthly/yearly subscription periods |
| Risk-managed operations | ✅ Validity windows, nonce security, real-time checks |
