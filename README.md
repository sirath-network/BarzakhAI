# Barzakh AI

<p align="center">
  <img width="100%" alt="Barzakh AI Banner" src="https://github.com/user-attachments/assets/bdb03347-8615-4be3-8920-aca4a7fc54b5" />
</p>

<p align="center">
  <strong>🧠 AI-Powered Blockchain Intelligence Platform</strong>
</p>

<p align="center">
  <a href="https://chat.barzakh.tech">
    <img src="https://img.shields.io/badge/Live-chat.barzakh.tech-blue?style=for-the-badge" alt="Live Demo">
  </a>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/TypeScript-5.6-3178C6?logo=typescript&logoColor=white" alt="TypeScript">
  <img src="https://img.shields.io/badge/Next.js-15.4-black?logo=next.js" alt="Next.js">
  <img src="https://img.shields.io/badge/React-19.0-61DAFB?logo=react&logoColor=black" alt="React">
  <img src="https://img.shields.io/badge/Node.js-18+-339933?logo=node.js&logoColor=white" alt="Node.js">
  <img src="https://img.shields.io/badge/PostgreSQL-15-4169E1?logo=postgresql&logoColor=white" alt="PostgreSQL">
  <img src="https://img.shields.io/badge/pnpm-8.6-F69220?logo=pnpm&logoColor=white" alt="pnpm">
  <img src="https://img.shields.io/badge/Turborepo-2.4-EF4444?logo=turborepo&logoColor=white" alt="Turborepo">
  <img src="https://img.shields.io/badge/Vercel-Deployed-black?logo=vercel" alt="Vercel">
  <img src="https://img.shields.io/badge/License-MIT-green" alt="MIT License">
</p>

<p align="center">
  <a href="docs/ARCHITECTURE.md">Architecture</a> •
  <a href="docs/FEATURES.md">Features</a> •
  <a href="docs/PAYMENTS.md">Payments</a> •
  <a href="docs/SECURITY.md">Security</a> •
  <a href="docs/API.md">API</a> •
  <a href="docs/DEMO.md">Demo</a>
</p>

---

## Overview

Barzakh AI is an enterprise-grade, full-stack blockchain analytics platform that combines real-time on-chain data with multi-model AI orchestration. Built as a **Turborepo monorepo** with pnpm workspaces, it provides intelligent wallet analysis, DeFi insights, automated blockchain workflows, and gasless crypto payments.

### ✨ Key Capabilities

| Feature | Description |
|---------|-------------|
| **Multi-Model AI** | GPT-4o/4.1/5, Claude Opus 4.5, Grok 4.1, GLM 4.6 with intelligent routing |
| **50+ Blockchain Tools** | Chain-specific analyzers for Cronos, EVM, Aptos, Solana, Flow, SEI, Zeta, Monad |
| **x402 Gasless Payments** | EIP-3009/EIP-712 gasless USDC payments on Cronos |
| **VVS DEX Integration** | Swap quotes, liquidity pools, and token lists from VVS Finance |
| **Enterprise Security** | 2FA (TOTP), wallet signature auth, Cloudflare API Shield |
| **Real-time Streaming** | Token-by-token SSE output with Vercel AI SDK |

> 📐 See [Architecture](docs/ARCHITECTURE.md) for system design and tech stack details

---

## Quick Start

### Prerequisites

| Requirement | Version |
|-------------|---------|
| Node.js | 18+ |
| pnpm | 8.6+ |
| PostgreSQL | 15+ |

### Installation

```bash
# Clone repository
git clone https://github.com/sirath-network/barzakh-ai.git
cd barzakh-ai

# Install dependencies
pnpm install

# Set up environment
cp apps/frontend/.env.example apps/frontend/.env.local

# Run database migrations
pnpm --filter frontend db:migrate

# Start development server
pnpm dev
```

### Commands

| Command | Description |
|---------|-------------|
| `pnpm dev` | Start all apps in dev mode |
| `pnpm build` | Build all apps |
| `pnpm lint` | Lint all packages |
| `pnpm --filter frontend db:migrate` | Run migrations |
| `pnpm --filter frontend db:studio` | Open Drizzle Studio |

---

## Documentation

| Document | Description |
|----------|-------------|
| [ARCHITECTURE.md](docs/ARCHITECTURE.md) | System design, tech stack, project structure |
| [FEATURES.md](docs/FEATURES.md) | AI models, 50+ blockchain tools, VVS DEX |
| [PAYMENTS.md](docs/PAYMENTS.md) | x402 crypto payment protocol |
| [SECURITY.md](docs/SECURITY.md) | 5-layer defense, authentication |
| [API.md](docs/API.md) | REST API reference |
| [DEMO.md](docs/DEMO.md) | Live demo, screenshots, sample queries |

---

## 🔗 Cronos x402 Integration

Built for the **Cronos x402 Paytech**, featuring deep integration with the Cronos ecosystem.

### ⚡ x402 Gasless Payments
- EIP-3009 TransferWithAuthorization for gasless USDC.e transfers
- Complete subscription lifecycle with automated expiry handling
- Zero gas fees for users - facilitator pays all gas costs

### 📊 Crypto.com Market Data MCP
- Real-time cryptocurrency prices from Crypto.com
- Market overview with BTC dominance and trending tokens
- CRO-specific market analytics

### 💱 VVS Finance DEX
- Swap quotes with price impact analysis
- Liquidity pool information (TVL, APR, volume)
- Supported token lists with live prices

### 🔧 20+ Cronos Blockchain Tools
- Wallet balance queries (single & batch)
- Transaction history and internal transactions
- Token transfers and CRC-20 balances
- Block info, gas prices, and contract analysis

📖 **Documentation**: 
- [Cronos Integration](docs/CRONOS-INTEGRATION.md)
- [x402 Implementation](docs/X402-IMPLEMENTATION.md)  
- [VVS DEX Integration](docs/VVS-DEX-INTEGRATION.md)

---

## Deployment

### Vercel (Recommended)

```bash
vercel --prod
```

### Environment Configuration

| Environment | URL | Branch |
|-------------|-----|--------|
| Production | chat.barzakh.tech | main |

---

## Contributing

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

---

## License

MIT License - see [LICENSE](LICENSE)

---

<p align="center">
  <strong>Built by <a href="https://github.com/sirath-network">Sirath Network</a></strong>
</p>

<p align="center">
  <sub>🚀 Powered by AI | ⛓️ Built on Blockchain | 🔒 Security First</sub>
</p>
