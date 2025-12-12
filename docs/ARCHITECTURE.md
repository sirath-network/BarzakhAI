# Architecture Overview

## System Architecture

Barzakh AI is built on a modern, scalable architecture designed for real-time blockchain analytics and AI-powered insights.

```mermaid
graph TB
    subgraph Client["Client Layer"]
        direction LR
        Web["Web Application<br/>(Next.js 15 / React 18)"]
        PWA["Progressive Web App"]
    end

    subgraph Gateway["API Gateway Layer"]
        direction LR
        CF["Cloudflare<br/>API Shield"]
        Auth["Authentication<br/>Service"]
        RateLimit["Rate Limiter"]
    end

    subgraph Core["Core Services"]
        direction TB
        ChatEngine["Chat Engine"]
        AIOrchestrator["AI Orchestrator"]
        ToolExecutor["Tool Executor"]
        StreamProcessor["Stream Processor"]
    end

    subgraph AI["AI Layer"]
        direction LR
        subgraph Models["Multi-Model Support"]
            GPT["OpenAI<br/>GPT-4o/4.1"]
            Claude["Claude<br/>Opus"]
            Grok["Grok<br/>4.1"]
            GLM["GLM<br/>4.6"]
        end
        Prompts["Prompt<br/>Engineering"]
    end

    subgraph Tools["Blockchain Tools (45+)"]
        direction TB
        subgraph Chain["Chain-Specific"]
            CronosTool["Cronos Tools"]
            EVMTools["EVM Tools"]
            AptosTools["Aptos Tools"]
            SolanaTools["Solana Tools"]
            FlowTools["Flow Tools"]
        end
        subgraph Utility["Utility Tools"]
            WebSearch["Web Search"]
            NewsSearch["News Search"]
            ImageGen["Image Generation"]
            DeFiLlama["DeFi Llama"]
        end
    end

    subgraph Data["Data Layer"]
        DB[(PostgreSQL)]
        Cache[(Redis Cache)]
        FileStore["File Storage"]
    end

    subgraph External["External Services"]
        Zerion["Zerion API"]
        Stripe["Stripe"]
        X402["x402 Payments"]
    end

    Client --> Gateway
    Gateway --> Core
    Core --> AI
    Core --> Tools
    Core --> Data
    Tools --> External
```

---

## Component Details

### 🖥️ Client Layer

| Component | Technology | Purpose |
|-----------|------------|---------|
| Web App | Next.js 15, React 18 | Main user interface |
| PWA | Service Workers | Mobile-first experience |
| UI Library | Tailwind CSS, Radix UI | Consistent design system |
| State | React Context, Zustand | Client state management |

### 🔐 API Gateway

| Component | Technology | Purpose |
|-----------|------------|---------|
| CDN/WAF | Cloudflare | Edge protection |
| API Shield | OpenAPI Schema | Request validation |
| Auth | NextAuth.js | Session management |
| Rate Limiting | Custom middleware | Abuse prevention |

### 🧠 AI Layer

```mermaid
sequenceDiagram
    participant User
    participant Chat as Chat Engine
    participant AI as AI Orchestrator
    participant Tools as Tool Executor
    participant Chain as Blockchain

    User->>Chat: Send message
    Chat->>AI: Process with model
    AI->>AI: Select appropriate model
    
    loop Tool Execution
        AI->>Tools: Call blockchain tool
        Tools->>Chain: Query on-chain data
        Chain-->>Tools: Return data
        Tools-->>AI: Tool result
    end
    
    AI-->>Chat: Generate response
    Chat-->>User: Stream response
```

### ⛓️ Multi-Chain Support

| Chain | SDK | Capabilities |
|-------|-----|--------------|
| **Cronos** | ethers.js | Full EVM compatibility |
| **Ethereum** | ethers.js | Smart contract interaction |
| **Aptos** | @aptos-labs/ts-sdk | Move-based queries |
| **Solana** | @solana/web3.js | SPL token support |
| **Flow** | @onflow/fcl | Cadence integration |
| **SEI** | sei-js | Cosmos SDK chain |

---

## Data Flow

### Chat Flow

```mermaid
flowchart LR
    A[User Input] --> B{Auth Check}
    B -->|Valid| C[Rate Limit Check]
    B -->|Invalid| X[401 Error]
    C -->|OK| D[AI Processing]
    C -->|Exceeded| Y[429 Error]
    D --> E{Needs Tools?}
    E -->|Yes| F[Execute Tools]
    E -->|No| G[Generate Response]
    F --> G
    G --> H[Stream to Client]
```

### Payment Flow (x402 Protocol)

```mermaid
sequenceDiagram
    participant User
    participant Frontend
    participant Backend
    participant Wallet
    participant Blockchain

    User->>Frontend: Select crypto payment
    Frontend->>Backend: Request payment details
    Backend-->>Frontend: Return x402 payment info
    Frontend->>Wallet: Request signature
    Wallet-->>Frontend: Signed transaction
    Frontend->>Blockchain: Submit transaction
    Blockchain-->>Frontend: Transaction hash
    Frontend->>Backend: Verify transaction
    Backend->>Blockchain: Confirm on-chain
    Backend-->>Frontend: Subscription activated
```

---

## Security Architecture

```mermaid
graph TB
    subgraph Protection["Security Layers"]
        L1["Layer 1: Edge<br/>Cloudflare WAF"]
        L2["Layer 2: API<br/>Schema Validation"]
        L3["Layer 3: Auth<br/>Session + 2FA"]
        L4["Layer 4: AI<br/>Prompt Sanitization"]
    end

    subgraph Threats["Protected Against"]
        T1["DDoS Attacks"]
        T2["API Abuse"]
        T3["Unauthorized Access"]
        T4["Prompt Injection"]
    end

    L1 -.-> T1
    L2 -.-> T2
    L3 -.-> T3
    L4 -.-> T4
```

---

## Scalability

| Component | Strategy |
|-----------|----------|
| **Frontend** | Vercel Edge Network |
| **API** | Serverless Functions |
| **Database** | Connection Pooling |
| **AI** | Multiple Provider Fallback |
| **Blockchain** | RPC Load Balancing |

---

## Environment

| Environment | URL | Purpose |
|-------------|-----|---------|
| Production | chat.barzakh.tech | Live application |
| Staging | staging.barzakh.tech | Pre-production testing |
| Development | localhost:3000 | Local development |
