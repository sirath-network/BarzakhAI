# API Reference

> **Barzakh AI REST API Documentation**

## Overview

Barzakh AI provides a comprehensive REST API for all platform functionality. All endpoints are protected by Cloudflare API Shield with OpenAPI 3.0 schema validation.

### Base URLs

| Environment | URL |
|-------------|-----|
| **Production** | `https://chat.barzakh.tech` |
| **API Production** | `https://staging.barzakh.tech` |

### Authentication

All authenticated endpoints require a session cookie. Session is obtained through one of three methods:
- Email/Password → Session Cookie
- Google OAuth → Session Cookie
- Wallet Signature (SIWE) → Session Cookie

### Response Format

```json
{
  "success": true,
  "data": {},
  "error": null
}
```

### Error Response

```json
{
  "success": false,
  "data": null,
  "error": {
    "code": "ERROR_CODE",
    "message": "Human-readable error message"
  }
}
```

---

## Endpoints

### Authentication

#### OAuth Callback
```http
GET /api/auth/callback/{provider}
```

| Parameter | Type | Description |
|-----------|------|-------------|
| `provider` | path | `google` or `credentials` |
| `code` | query | OAuth authorization code |
| `state` | query | CSRF state parameter |

**Response:** `302 Redirect` to callback URL

---

#### Sign In
```http
POST /api/auth/signin
```

**Request Body:**
```json
{
  "email": "user@example.com",
  "password": "password123"
}
```

**Response:**
```json
{
  "success": true,
  "redirect": "/",
  "requires2FA": false
}
```

**If 2FA required:**
```json
{
  "success": false,
  "requires2FA": true,
  "tempToken": "temp_token_for_2fa_flow"
}
```

---

#### Sign Out
```http
POST /api/auth/signout
```

**Response:** `200 OK` with session cookie cleared

---

#### Get Session
```http
GET /api/auth/session
```

**Response:**
```json
{
  "user": {
    "id": "user_123",
    "email": "user@example.com",
    "name": "John Doe",
    "image": "https://...",
    "walletAddress": "0x..."
  },
  "expires": "2024-02-01T00:00:00.000Z"
}
```

---

#### Get Wallet Nonce
```http
GET /api/auth/nonce?address={walletAddress}
```

| Parameter | Type | Description |
|-----------|------|-------------|
| `address` | query | Ethereum address (0x...) |

**Response:**
```json
{
  "nonce": "Sign this message to verify your wallet ownership:\n\nNonce: a1b2c3d4..."
}
```

---

### Two-Factor Authentication

#### Temporary Login (2FA Required)
```http
POST /api/2fa/temp-login
```

**Request Body:**
```json
{
  "email": "user@example.com",
  "password": "password123"
}
```

**Response:**
```json
{
  "tempToken": "eyJ...",
  "expiresAt": 1704067200
}
```

---

#### Complete Login with 2FA
```http
POST /api/2fa/complete-login
```

**Request Body:**
```json
{
  "tempToken": "eyJ...",
  "totpCode": "123456"
}
```

**Response:**
```json
{
  "success": true
}
```
+ Session cookie set

---

### Billing (x402 Crypto Payments)

#### Get Wallet Verification Nonce
```http
GET /api/billing/x402/verify-wallet?address={address}
```

**Response:**
```json
{
  "message": "Sign to verify wallet for Barzakh AI payment:\nNonce: abc123...",
  "expiresIn": 300
}
```

---

#### Verify Wallet Signature
```http
POST /api/billing/x402/verify-wallet
```

**Request Body:**
```json
{
  "address": "0x742d35Cc6634C0532925a3b844Bc9e7595f...",
  "signature": "0x..."
}
```

**Response:**
```json
{
  "success": true,
  "walletAddress": "0x742d35Cc6634C0532925a3b844Bc9e7595f..."
}
```

---

#### Subscribe with Crypto
```http
POST /api/billing/x402/subscribe
```

**Request Body:**
```json
{
  "planId": "pro",
  "chainId": 25
}
```

**Response:** `402 Payment Required`
```json
{
  "amount": "10000000000000000000",
  "amountFormatted": "10 CRO",
  "recipient": "0x...",
  "chainId": 25,
  "deadline": 1704067200,
  "message": "Send exactly 10 CRO to the recipient address"
}
```

---

#### Verify Payment Transaction
```http
POST /api/billing/x402/verify
```

**Request Body:**
```json
{
  "txHash": "0x..."
}
```

**Response:**
```json
{
  "success": true,
  "subscription": {
    "status": "active",
    "plan": "pro",
    "expiresAt": "2025-01-01T00:00:00.000Z"
  }
}
```

**Error (Block not found - retry):**
```json
{
  "error": "Block not found yet",
  "code": "BLOCK_NOT_FOUND"
}
```

---

### Account Management

#### Delete Account
```http
POST /api/account/delete
```

Requires re-authentication:
- Password is always required
- If 2FA enabled: `twoFactorToken` required
- If 2FA not enabled: `emailOtp` required

**Request Body:**
```json
{
  "password": "current_password",
  "twoFactorToken": "123456",
  "emailOtp": null
}
```

**Response:**
```json
{
  "success": true,
  "message": "Account deleted successfully"
}
```

---

#### Send Account Deletion OTP
```http
POST /api/account/delete/send-otp
```

**Response:**
```json
{
  "success": true,
  "maskedEmail": "u***@example.com",
  "remaining": 2
}
```

---

### Settings

#### Get Settings
```http
GET /api/settings
```

**Response:**
```json
{
  "user": {
    "id": "user_123",
    "email": "user@example.com",
    "name": "John Doe",
    "walletAddress": "0x...",
    "has2FA": true,
    "hasPassword": true
  },
  "subscription": {
    "plan": "pro",
    "status": "active",
    "expiresAt": "2025-01-01T00:00:00.000Z"
  }
}
```

---

#### Update Settings
```http
PATCH /api/settings
```

**Request Body:**
```json
{
  "name": "New Name",
  "theme": "dark"
}
```

**Response:**
```json
{
  "success": true
}
```

---

### Wallet Binding

#### Get Bind Nonce
```http
GET /api/settings/wallet/bind?address={address}
```

**Response:**
```json
{
  "message": "Sign to bind wallet to Barzakh AI account:\nNonce: xyz789..."
}
```

---

#### Bind Wallet
```http
POST /api/settings/wallet/bind
```

Requires re-authentication (password + 2FA or email OTP)

**Request Body:**
```json
{
  "address": "0x...",
  "signature": "0x...",
  "password": "current_password",
  "twoFactorToken": "123456"
}
```

**Response:**
```json
{
  "success": true,
  "walletAddress": "0x..."
}
```

---

#### Unbind Wallet
```http
POST /api/settings/wallet/unbind
```

Requires re-authentication

**Request Body:**
```json
{
  "password": "current_password",
  "twoFactorToken": "123456"
}
```

**Response:**
```json
{
  "success": true
}
```

---

## Rate Limits

| Endpoint Category | Limit | Window |
|-------------------|-------|--------|
| Authentication | 10 requests | 1 minute |
| Chat/AI | 60 requests | 1 minute (varies by tier) |
| Billing | 20 requests | 1 minute |
| Settings | 30 requests | 1 minute |
| OTP Requests | 3 requests | 10 minutes |

---

## Error Codes

| Code | HTTP Status | Description |
|------|-------------|-------------|
| `UNAUTHORIZED` | 401 | Authentication required |
| `FORBIDDEN` | 403 | Permission denied |
| `NOT_FOUND` | 404 | Resource not found |
| `RATE_LIMITED` | 429 | Too many requests |
| `VALIDATION_ERROR` | 400 | Invalid request data |
| `REQUIRES_2FA` | 401 | 2FA token required |
| `REQUIRES_REAUTH` | 401 | Re-authentication required |
| `BLOCK_NOT_FOUND` | 409 | Blockchain block not confirmed (retry) |
| `INVALID_SIGNATURE` | 400 | Wallet signature verification failed |

---

## OpenAPI Schema

Full OpenAPI 3.0 specification is available for API integration:
- Used by Cloudflare API Shield for request validation
- All requests are validated against schema before processing
- Strict type checking on all parameters
