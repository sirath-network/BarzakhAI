<p align="center">
  <img src="assets/banner.png" alt="Barzakh AI Banner" width="100%">
</p>

<h1 align="center">Barzakh AI</h1>

<p align="center">
  <strong>🧠 AI-Powered Blockchain Intelligence Platform</strong><br/>
  <em>Streamline On-Chain Workflows with AI Simplicity</em>
</p>

<p align="center">
  <a href="https://chat.barzakh.tech">
    <img src="https://img.shields.io/badge/🌐_Live_Demo-chat.barzakh.tech-blue?style=for-the-badge" alt="Live Demo">
  </a>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/TypeScript-5.0-3178C6?logo=typescript&logoColor=white" alt="TypeScript">
  <img src="https://img.shields.io/badge/Next.js-15-black?logo=next.js" alt="Next.js">
  <img src="https://img.shields.io/badge/React-18-61DAFB?logo=react&logoColor=black" alt="React">
  <img src="https://img.shields.io/badge/PostgreSQL-15-4169E1?logo=postgresql&logoColor=white" alt="PostgreSQL">
  <img src="https://img.shields.io/badge/Cloudflare-R2_|_WAF-F38020?logo=cloudflare&logoColor=white" alt="Cloudflare">
  <img src="https://img.shields.io/badge/Vercel-Deployed-black?logo=vercel" alt="Vercel">
  <img src="https://img.shields.io/badge/License-MIT-green" alt="MIT License">
</p>

<p align="center">
  <a href="docs/ARCHITECTURE.md">Architecture</a> •
  <a href="docs/API.md">API Reference</a> •
  <a href="docs/FEATURES.md">Features</a> •
  <a href="docs/SECURITY.md">Security</a> •
  <a href="docs/DEMO.md">Demo</a>
</p>

---

## Overview

**Barzakh AI** is a full-stack blockchain analytics platform that combines real-time on-chain data analysis with multi-model AI orchestration. Built as a production-grade monorepo with Turborepo, it delivers intelligent wallet analysis, DeFi insights, cross-chain data aggregation, and automated blockchain workflows.

### Core Capabilities

| Capability | Description |
|------------|-------------|
| **Multi-Model AI** | GPT-4o, GPT-4.1, GPT-5, Claude Opus 4.5, Grok 4.1, GLM 4.6 |
| **45+ Blockchain Tools** | Chain-specific analyzers for Cronos, EVM, Aptos, Flow, SEI |
| **x402 Crypto Payments** | Native on-chain payment protocol with wallet signature verification |
| **Enterprise Security** | 4-layer AI security, 2FA, wallet auth, Cloudflare API Shield |
| **Real-Time Intelligence** | Live blockchain data, news aggregation, market sentiment |

---

## Architecture

### High-Level System Design

```
┌──────────────────────────────────────────────────────────────────────────────────────┐
│                                    EDGE LAYER                                        │
│  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐  │
│  │   Cloudflare    │  │   API Shield    │  │  Rate Limiter   │  │    R2 Storage   │  │
│  │   WAF + DDoS    │  │ OpenAPI 3.0 Spec│  │  Token Bucket   │  │   Object Store  │  │
│  └────────┬────────┘  └────────┬────────┘  └────────┬────────┘  └────────┬────────┘  │
└───────────┼────────────────────┼────────────────────┼────────────────────┼───────────┘
            │                    │                    │                    │
            └────────────────────┴────────────────────┴────────────────────┘
                                          │
                                          ▼
┌───────────────────────────────────────────────────────────────────────────────────────┐
│                              APPLICATION LAYER (Vercel)                               │
│  ┌─────────────────────────────────────────────────────────────────────────────────┐  │
│  │                         Next.js 15 (App Router)                                 │  │
│  │  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌─────────────────────────┐ │  │
│  │  │   React 19  │  │   Server    │  │  API Routes │  │    Middleware Chain     │ │  │
│  │  │     RSC     │  │  Components │  │   (Edge)    │  │  Auth → Rate → Validate │ │  │
│  │  └─────────────┘  └─────────────┘  └─────────────┘  └─────────────────────────┘ │  │
│  └─────────────────────────────────────────────────────────────────────────────────┘  │
└───────────────────────────────────────────────────────────────────────────────────────┘
                                          │
                                          ▼
┌─────────────────────────────────────────────────────────────────────────────────────┐
│                                   CORE SERVICES                                     │
│  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐ │
│  │  Chat Engine    │  │ AI Orchestrator │  │  Tool Executor  │  │ Stream Processor│ │
│  │  Vercel AI SDK  │  │  Multi-Model    │  │   45+ Tools     │  │   SSE/Chunks    │ │
│  └────────┬────────┘  └────────┬────────┘  └────────┬────────┘  └────────┬────────┘ │
└───────────┼────────────────────┼────────────────────┼────────────────────┼──────────┘
            │                    │                    │                    │
            └────────────────────┴────────────────────┴────────────────────┘
                                          │
                                          ▼
┌─────────────────────────────────────────────────────────────────────────────────────┐
│                                   AI LAYER                                          │
│  ┌───────────────────────────────────────────────────────────────────────────────┐  │
│  │                           LLM Provider Abstraction                            │  │
│  │  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────────────┐ │  │
│  │  │  OpenAI  │  │Anthropic │  │   xAI    │  │  Zhipu   │  │   CometAPI       │ │  │
│  │  │ GPT-4o   │  │  Claude  │  │  Grok 2  │  │ GLM-4.6  │  │  (Aggregator)    │ │  │
│  │  │ o1/o3    │  │ Sonnet   │  │          │  │          │  │                  │ │  │
│  │  │          │  │ 4/4.5    │  │          │  │          │  │                  │ │  │
│  │  └──────────┘  └──────────┘  └──────────┘  └──────────┘  └──────────────────┘ │  │
│  └───────────────────────────────────────────────────────────────────────────────┘  │
│  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────────────────────────┐  │
│  │ Prompt Engineer │  │ Input Sanitizer │  │        Response Streamer            │  │
│  │  System Prompts │  │ Injection Guard │  │    Token-by-Token SSE Output        │  │
│  └─────────────────┘  └─────────────────┘  └─────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────────────────────────┘
                                          │
                                          ▼
┌───────────────────────────────────────────────────────────────────────────────────────┐
│                              BLOCKCHAIN TOOLS LAYER                                   │
│  ┌─────────────────────────────────────────────────────────────────────────────────┐  │
│  │                         Chain-Specific Tool Modules                             │  │
│  │  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────────────┐   │  │
│  │  │  Cronos  │  │   EVM    │  │  Aptos   │  │   Flow   │  │       SEI        │   │  │
│  │  │ zkEVM +  │  │ Ethereum │  │   Sui    │  │ Cadence  │  │  Cosmos SDK      │   │  │
│  │  │  EVM     │  │ Polygon  │  │  Move    │  │  FCL     │  │  IBC Transfers   │   │  │
│  │  └──────────┘  └──────────┘  └──────────┘  └──────────┘  └──────────────────┘   │  │
│  └─────────────────────────────────────────────────────────────────────────────────┘  │
│  ┌─────────────────────────────────────────────────────────────────────────────────┐  │
│  │                            Utility Tool Modules                                 │  │
│  │  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────────────┐   │  │
│  │  │DeFi Llama│  │Web Search│  │  News    │  │ X/Twitter│  │  Image Gen       │   │  │
│  │  │   TVL    │  │  Tavily  │  │  Search  │  │  Search  │  │  Gemini 2.5      │   │  │
│  │  │   API    │  │  Search  │  │   API    │  │   API    │  │   Flash/Pro      │   │  │
│  │  └──────────┘  └──────────┘  └──────────┘  └──────────┘  └──────────────────┘   │  │
│  └─────────────────────────────────────────────────────────────────────────────────┘  │
└───────────────────────────────────────────────────────────────────────────────────────┘
                                          │
                                          ▼
┌──────────────────────────────────────────────────────────────────────────────────────┐
│                                  DATA LAYER                                          │
│  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐  │
│  │   PostgreSQL    │  │  Cloudflare R2  │  │   Drizzle ORM   │  │   Connection    │  │
│  │   (Neon/Turso)  │  │  Object Storage │  │   Type-Safe     │  │    Pooling      │  │
│  └─────────────────┘  └─────────────────┘  └─────────────────┘  └─────────────────┘  │
└──────────────────────────────────────────────────────────────────────────────────────┘
```

> 📐 For detailed architecture diagrams and data flows, see [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md)

---

## Tech Stack

| Layer | Technologies |
|-------|-------------|
| **Frontend** | Next.js 15 (App Router), React 18, TypeScript 5.0, TailwindCSS, Radix UI |
| **Backend** | Next.js API Routes, Vercel AI SDK, Server Components |
| **Database** | PostgreSQL (Neon), Drizzle ORM, Connection Pooling |
| **Storage** | Cloudflare R2 (S3-compatible object storage) |
| **AI/LLM** | OpenAI (GPT-4o/4.1/5), Anthropic (Claude), xAI (Grok), Zhipu (GLM) |
| **Auth** | NextAuth.js v5, TOTP 2FA (RFC 6238), Wallet Connect, OAuth 2.0 |
| **Payments** | x402 Protocol (on-chain crypto payments) |
| **Security** | Cloudflare WAF + API Shield, Input Sanitization, Prompt Injection Defense |
| **Infrastructure** | Vercel (Edge + Serverless), Cloudflare (CDN + Security) |

---

## Multi-Chain Support

| Chain | SDK | Network | Capabilities |
|-------|-----|---------|--------------|
| **Cronos** | `ethers.js v6` | Cronos Mainnet | EVM transactions, CRC-20 tokens, DeFi protocols |
| **Ethereum** | `ethers.js v6` | Mainnet | ENS resolution, ERC-20/721/1155, Smart contracts |
| **Polygon** | `ethers.js v6` | PoS Mainnet | Low-cost transactions, NFT marketplaces |
| **Aptos** | `@aptos-labs/ts-sdk` | Mainnet | Move resources, coin balances, modules |
| **Flow** | `@onflow/fcl` | Mainnet | Cadence scripts, NFT collections |
| **SEI** | `@sei-js/core` | Pacific-1 | Cosmos SDK queries, IBC transfers |
| **Wormhole** | Custom | Multi-chain | Cross-chain message verification |

---

## AI Models

| Model | Provider | Context | Use Case |
|-------|----------|---------|----------|
| **GPT-4o** | OpenAI | 128K | Fast responses, general queries |
| **GPT-4.1** | OpenAI | 1M | Complex reasoning, multi-step tasks |
| **GPT-5** | OpenAI | - | Experimental, next-gen capabilities |
| **Claude Opus 4.5** | Anthropic | 200K | Deep analysis, thinking mode |
| **Grok 4.1** | xAI | - | Real-time data, non-reasoning |
| **GLM 4.6** | Zhipu AI | - | Multilingual, Chinese optimized |
| **Gemini 2.5 Flash** | Google | - | Image generation |

---

## Security

Barzakh AI implements a **4-layer defense architecture** to protect against AI-specific threats:

| Layer | Protection |
|-------|------------|
| **Input Sanitization** | Homoglyph detection, invisible char stripping, RTL removal, Unicode normalization |
| **Prompt Injection Defense** | Direct/indirect injection, jailbreak patterns, role manipulation prevention |
| **Media Protection** | Polyglot detection, EXIF stripping, steganography defense, magic byte validation |
| **Model Protection** | Sponge attack prevention, model extraction defense, output filtering |

> 🔒 For detailed security documentation, see [docs/SECURITY.md](docs/SECURITY.md)

---

## Key Features

- **🤖 AI Chat Interface** — Multi-model streaming responses with tool calling
- **📊 Wallet Analytics** — Portfolio tracking, transaction history, DeFi positions
- **🔍 On-Chain Search** — Query blockchain data with natural language
- **📰 News Aggregation** — Real-time crypto news and X/Twitter sentiment
- **💳 Crypto Payments** — x402 protocol for on-chain subscriptions
- **🔐 Enterprise Auth** — 2FA, wallet signatures, OAuth, session management
- **📱 PWA Support** — Installable mobile experience with offline capabilities

> ✨ For complete feature documentation, see [docs/FEATURES.md](docs/FEATURES.md)

---

## Demo

**🌐 Live Application**: [https://chat.barzakh.tech](https://chat.barzakh.tech)

> 📺 For demo video and screenshots, see [docs/DEMO.md](docs/DEMO.md)

---

## Hackathon Submission

This project is submitted to the **Cronos Hackathon** on DoraHacks.

### Track
**AI Track** — AI-powered blockchain analytics platform

### Innovations
1. **Multi-Model AI Orchestration** — Seamlessly switch between 6 LLM providers
2. **45+ Blockchain Tools** — Comprehensive toolkit for on-chain analysis
3. **x402 Crypto Payments** — Native blockchain payment protocol
4. **4-Layer AI Security** — Defense against prompt injection, polyglot attacks, and more

---

## Documentation

| Document | Description |
|----------|-------------|
| [ARCHITECTURE.md](docs/ARCHITECTURE.md) | System design, data flows, sequence diagrams |
| [API.md](docs/API.md) | REST API reference, endpoint documentation |
| [FEATURES.md](docs/FEATURES.md) | Complete feature breakdown |
| [SECURITY.md](docs/SECURITY.md) | Security architecture, threat protection |
| [DEMO.md](docs/DEMO.md) | Demo video, screenshots, sample queries |

---

## Contact

- **Website**: [barzakh.tech](https://barzakh.tech)
- **Live App**: [chat.barzakh.tech](https://chat.barzakh.tech)
- **Email**: support@barzakh.tech

---

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

<p align="center">
  <strong>Built with ❤️ by <a href="https://github.com/sirath-network">Sirath Network</a> </strong>
</p>
