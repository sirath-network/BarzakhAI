# Cronos Blockchain Integration

Barzakh AI provides comprehensive integration with the Cronos blockchain ecosystem, featuring 20+ AI-callable tools for real-time data, DeFi operations, and gasless payments via the x402 protocol.

## 🔗 Ecosystem Integration Overview

```
┌───────────────────────────────────────────────────────────────────────────────┐
│                              BARZAKH AI PLATFORM                              │
├───────────────────────────────────────────────────────────────────────────────┤
│                                                                               │
│   ┌────────────┐      ┌────────────┐      ┌────────────┐      ┌────────────┐  │
│   │  Multi-AI  │      │   Intent   │      │    Tool    │      │  Response  │  │
│   │Orchestrator│─────▶│ Classifier │─────▶│  Executor  │─────▶│  Renderer│  │
│   └────────────┘      └────────────┘      └─────┬──────┘      └────────────┘  │
│                                                 │                             │
│                 ┌───────────────────────────────┴───────────────┐             │
│                 │               CRONOS TOOL SUITE               │             │
│                 └──────┬────────────────────────────────┬───────┘             │
│                        │                                │                     │
│       ┌────────────────┴──────────────┐  ┌──────────────┴────────────────┐    │
│       │    CRYPTO.COM MARKET DATA     │  │      CRONOS EXPLORER API      │    │
│       ├───────────────────────────────┤  ├───────────────────────────────┤    │
│       │ • getCryptoPrice              │  │ • getCronosBalance            │    │
│       │ • getMarketOverview           │  │ • getCronosTransaction        │    │
│       │ • getCronosMarketData         │  │ • getCronosTokenBalance       │    │
│       │ • convertCrypto               │  │ • getCronosTransactionHistory │    │
│       └───────────────────────────────┘  │ • getCronosGasPrice           │    │
│                                          │ • getCronosTokenList          │    │
│       ┌───────────────────────────────┐  │ • getCronosBlockInfo          │    │
│       │        VVS FINANCE DEX        │  │ • +12 more tools              │    │
│       ├───────────────────────────────┤  └───────────────────────────────┘    │
│       │ • getVVSSwapQuote             │                                       │
│       │ • getVVSTokenList             │  ┌───────────────────────────────┐    │
│       │ • getVVSPoolInfo              │  │     x402 PAYMENT PROTOCOL     │    │
│       └───────────────────────────────┘  ├───────────────────────────────┤    │
│                                          │ • Gasless USDC.e transfers    │    │
│                                          │ • EIP-3009 Authorization      │    │
│                                          │ • Subscription automation     │    │
│                                          └───────────────────────────────┘    │
│                                                                               │
└───────────────────────────────────────┬───────────────────────────────────────┘
                                        │
                 ┌──────────────────────┼──────────────────────┐
                 ▼                      ▼                      ▼
         ┌──────────────┐       ┌──────────────┐       ┌──────────────┐
         │    Cronos    │       │   Crypto.    │       │     VVS      │
         │     EVM      │       │     com      │       │   Finance    │
         │  (Chain 25)  │       │     MCP      │       │     DEX      │
         └──────────────┘       └──────────────┘       └──────────────┘
```

---

## 📊 Crypto.com Market Data MCP Integration

We integrate with the **Crypto.com Market Data MCP Server** for real-time cryptocurrency prices and market intelligence.

### Available Tools

| Tool | Description | Example Query |
|------|-------------|---------------|
| `getCryptoPrice` | Real-time price for any cryptocurrency | "What's the CRO price?" |
| `getMarketOverview` | Market summary with BTC dominance, trending tokens | "Show me market overview" |
| `getCronosMarketData` | CRO-specific market data (price, volume, cap) | "CRO market stats" |
| `convertCrypto` | Convert between cryptocurrencies | "Convert 100 CRO to USDC" |

### Implementation Highlights

```typescript
// Real-time price fetching from Crypto.com
const price = await getCryptoPrice({ symbol: "CRO_USDT" });

// Response includes:
// - Current price
// - 24h change percentage
// - Volume data
// - Trend analysis
```

### Data Sources
- **Primary**: Crypto.com Exchange API (real-time)
- **Fallback**: CoinGecko API (aggregated from 900+ exchanges)

---

## 🔧 Cronos Blockchain Tools

Full suite of 20+ tools for interacting with the Cronos EVM blockchain.

### Wallet & Balance Tools

| Tool | Description |
|------|-------------|
| `getCronosBalance` | Get native CRO balance for any wallet |
| `getCronosBalanceMulti` | Batch query multiple wallet balances |
| `getCronosTokenBalance` | Get CRC-20 token balance |
| `getCronosTokenList` | List all tokens held by a wallet |

### Transaction Tools

| Tool | Description |
|------|-------------|
| `getCronosTransaction` | Get transaction details by hash |
| `getCronosTransactionHistory` | Paginated transaction history |
| `getCronosInternalTxList` | Internal transactions (contract calls) |
| `getCronosTokenTransfers` | CRC-20 transfer events |
| `getCronosTxReceiptStatus` | Check transaction success/failure |

### Block & Network Tools

| Tool | Description |
|------|-------------|
| `getCronosBlockInfo` | Block information by number/hash |
| `getCronosGasPrice` | Current gas prices with estimates |
| `getCronosSupply` | Total CRO circulating supply |
| `getCronosBlockByTime` | Find block at specific timestamp |

### Contract Analysis Tools

| Tool | Description |
|------|-------------|
| `getCronosContractABI` | Get ABI for verified contracts |
| `getCronosContractSource` | Verified source code |
| `getCronosTokenInfo` | Token metadata (name, symbol, decimals) |
| `getCronosTokenHolders` | Top token holders |
| `getCronosLogs` | Event logs from contracts |

### Example Usage

```typescript
// Get wallet portfolio on Cronos
const balance = await getCronosBalance({ 
  address: "0x...",
  testnet: false 
});

// Get transaction history
const history = await getCronosTransactionHistory({
  address: "0x...",
  page: 1,
  limit: 10,
  sort: "desc"
});

// Check gas prices
const gas = await getCronosGasPrice({ testnet: false });
// Returns: { low, medium, high, baseFee }
```

---

## 💱 VVS Finance DEX Integration

VVS Finance is the leading DEX on Cronos with $100M+ TVL. We provide AI-accessible tools for swap quotes and liquidity data.

### Available Tools

| Tool | Description |
|------|-------------|
| `getVVSSwapQuote` | Get swap quote for token pairs |
| `getVVSTokenList` | Supported tokens with prices/liquidity |
| `getVVSPoolInfo` | Liquidity pool details (TVL, APR, volume) |

### Swap Quote Example

```typescript
const quote = await getVVSSwapQuote({
  tokenIn: "CRO",
  tokenOut: "VVS",
  amountIn: "100",
  slippage: 0.5 // 0.5%
});

// Response:
// {
//   amountOut: "45000",
//   priceImpact: "0.12%",
//   route: ["CRO", "USDC", "VVS"],
//   minimumReceived: "44775"
// }
```

### Pool Information

```typescript
const pool = await getVVSPoolInfo({
  pair: "CRO-USDC"
});

// Response:
// {
//   tvl: "$15,234,567",
//   apr: "24.5%",
//   volume24h: "$1,234,567",
//   fees24h: "$3,704"
// }
```

---

## ⚡ x402 Gasless Payment Protocol

See [X402-IMPLEMENTATION.md](./X402-IMPLEMENTATION.md) for detailed documentation on our gasless USDC.e payment implementation.

### Quick Overview

- **Protocol**: EIP-3009 TransferWithAuthorization
- **Token**: USDC.e (Bridged USDC via Stargate)
- **Networks**: Cronos Mainnet (Chain 25) + Testnet (Chain 338)
- **Gas Fees**: Zero for users - facilitator pays gas

---

## 🌐 Network Configuration

### Cronos Mainnet
| Property | Value |
|----------|-------|
| Chain ID | 25 |
| RPC URL | https://evm.cronos.org |
| Explorer | https://explorer.cronos.org |
| USDC.e | 0xf951eC28187D9E5Ca673Da8FE6757E6f0Be5F77C |

### Cronos Testnet
| Property | Value |
|----------|-------|
| Chain ID | 338 |
| RPC URL | https://evm-t3.cronos.org |
| Explorer | https://explorer.cronos.org/testnet |
| devUSDC.e | 0xc01efAaF7C5C61bEbFAeb358E1161b537b8bC0e0 |

---

## 🎯 AI-Driven Workflow Examples

### Example 1: Portfolio Analysis
```
User: "Show me the CRO balance for 0xABC..."

AI Flow:
1. Intent classified → "cronos"
2. Tool selected → getCronosBalance
3. Data fetched from Cronos Explorer API
4. Response formatted with explorer link
```

### Example 2: DeFi Query
```
User: "How much would I get swapping 500 CRO to VVS?"

AI Flow:
1. Intent classified → "cronos" (DeFi)
2. Tool selected → getVVSSwapQuote
3. Quote fetched from VVS Finance
4. Response includes price impact, route, slippage
```

### Example 3: Market Analysis
```
User: "What's the current CRO price and market trend?"

AI Flow:
1. Tool selected → getCronosMarketData
2. Crypto.com MCP queried for real-time data
3. Response includes price, volume, 24h change, trend
```

---

## 📚 Related Documentation

- [x402 Implementation Details](./X402-IMPLEMENTATION.md)
- [API Reference](./API.md)
- [Architecture Overview](./ARCHITECTURE.md)
