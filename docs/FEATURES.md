# Features

> **Complete Feature Documentation for Barzakh AI**

## Table of Contents
- [AI Capabilities](#ai-capabilities)
- [Blockchain Tools](#blockchain-tools)
- [Authentication & Security](#authentication--security)
- [Payments](#payments)
- [User Experience](#user-experience)

---

## AI Capabilities

### Multi-Model Support

Barzakh AI integrates multiple state-of-the-art language models, allowing users to choose the best model for their task.

| Model | Provider | Context Window | Best For |
|-------|----------|----------------|----------|
| **GPT-4o** | OpenAI | 128K tokens | Fast responses, general queries, quick analysis |
| **GPT-4.1** | OpenAI | 1M tokens | Complex reasoning, multi-step tasks, long documents |
| **GPT-5** | OpenAI | Experimental | Next-generation capabilities, cutting-edge performance |
| **Claude Opus 4.5** | Anthropic | 200K tokens | Deep analysis, thinking mode, nuanced understanding |
| **Grok 4.1** | xAI | - | Real-time data analysis, fast non-reasoning tasks |
| **GLM 4.6** | Zhipu AI | - | Multilingual support, Chinese language optimization |

### Image Generation

| Model | Provider | Capabilities |
|-------|----------|--------------|
| **Gemini 2.5 Flash Image** | Google | High-fidelity image generation, fast inference |

### AI Features

- **Streaming Responses** — Token-by-token streaming for real-time output
- **Tool Calling** — AI can invoke 45+ tools to fetch real data
- **Context Memory** — Maintains conversation history within sessions
- **Multi-Turn Conversations** — Coherent dialogue across multiple exchanges
- **Function Calling** — Structured tool invocation with JSON schema validation

---

## Blockchain Tools

### Chain-Specific Tools (45+)

#### Cronos
| Tool | Description |
|------|-------------|
| `cronos_get_balance` | Get native CRO balance for address |
| `cronos_get_transactions` | Fetch transaction history |
| `cronos_get_token_balances` | List all CRC-20 token balances |
| `cronos_get_nfts` | Retrieve NFT collections owned |
| `cronos_defi_positions` | Get DeFi protocol positions |

#### EVM Chains (Ethereum, Polygon)
| Tool | Description |
|------|-------------|
| `evm_get_balance` | Get native token balance |
| `evm_get_transactions` | Transaction history with pagination |
| `evm_get_token_balances` | ERC-20 token holdings |
| `evm_get_nfts` | ERC-721/1155 NFT inventory |
| `evm_ens_resolve` | Resolve ENS names to addresses |
| `evm_translate_transaction` | Human-readable transaction explanation |

#### Aptos
| Tool | Description |
|------|-------------|
| `aptos_get_account` | Account resources and modules |
| `aptos_get_balance` | APT and coin balances |
| `aptos_get_transactions` | Transaction history |
| `aptos_get_nfts` | NFT collections (Digital Assets) |

#### Flow
| Tool | Description |
|------|-------------|
| `flow_get_account` | Account information |
| `flow_get_balance` | FLOW token balance |
| `flow_get_nfts` | NFT collections (TopShot, etc.) |
| `flow_execute_script` | Run Cadence scripts |

#### SEI
| Tool | Description |
|------|-------------|
| `sei_get_balance` | SEI token balance |
| `sei_get_delegations` | Staking delegations |
| `sei_get_transactions` | Transaction history |

#### Wormhole (Cross-Chain)
| Tool | Description |
|------|-------------|
| `wormhole_verify_message` | Verify cross-chain message |
| `wormhole_track_transfer` | Track cross-chain asset transfer |

### Data Aggregation Tools

#### DeFi Llama
| Tool | Description |
|------|-------------|
| `defillama_get_protocols` | List all DeFi protocols |
| `defillama_get_tvl` | Total Value Locked by protocol |
| `defillama_get_yields` | Yield farming opportunities |
| `defillama_get_stablecoins` | Stablecoin market data |

#### Zerion
| Tool | Description |
|------|-------------|
| `zerion_get_portfolio` | Complete portfolio analysis |
| `zerion_get_positions` | DeFi positions across protocols |
| `zerion_get_history` | Historical transactions |

### Search & Research Tools

| Tool | Description |
|------|-------------|
| `web_search` | Search the web using Tavily |
| `news_search` | Crypto news aggregation |
| `x_search` | Twitter/X sentiment search |
| `scrape_site` | Extract content from URLs |

### Image Tools

| Tool | Description |
|------|-------------|
| `create_image` | Generate images from text prompts |

---

## Authentication & Security

### Authentication Methods

| Method | Description | Security |
|--------|-------------|----------|
| **Email/Password** | Traditional credentials | bcrypt (12 rounds) |
| **Google OAuth** | Single sign-on | OAuth 2.0 |
| **Wallet Connect** | Sign-in with Ethereum | EIP-4361 SIWE |

### Two-Factor Authentication

| Type | Standard | Window |
|------|----------|--------|
| **TOTP** | RFC 6238 | 30-second |
| **Email OTP** | 6-digit code | 10-minute TTL |

### Session Security

- **JWT Tokens** — HS256 signed, HttpOnly cookies
- **Session Duration** — 30-day max, sliding window
- **Secure Cookies** — SameSite=Lax, Secure=true, Path=/

### Sensitive Operations (Re-auth Required)

These operations require password + second factor:
- Account deletion
- Wallet binding/unbinding
- Email change
- Password change

---

## Payments

### x402 Crypto Payment Protocol

Native on-chain payments for subscriptions without credit cards.

#### Payment Flow
1. **Wallet Verification** — Sign message to prove ownership
2. **Payment Request** — Receive amount and recipient address
3. **On-Chain Transaction** — Send crypto via wallet
4. **Verification** — Backend confirms on-chain
5. **Activation** — Subscription activated instantly

#### Features
- No credit card required
- Instant activation on confirmation
- Transparent on-chain payments
- Multi-chain support

---

## User Experience

### Chat Interface

| Feature | Description |
|---------|-------------|
| **Streaming Output** | Real-time token-by-token response display |
| **Syntax Highlighting** | Code blocks with language detection |
| **Markdown Rendering** | Rich text formatting in responses |
| **Message Editing** | Edit previous messages and regenerate |
| **Copy/Share** | Easy sharing of AI responses |
| **Chat History** | Searchable conversation history |

### Model Selection

- Switch between 6 LLM providers mid-conversation
- Visual model indicator in chat header
- Model-specific capabilities displayed

### File Attachments

| Type | Supported |
|------|-----------|
| Images | PNG, JPG, JPEG, GIF, WebP |
| Documents | PDF, TXT, MD |
| Max Size | 10MB per file |

### Progressive Web App (PWA)

| Feature | Description |
|---------|-------------|
| **Installable** | Add to home screen on mobile |
| **Responsive** | Optimized for all screen sizes |
| **Offline Support** | Service worker caching |
| **Push Notifications** | (Planned) Real-time alerts |

### Themes

| Theme | Description |
|-------|-------------|
| **Light** | Clean, professional appearance |
| **Dark** | Reduced eye strain, developer-friendly |
| **System** | Auto-detect OS preference |

### Accessibility

- Keyboard navigation
- Screen reader support
- High contrast mode
- Reduced motion support

---

## Subscription Tiers

### Free Tier
- **Daily Messages**: 10 messages per day
- **AI Models**: All 6 models (GPT-4o, GPT-4.1, GPT-5, Claude, Grok, GLM)
- **Blockchain Tools**: Full access to 45+ tools
- **Chat History**: Persistent
- **File Attachments**: Supported

### Pro Tier

| Billing Cycle | Daily Messages | Price |
|---------------|----------------|-------|
| **Monthly** | 50 messages/day | - |
| **Quarterly** | 100 messages/day | - |
| **Yearly** | 150 messages/day | - |

**Includes:**
- All 6 AI models
- Image generation (Gemini 2.5 Flash)
- Full blockchain tools access
- Unlimited chat history

### Ultimate Tier

| Billing Cycle | Daily Messages | Price |
|---------------|----------------|-------|
| **Monthly** | 250 messages/day | - |
| **Quarterly** | 350 messages/day | - |
| **Yearly** | 500 messages/day | - |

**Includes:**
- Everything in Pro
- Priority tool execution
- Priority support
- Early access to new features

### Payment

Currently supported:
- **TCRO** (Testnet Cronos) via x402 protocol

---

## Upcoming Features

- [ ] Custom AI agents with saved prompts
- [ ] Portfolio alerts and notifications
- [ ] Social trading signals
- [ ] Team collaboration workspaces
- [ ] API access for developers
- [ ] Webhook integrations
