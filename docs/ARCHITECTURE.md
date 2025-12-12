# Architecture

### Barzakh AI System Architecture Documentation

## Table of Contents
- [System Overview](#system-overview)
- [Request Lifecycle](#request-lifecycle)
- [Authentication Architecture](#authentication-architecture)
- [AI Pipeline](#ai-pipeline)
- [Multi-Chain Integration](#multi-chain-integration)
- [x402 Payment Protocol](#x402-payment-protocol)
- [Security Layers](#security-layers)
- [Infrastructure](#infrastructure)

---

## System Overview

Barzakh AI is built on a modern, event-driven architecture optimized for real-time AI streaming and blockchain data processing.

### Layer Architecture

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

---

## Request Lifecycle

### Chat Request Flow

```mermaid
sequenceDiagram
    autonumber
    participant Client as 🖥️ Client (Browser)
    participant CF as 🛡️ Cloudflare Edge
    participant MW as ⚙️ Next.js Middleware
    participant Auth as 🔐 NextAuth.js
    participant API as 📡 API Route
    participant Orchestrator as 🧠 AI Orchestrator
    participant Sanitizer as 🛡️ Input Sanitizer
    participant LLM as 🤖 LLM Provider
    participant Tools as 🔧 Tool Executor
    participant RPC as ⛓️ Blockchain RPC
    participant DB as 💾 PostgreSQL

    Client->>CF: POST /api/chat (TLS 1.3)
    CF->>CF: 1. WAF Rules Check
    CF->>CF: 2. Bot Detection
    CF->>CF: 3. API Shield Schema Validation
    CF->>CF: 4. Rate Limit Check (Token Bucket)
    CF->>MW: Forward Request
    
    MW->>Auth: Validate Session Cookie
    Auth->>DB: SELECT session, user WHERE session_id = ?
    DB-->>Auth: User Context + Subscription Tier + Limits
    Auth-->>MW: Authenticated User Object
    
    MW->>MW: Check User Quota / Tier Limits
    MW->>API: Process Chat Request
    
    API->>Orchestrator: Initialize Stream Context
    Orchestrator->>Orchestrator: Load Chat History (last N messages)
    Orchestrator->>Orchestrator: Build Context Window
    
    Orchestrator->>Sanitizer: Sanitize User Input
    Sanitizer->>Sanitizer: 1. Unicode Normalization (NFC)
    Sanitizer->>Sanitizer: 2. Homoglyph Detection
    Sanitizer->>Sanitizer: 3. Invisible Char Removal
    Sanitizer->>Sanitizer: 4. RTL Override Strip
    Sanitizer->>Sanitizer: 5. Prompt Injection Scan
    Sanitizer-->>Orchestrator: Sanitized Input
    
    Orchestrator->>LLM: streamText({ model, messages, tools })
    
    loop Agentic Tool Loop (max 10 iterations)
        LLM-->>Orchestrator: Tool Call Request { name, args }
        Orchestrator->>Orchestrator: Validate Tool + Args
        Orchestrator->>Tools: Execute Tool
        
        alt Blockchain Tool
            Tools->>RPC: JSON-RPC / GraphQL Query
            RPC-->>Tools: On-Chain Data
        else Data Aggregator
            Tools->>Tools: External API Call (Zerion, DeFi Llama, etc.)
        end
        
        Tools-->>Orchestrator: Structured Result (JSON)
        Orchestrator->>LLM: Continue with Tool Result
    end
    
    LLM-->>Orchestrator: Final Response Tokens (streaming)
    Orchestrator-->>API: SSE Stream (text/event-stream)
    API-->>Client: Transfer-Encoding: chunked
    
    API->>DB: INSERT message (async, non-blocking)
    Note over DB: Persist user/assistant messages
```

---

## Authentication Architecture

### Multi-Factor Authentication Flow

```mermaid
flowchart TB
    subgraph Entry["🌐 Authentication Entry Points"]
        Email["fa:fa-envelope Email + Password"]
        Google["fa:fa-google Google OAuth 2.0"]
        Wallet["fa:fa-wallet Wallet Connect<br/>(EIP-4361 SIWE)"]
    end

    subgraph Verification["🔐 Multi-Factor Verification"]
        Password["Password Verification<br/>bcrypt (cost=12)"]
        TOTP["TOTP 2FA<br/>RFC 6238<br/>30-second window<br/>6-digit code"]
        EmailOTP["Email OTP<br/>6-digit code<br/>10-minute TTL<br/>Rate limited"]
        WalletSig["Wallet Signature<br/>EIP-191 personal_sign<br/>Nonce verification"]
    end

    subgraph Session["🎫 Session Management"]
        JWT["JWT Token<br/>HS256 algorithm<br/>HttpOnly cookie"]
        Cookie["Secure Cookie<br/>SameSite=Lax<br/>Secure=true<br/>Path=/"]
        Refresh["Session Lifecycle<br/>30-day max age<br/>Sliding window<br/>Auto-refresh"]
    end

    subgraph Operations["🛡️ Operation Categories"]
        Normal["Normal Operations<br/>Session Only"]
        Sensitive["Sensitive Operations<br/>Re-auth Required"]
    end

    subgraph SensitiveOps["⚠️ Re-authentication Required"]
        Delete["Account Deletion"]
        WalletBind["Wallet Bind/Unbind"]
        EmailChange["Email Change"]
        PasswordChange["Password Change"]
    end

    Email --> Password
    Password --> TOTP
    Password --> EmailOTP
    
    Google --> JWT
    
    Wallet --> WalletSig --> JWT
    
    TOTP --> JWT
    EmailOTP --> JWT
    
    JWT --> Cookie
    Cookie --> Refresh
    Refresh --> Normal
    Refresh --> Sensitive
    Sensitive --> SensitiveOps

    style Sensitive fill:#ff6b6b,color:#fff
    style SensitiveOps fill:#ff8787,color:#fff
```

### Wallet Authentication (EIP-4361 SIWE)

```mermaid
sequenceDiagram
    participant User as 👤 User
    participant App as 🖥️ Frontend
    participant Wallet as 🔐 Wallet (MetaMask)
    participant API as 📡 Backend
    participant DB as 💾 Database

    User->>App: Click "Connect Wallet"
    App->>Wallet: Request accounts
    Wallet->>User: Approve connection
    User->>Wallet: Confirm
    Wallet-->>App: Account address (0x...)
    
    App->>API: GET /api/auth/nonce?address=0x...
    API->>API: Generate cryptographic nonce
    API->>DB: Store nonce (5-min TTL)
    API-->>App: SIWE Message with nonce
    
    Note over App: Message format:<br/>"Sign in to Barzakh AI<br/>Nonce: abc123..."
    
    App->>Wallet: personal_sign(message)
    Wallet->>User: Approve signature
    User->>Wallet: Confirm
    Wallet-->>App: Signature (65 bytes, r+s+v)
    
    App->>API: POST /api/auth/signin { address, signature }
    API->>API: ecrecover(hash, signature)
    API->>API: Verify recovered address matches
    API->>DB: Verify nonce not expired/used
    API->>DB: Create/update user session
    API-->>App: Set-Cookie: session=...
```

---

## AI Pipeline

### Input Processing & Model Routing

```mermaid
flowchart LR
    subgraph Input["📥 Input Layer"]
        UserMsg["User Message"]
        History["Chat History<br/>(Context Window)"]
        SystemPrompt["System Prompt<br/>(58KB+ optimized)"]
        ToolDefs["Tool Definitions<br/>(45+ tools)"]
    end

    subgraph Sanitization["🛡️ Security Layer"]
        Homoglyph["Homoglyph<br/>Detection<br/>(Visual Lookalikes)"]
        Invisible["Invisible Char<br/>Removal<br/>(U+200B, U+FEFF)"]
        RTL["RTL/LTR Override<br/>Stripping<br/>(U+202A-U+202E)"]
        Unicode["Unicode<br/>Normalization<br/>(NFC/NFKC)"]
        Injection["Prompt Injection<br/>Pattern Scan"]
    end

    subgraph Router["🔀 Model Router"]
        Selector{"Model<br/>Selector"}
    end

    subgraph Models["🤖 LLM Providers"]
        GPT4o["<b>GPT-4o</b><br/>128K context<br/>Fast inference<br/>General purpose"]
        GPT41["<b>GPT-4.1</b><br/>1M context<br/>Deep reasoning<br/>Complex tasks"]
        GPT5["<b>GPT-5</b><br/>Next-gen<br/>Experimental"]
        Claude["<b>Claude Opus 4.5</b><br/>200K context<br/>Thinking mode<br/>Analysis"]
        Grok["<b>Grok 4.1</b><br/>Real-time data<br/>Non-reasoning<br/>Fast"]
        GLM["<b>GLM 4.6</b><br/>Multilingual<br/>Chinese optimized"]
    end

    subgraph Output["📤 Output Layer"]
        Stream["SSE Stream<br/>(Token-by-token)"]
        ToolCall["Tool Invocation<br/>(Function calling)"]
    end

    UserMsg --> Homoglyph
    History --> Unicode
    SystemPrompt --> Unicode
    ToolDefs --> Unicode
    
    Homoglyph --> Invisible --> RTL --> Unicode --> Injection
    Injection --> Selector
    
    Selector -->|"chat-model-small"| GPT4o
    Selector -->|"chat-model-large"| GPT41
    Selector -->|"chat-model-gigantic"| GPT5
    Selector -->|"chat-model-claude"| Claude
    Selector -->|"chat-model-grok"| Grok
    Selector -->|"chat-model-glm"| GLM
    
    GPT4o & GPT41 & GPT5 & Claude & Grok & GLM --> Stream
    GPT4o & GPT41 & GPT5 & Claude & Grok & GLM --> ToolCall
```

---

## Multi-Chain Integration

### Supported Chains

| Chain | SDK | Network | RPC Provider | Capabilities |
|-------|-----|---------|--------------|--------------|
| **Cronos** | `ethers.js v6` | Cronos Mainnet | Cronos RPC | EVM transactions, CRC-20, DeFi |
| **Ethereum** | `ethers.js v6` | Mainnet | Infura/Alchemy | ENS, ERC-20/721/1155, Uniswap |
| **Polygon** | `ethers.js v6` | PoS Mainnet | QuickNode | L2 transactions, NFTs |
| **Aptos** | `@aptos-labs/ts-sdk` | Mainnet | Aptos Fullnode | Move resources, coins |
| **Flow** | `@onflow/fcl` | Mainnet | Flow Access | Cadence scripts, NFTs |
| **SEI** | `@sei-js/core` | Pacific-1 | SEI RPC | Cosmos SDK, IBC |
| **Wormhole** | Custom | Multi-chain | Guardian Net | Cross-chain verification |

### Chain Adapter Pattern

```typescript
// Simplified chain adapter interface
interface ChainAdapter {
  getBalance(address: string): Promise<Balance>
  getTransactions(address: string, limit: number): Promise<Transaction[]>
  getTokenBalances(address: string): Promise<TokenBalance[]>
  getNFTs(address: string): Promise<NFT[]>
}

// Implementation per chain
class CronosAdapter implements ChainAdapter { /* ethers.js */ }
class AptosAdapter implements ChainAdapter { /* @aptos-labs/ts-sdk */ }
class FlowAdapter implements ChainAdapter { /* @onflow/fcl */ }
```

---

## x402 Payment Protocol

### Crypto Payment Flow

```mermaid
sequenceDiagram
    autonumber
    participant User as 👤 User
    participant Frontend as 🖥️ Frontend
    participant API as 📡 Backend API
    participant Wallet as 🔐 Wallet
    participant Chain as ⛓️ Blockchain

    User->>Frontend: Select Crypto Payment
    
    Note over Frontend,API: Step 1: Wallet Verification
    Frontend->>API: GET /api/billing/x402/verify-wallet?address=0x...
    API->>API: Generate nonce (UUID v4)
    API-->>Frontend: { message: "Sign to verify: nonce..." }
    
    Frontend->>Wallet: personal_sign(message)
    Wallet->>User: Approve signature
    User->>Wallet: Confirm
    Wallet-->>Frontend: Signature (0x...)
    
    Frontend->>API: POST /api/billing/x402/verify-wallet { address, signature }
    API->>API: ecrecover(messageHash, signature)
    API-->>Frontend: { success: true, walletAddress: "0x..." }
    
    Note over Frontend,API: Step 2: Payment Request
    Frontend->>API: POST /api/billing/x402/subscribe { planId: "pro" }
    API->>API: Calculate payment amount
    API-->>Frontend: HTTP 402 Payment Required<br/>{ amount, recipient, chainId, deadline }
    
    Note over Frontend,Chain: Step 3: On-Chain Transaction
    Frontend->>Wallet: eth_sendTransaction({ to, value })
    Wallet->>User: Approve transaction
    User->>Wallet: Confirm
    Wallet->>Chain: Submit transaction
    Chain-->>Wallet: Transaction hash (0x...)
    Wallet-->>Frontend: Transaction hash
    
    Note over Frontend,API: Step 4: Verification & Activation
    Frontend->>API: POST /api/billing/x402/verify { txHash }
    
    loop Poll for confirmation (max 30s)
        API->>Chain: eth_getTransactionReceipt(txHash)
        Chain-->>API: Receipt (or null)
    end
    
    API->>API: Validate: amount >= required
    API->>API: Validate: recipient matches
    API->>API: Validate: confirmations >= 1
    API->>API: Activate subscription
    API-->>Frontend: 200 OK { success: true }
```

---

## Security Layers

### 4-Layer Defense Architecture

```
┌──────────────────────────────────────────────────────────────────────────────────────┐
│                           AI SECURITY DEFENSE LAYERS                                 │
├───────────────────────────────────────────────────────────────────────────────────── ┤
│                                                                                      │
│  ┌──────────────────────────────────────────────────────────────────────────────┐    │
│  │  LAYER 1: INPUT SANITIZATION                                                 │    │
│  │  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐ ┌──────────────────────┐    │    │
│  │  │ Homoglyph   │ │ Invisible   │ │ RTL/LTR     │ │ Unicode              │    │    │
│  │  │ Detection   │ │ Char Strip  │ │ Override    │ │ Normalization        │    │    │
│  │  │ (Lookalikes)│ │ (U+200B,etc)│ │ Removal     │ │ (NFC/NFKC)           │    │    │
│  │  └─────────────┘ └─────────────┘ └─────────────┘ └──────────────────────┘    │    │
│  └──────────────────────────────────────────────────────────────────────────────┘    │
│                                       │                                              │
│                                       ▼                                              │
│  ┌──────────────────────────────────────────────────────────────────────────────┐    │
│  │  LAYER 2: PROMPT INJECTION DEFENSE                                           │    │
│  │  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐ ┌──────────────────────┐    │    │
│  │  │ Direct      │ │ Indirect    │ │ Jailbreak   │ │ Role/Context         │    │    │
│  │  │ Injection   │ │ Injection   │ │ Pattern     │ │ Manipulation         │    │    │
│  │  │ Detection   │ │ (via URLs)  │ │ Matching    │ │ Prevention           │    │    │
│  │  └─────────────┘ └─────────────┘ └─────────────┘ └──────────────────────┘    │    │
│  └──────────────────────────────────────────────────────────────────────────────┘    │
│                                       │                                              │
│                                       ▼                                              │
│  ┌──────────────────────────────────────────────────────────────────────────────┐    │
│  │  LAYER 3: MEDIA & FILE PROTECTION                                            │    │
│  │  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐ ┌──────────────────────┐    │    │
│  │  │ Polyglot    │ │ EXIF/Meta   │ │Steganography│ │ File Type            │    │    │
│  │  │ File        │ │ Data Strip  │ │ Detection   │ │ Validation           │    │    │
│  │  │ Detection   │ │             │ │             │ │ (Magic Bytes)        │    │    │
│  │  └─────────────┘ └─────────────┘ └─────────────┘ └──────────────────────┘    │    │
│  └──────────────────────────────────────────────────────────────────────────────┘    │
│                                       │                                              │
│                                       ▼                                              │
│  ┌──────────────────────────────────────────────────────────────────────────────┐    │
│  │  LAYER 4: MODEL PROTECTION                                                   │    │
│  │  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐ ┌──────────────────────┐    │    │
│  │  │ Sponge      │ │ Model       │ │ Model       │ │ Output               │    │    │
│  │  │ Attack      │ │ Extraction  │ │ Inversion   │ │ Filtering            │    │    │
│  │  │ Prevention  │ │ Defense     │ │ Guard       │ │ (PII, Secrets)       │    │    │
│  │  └─────────────┘ └─────────────┘ └─────────────┘ └──────────────────────┘    │    │
│  └──────────────────────────────────────────────────────────────────────────────┘    │
│                                       │                                              │
│                                       ▼                                              │
│  ┌──────────────────────────────────────────────────────────────────────────────┐    │
│  │  LAYER 5: RUNTIME MONITORING                                                 │    │
│  │  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐ ┌──────────────────────┐    │    │
│  │  │ Rate        │ │ Anomaly     │ │ Behavioral  │ │ Audit                │    │    │
│  │  │ Limiting    │ │ Detection   │ │ Analysis    │ │ Logging              │    │    │
│  │  │ (Token/IP)  │ │ (Pattern)   │ │ (Usage)     │ │ (Compliance)         │    │    │
│  │  └─────────────┘ └─────────────┘ └─────────────┘ └──────────────────────┘    │    │
│  └──────────────────────────────────────────────────────────────────────────────┘    │
│                                                                                      │
└──────────────────────────────────────────────────────────────────────────────────────┘
```

### Threat Protection Matrix

| Threat Category | Attack Vector | Defense Mechanism |
|-----------------|---------------|-------------------|
| **Prompt Injection** | Direct system prompt override | Pattern matching, input boundary enforcement |
| **Indirect Injection** | Malicious content via URLs/files | Content isolation, sandboxed parsing |
| **Jailbreak Attempts** | Role manipulation, DAN prompts | System prompt hardening, output monitoring |
| **Homoglyph Attacks** | Lookalike Unicode characters | Character normalization, visual similarity detection |
| **Invisible Characters** | Zero-width chars (U+200B, U+FEFF) | Whitespace stripping, control char removal |
| **RTL Override** | Bidirectional text manipulation | Unicode Bidi control removal |
| **Polyglot Files** | Images containing executable code | Magic byte validation, metadata stripping |
| **Sponge Attacks** | DoS via expensive computations | Token limits, complexity analysis, timeouts |
| **Model Extraction** | Query-based model stealing | Rate limiting, query pattern analysis |

---

## Infrastructure

### Deployment Topology

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                              CLOUDFLARE EDGE                                │
│  ┌────────────────────────────────────────────────────────────────────────┐ │
│  │  WAF │ DDoS Protection │ API Shield │ Rate Limiting │ Bot Management   │ │
│  └────────────────────────────────────────────────────────────────────────┘ │
│  ┌────────────────────────────────────────────────────────────────────────┐ │
│  │                         Cloudflare R2                                  │ │
│  │              (Object Storage - Images, Files, Attachments)             │ │
│  └────────────────────────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────────────────────┘
                                     │
                                     ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                              VERCEL PLATFORM                                │
│  ┌─────────────────────┐  ┌─────────────────────┐  ┌─────────────────────┐  │
│  │   Edge Functions    │  │  Serverless Fns     │  │    Static Assets    │  │
│  │   (Middleware)      │  │  (API Routes)       │  │    (CDN Cached)     │  │
│  │   < 1ms cold start  │  │  Node.js Runtime    │  │    Global Edge      │  │
│  └─────────────────────┘  └─────────────────────┘  └─────────────────────┘  │
└─────────────────────────────────────────────────────────────────────────────┘
                                     │
                    ┌────────────────┴────────────────┐
                    ▼                                 ▼
┌─────────────────────────────┐      ┌─────────────────────────────┐
│      POSTGRESQL (Neon)      │      │     EXTERNAL SERVICES       │
│  ┌───────────────────────┐  │      │  ┌───────────────────────┐  │
│  │   Connection Pooling  │  │      │  │   Zerion Portfolio    │  │
│  │   Drizzle ORM         │  │      │  │   DeFi Llama TVL      │  │
│  │   Prepared Statements │  │      │  │   CometAPI (LLM)      │  │
│  │   Automatic Backups   │  │      │  │   OpenAI / Anthropic  │  │
│  └───────────────────────┘  │      │  └───────────────────────┘  │
└─────────────────────────────┘      └─────────────────────────────┘
```

### Environment Configuration

| Environment | Domain | Branch | Purpose |
|-------------|--------|--------|---------|
| **Production** | chat.barzakh.tech | main | Live application |
| **API Production** | staging.barzakh.tech | Live application |
