# VVS Finance DEX Integration

Barzakh AI integrates with **VVS Finance**, the leading decentralized exchange on Cronos with over $100M TVL. Our AI agents can fetch swap quotes, token lists, and liquidity pool information in real-time.

## 🎯 Overview

VVS Finance provides the core DeFi infrastructure for Cronos. Our integration enables AI-powered DeFi queries and swap analysis.

```
┌─────────────────────────────────────────────────────────────────┐
│                    VVS FINANCE INTEGRATION                      │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│    User Query                    AI Tool                        │
│    ─────────                     ───────                        │
│                                                                 │
│    "Swap 100 CRO to VVS"    →    getVVSSwapQuote               │
│    "What tokens on VVS?"    →    getVVSTokenList               │
│    "CRO-USDC pool stats"    →    getVVSPoolInfo                │
│                                                                 │
│                         ▼                                       │
│                                                                 │
│              ┌─────────────────────────┐                       │
│              │    VVS Finance API      │                       │
│              │    + Smart Contracts    │                       │
│              └─────────────────────────┘                       │
│                         │                                       │
│                         ▼                                       │
│              ┌─────────────────────────┐                       │
│              │    Formatted Response   │                       │
│              │    with AI Analysis     │                       │
│              └─────────────────────────┘                       │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## 🔧 Available Tools

### 1. getVVSSwapQuote

Get a swap quote for trading one token for another on VVS Finance.

**Parameters:**
| Parameter | Type | Description |
|-----------|------|-------------|
| `tokenIn` | string | Input token symbol (e.g., "CRO", "USDC") |
| `tokenOut` | string | Output token symbol (e.g., "VVS", "WCRO") |
| `amountIn` | string | Amount of input token to swap |
| `slippage` | number | Optional slippage tolerance (default: 0.5%) |

**Example:**
```typescript
const quote = await getVVSSwapQuote({
  tokenIn: "CRO",
  tokenOut: "VVS",
  amountIn: "100",
  slippage: 0.5
});
```

**Response:**
```json
{
  "success": true,
  "quote": {
    "amountIn": "100",
    "amountOut": "45234.56",
    "tokenIn": {
      "symbol": "CRO",
      "address": "0x...",
      "decimals": 18
    },
    "tokenOut": {
      "symbol": "VVS",
      "address": "0x...",
      "decimals": 18
    },
    "priceImpact": "0.12%",
    "route": ["CRO", "USDC", "VVS"],
    "minimumReceived": "44981.78",
    "executionPrice": "452.35 VVS per CRO"
  }
}
```

---

### 2. getVVSTokenList

Get the list of supported tokens on VVS Finance with their prices and liquidity.

**Parameters:**
| Parameter | Type | Description |
|-----------|------|-------------|
| `limit` | number | Optional, max tokens to return (default: 50) |

**Example:**
```typescript
const tokens = await getVVSTokenList({ limit: 20 });
```

**Response:**
```json
{
  "success": true,
  "tokens": [
    {
      "symbol": "CRO",
      "name": "Cronos",
      "address": "0x...",
      "decimals": 18,
      "priceUSD": "0.089",
      "volume24h": "$1,234,567",
      "liquidity": "$15,000,000"
    },
    {
      "symbol": "VVS",
      "name": "VVS Finance",
      "address": "0x...",
      "decimals": 18,
      "priceUSD": "0.00019",
      "volume24h": "$567,890",
      "liquidity": "$8,000,000"
    }
  ],
  "totalTokens": 150
}
```

---

### 3. getVVSPoolInfo

Get detailed information about a liquidity pool on VVS Finance.

**Parameters:**
| Parameter | Type | Description |
|-----------|------|-------------|
| `pair` | string | Trading pair (e.g., "CRO-USDC", "VVS-CRO") |

**Example:**
```typescript
const pool = await getVVSPoolInfo({ pair: "CRO-USDC" });
```

**Response:**
```json
{
  "success": true,
  "pool": {
    "pair": "CRO-USDC",
    "address": "0x...",
    "token0": {
      "symbol": "CRO",
      "reserve": "15,234,567"
    },
    "token1": {
      "symbol": "USDC",
      "reserve": "1,356,789"
    },
    "tvl": "$2,713,578",
    "volume24h": "$234,567",
    "fees24h": "$703.70",
    "apr": {
      "trading": "9.5%",
      "farming": "15.2%",
      "total": "24.7%"
    }
  }
}
```

---

## 🤖 AI Agent Usage Examples

### Example 1: Swap Analysis
```
User: "How much VVS would I get for 500 CRO?"

AI Response:
Based on current VVS Finance rates:
• Input: 500 CRO
• Output: ~226,172 VVS
• Price Impact: 0.08%
• Route: CRO → VVS (direct)
• Minimum Received: 224,910 VVS (0.5% slippage)

Current rate: 452.34 VVS per CRO
```

### Example 2: Pool Research
```
User: "What's the APR for the CRO-USDC pool on VVS?"

AI Response:
CRO-USDC Pool on VVS Finance:
• TVL: $2.7M
• Trading APR: 9.5%
• Farming APR: 15.2%
• Total APR: 24.7%
• 24h Volume: $234,567
```

### Example 3: Token Discovery
```
User: "What are the top tokens by liquidity on VVS?"

AI Response:
Top VVS Finance tokens by liquidity:
1. CRO - $15M liquidity
2. USDC - $12M liquidity
3. USDT - $8M liquidity
4. VVS - $8M liquidity
5. WCRO - $6M liquidity
```

---

## 📊 VVS Finance Key Metrics

| Metric | Value |
|--------|-------|
| Total Value Locked | $100M+ |
| Trading Pairs | 150+ |
| Daily Volume | $5M+ |
| Trading Fee | 0.3% |

---

## 🔗 Integration Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                    BARZAKH AI PLATFORM                          │
│                                                                 │
│    ┌──────────────┐    ┌──────────────┐    ┌──────────────┐   │
│    │   Intent     │    │    Tool      │    │   Response   │   │
│    │  Classifier  │───▶│   Router     │───▶│   Renderer   │   │
│    └──────────────┘    └──────┬───────┘    └──────────────┘   │
│                               │                                 │
│                    ┌──────────▼──────────┐                     │
│                    │   VVS TOOL SUITE    │                     │
│                    │                     │                     │
│                    │ • getVVSSwapQuote   │                     │
│                    │ • getVVSTokenList   │                     │
│                    │ • getVVSPoolInfo    │                     │
│                    └──────────┬──────────┘                     │
│                               │                                 │
└───────────────────────────────┼─────────────────────────────────┘
                                │
                    ┌───────────▼───────────┐
                    │                       │
                    │   VVS FINANCE         │
                    │   Smart Contracts     │
                    │   + Subgraph API      │
                    │                       │
                    └───────────────────────┘
```

---

## 🎯 Hackathon Track Alignment

This integration directly supports the **Crypto.com × Cronos Ecosystem Integrations** track:

| Requirement | Our Implementation |
|-------------|-------------------|
| Cronos dApp integrations | ✅ VVS Finance - leading Cronos DEX |
| DeFi functionality | ✅ Swap quotes, pool info, token lists |
| AI-driven automation | ✅ Natural language DeFi queries |
| Real-time data | ✅ Live prices and liquidity data |

---

## 📚 Related Documentation

- [Cronos Integration Overview](./CRONOS-INTEGRATION.md)
- [x402 Payment Implementation](./X402-IMPLEMENTATION.md)
- [VVS Finance Official Docs](https://docs.vvs.finance/)
