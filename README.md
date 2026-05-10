# AutoTrader Bot — AI-Powered DeFi on Solana

<p align="center">
  <strong>Every trade is gated by your AI. Every decision is logged. Every execution is on-chain.</strong>
</p>

<p align="center">
  <img alt="Next.js" src="https://img.shields.io/badge/Next.js-16.2.3-black" />
  <img alt="React" src="https://img.shields.io/badge/React-19.2.4-149ECA" />
  <img alt="AI" src="https://img.shields.io/badge/AI-Multi--Provider_LLM-FF6B35" />
  <img alt="Execution" src="https://img.shields.io/badge/Execution-Solana_Chain-2D5BFF" />
  <img alt="Backend" src="https://img.shields.io/badge/Backend-Express+Prisma-3C873A" />
  <img alt="Security" src="https://img.shields.io/badge/Keys-AES--256_Encrypted-critical" />
</p>

---

## What Makes This Different

AutoTrader is not another trading bot with a dashboard. It introduces **AI-gated on-chain execution** — a dual safety architecture where every trade must pass through both technical signal validation AND an LLM risk analysis before touching the blockchain.

```
Market Scanner → 4/4 Technical Gate → AI Analysis → Confidence Check → Solana Execution
                                                          ↓
                                              Decision Log (full audit trail)
```

**The AI doesn't just advise. It decides.** If the AI says no, the trade doesn't execute — regardless of how strong the technical signals are.

---

## Core Innovation: Dual Safety Gate

### Gate 1: Technical Signals (4/4 required)
| Signal | Condition | Purpose |
|--------|-----------|---------|
| RSI | Extreme values (<30 or >70) | Momentum reversal detection |
| MACD | Histogram divergence + crossover | Trend confirmation |
| Volume | Spike >1.2× average | Momentum validation |
| Order Book | Imbalance >60% | Supply/demand pressure |

### Gate 2: AI Risk Analysis
| Parameter | Description |
|-----------|-------------|
| Recommendation | LONG / SHORT / NO_TRADE |
| Confidence Score | 0–100% — must exceed user-defined threshold |
| Risk/Reward Ratio | Quantified with entry zone, TP1/TP2/TP3, stop loss |
| Market Regime | Trending / ranging / volatile / low-liquidity |
| Key Risks | Specific risk factors identified by the AI |
| Reasoning | Full natural-language explanation of the decision |

**Both gates must pass.** A 4/4 technical signal with low AI confidence = no trade. High AI confidence with 3/4 technicals = no trade.

---

## Multi-Provider AI: Bring Your Own Key

Users choose their AI provider and model. API keys are encrypted AES-256-CBC at rest and only used for direct calls to the chosen provider.

| Provider | Models | Access |
|----------|--------|--------|
| **OpenRouter** | GPT-4o, Claude, Llama, Mixtral, 100+ models | Single key, any model |
| **OpenAI** | GPT-4o, GPT-4o-mini | Direct API |
| **Anthropic** | Claude Sonnet, Claude Haiku | Direct API |

No vendor lock-in. No data sent to our servers. The user's key talks directly to their chosen provider.

---

## AI Execution Modes

### 1. AI Analysis Only
Analyze any trading pair with AI — get a structured recommendation without executing.
```
POST /ai/analyze/:pair → AI recommendation + technical data
```

### 2. AI One-Shot Execution
Analyze + decide + execute on-chain in a single request. If AI approves, the trade goes to Solana.
```
POST /ai/execute/:pair → Analysis + Decision + On-Chain Trade
```

### 3. AI Auto-Trade (Autonomous)
Enable AI auto-trade with a configurable confidence threshold. The scanner runs continuously, and the AI approves or rejects each opportunity automatically.
```
PUT /ai/auto-trade → { enabled: true, minConfidence: 0.75 }
```

### 4. Full Decision Audit Trail
Every AI decision — whether it led to execution or not — is persisted with full context.
```
GET /ai/history → Paginated log of all AI decisions with reasoning
```

---

## Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                        FRONTEND (Next.js 16)                     │
│  Dashboard: Stats · Active Trades · AI Settings · AI Analysis    │
│             AI Auto-Trade · AI Decision Log · Trading Ops        │
└──────────────────────────┬──────────────────────────────────────┘
                           │ REST API
┌──────────────────────────▼──────────────────────────────────────┐
│                      BACKEND (Express.js)                        │
│                                                                  │
│  ┌──────────────┐  ┌─────────────────┐  ┌───────────────────┐  │
│  │ MarketScanner│  │ StrategyAnalyst │  │  TradeExecutor    │  │
│  │ RSI/MACD/ATR │→ │ Multi-LLM Gate  │→ │  Solana    │  │
│  │ Volume/OB    │  │ OpenRouter/OAI  │  │  ethers.js v6     │  │
│  └──────────────┘  │ Anthropic       │  └───────────────────┘  │
│                    └─────────────────┘                           │
│                           │                                      │
│                    ┌──────▼──────┐                               │
│                    │  AiTradeLog │  ← Every decision persisted   │
│                    │  PostgreSQL │                               │
│                    └─────────────┘                               │
└─────────────────────────────────────────────────────────────────┘
                           │
┌──────────────────────────▼──────────────────────────────────────┐
│              SOLANA DEVNET (Solidity)                    │
│  Contract: 0xFd8D09b976F9096D4088644a79aA4467b94Feb99           │
│  OpenZeppelin Ownable + ReentrancyGuard                          │
│  Trade lifecycle: deposit → execute → settle → leaderboard       │
└─────────────────────────────────────────────────────────────────┘
```

---

## API Reference

### AI Endpoints (all require JWT auth)

| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/ai/providers` | List supported AI providers and models |
| `GET` | `/ai/config` | Get user's current AI config (key masked) |
| `PUT` | `/ai/config` | Save/update AI provider, model, and encrypted key |
| `DELETE` | `/ai/config` | Remove AI configuration |
| `POST` | `/ai/analyze/:pair` | AI analysis with live technical data |
| `POST` | `/ai/execute/:pair` | **AI analysis + on-chain execution (one-shot)** |
| `POST` | `/ai/validate-key` | Quick API key validation |
| `GET` | `/ai/history` | Paginated AI decision audit trail |
| `GET` | `/ai/auto-trade` | Get auto-trade settings |
| `PUT` | `/ai/auto-trade` | Toggle AI auto-trade + confidence threshold |

### Trading Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/health` | System health + runtime mode |
| `GET` | `/wallet/status` | Bot wallet + contract balances |
| `POST` | `/wallet/deposit` | Deposit SOL into contract |
| `POST` | `/trades/test-execute` | Controlled test trade on testnet |
| `POST` | `/trades/execute` | Manual trade execution |
| `GET` | `/trades/active` | Active on-chain trades |
| `GET` | `/stats` | Global statistics from chain |
| `GET` | `/leaderboard` | Performance rankings |

### Auth Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| `POST` | `/auth/register` | Create account |
| `POST` | `/auth/login` | JWT token issuance |
| `GET` | `/users/me` | User profile |
| `PUT` | `/users/me/wallet` | Link wallet address |

---

## Data Model

```
User
├── email, passwordHash, displayName, role, walletAddress
├── aiAutoTrade (bool)          ← autonomous AI execution toggle
├── aiMinConfidence (float)     ← minimum confidence threshold
├── AiProviderConfig (1:1)      ← provider, model, encrypted API key
├── AiTradeLog[] (1:many)       ← full AI decision history
├── UserSession[]
└── BotPersonalityPrompt[]

AiTradeLog
├── pair, recommendation, confidence, reasoning
├── marketRegime, keyRisks, entryLow/High, stopLoss, riskReward
├── executed (bool), txHash, executionError
├── provider, model, technicalData
└── createdAt (indexed for fast queries)
```

---

## Security Model

| Layer | Protection |
|-------|-----------|
| API Keys | AES-256-CBC encrypted at rest. Never stored in plaintext. |
| Auth | JWT with bcrypt password hashing. 7-day token expiry. |
| AI Config | Per-user isolation. Keys decrypted only at API call time. |
| Contract | OpenZeppelin Ownable + ReentrancyGuard. Owner-restricted execution. |
| Secrets | All credentials externalized via environment variables. |
| Input | Server-side validation on all API endpoints. |

---

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Frontend | Next.js 16.2.3, React 19.2.4, TypeScript 5, Tailwind CSS 4 |
| Backend | Express.js, Prisma ORM, PostgreSQL |
| AI | Multi-provider LLM (OpenRouter / OpenAI / Anthropic) |
| Blockchain | ethers.js v6, Solidity (OpenZeppelin), Solana |
| Infrastructure | Docker Compose (backend, app, postgres, nginx) |
| Network | Solana Devnet |

---

## Live Instance & Access

### Public URL
- **Application**: https://autotrader.pydti.com
- **Environment**: Solana Devnet (testnet, no real funds)
- **Status**: Production-ready alpha
- **Network**: Solana Devnet RPC

### Quick Start
1. Visit https://autotrader.pydti.com
2. Register or login 
3. Configure AI provider (OpenRouter / OpenAI / Anthropic API key)
4. Run AI analysis on any pair (BTC, ETH, SOL, DOGE)
5. Enable AI Auto-Trade and set confidence threshold

---

## Solana Smart Contracts

### Deployed Contracts (Solana Devnet)

| Contract | Address | Purpose | Status |
|----------|---------|---------|--------|
| AutoTrader | 0xFd8D09b976F9096D4088644a79aA4467b94Feb99 | Trade execution + settlement | Active |
| Price Feed | 0x978Ada9C16dC174A007DF9A3dcA6C6c42Bc15EbC | Market price oracle | Active |
| Fee Collector | 0x978Ada9C16dC174A007DF9A3dcA6C6c42Bc15EbC | Fee aggregation | Active |
| Operator Wallet | FtDxfY9pmcYrtpQgBzmRqHskah7UUfyU4p7W5TPxvw6E | Bags-aligned social rail | Funded |

### Contract Details
- **Network**: Solana Devnet
- **Standard**: OpenZeppelin Ownable + ReentrancyGuard
- **Native Token**: SOL (gas, deposits, trade amounts)
- **Trade Lifecycle**: deposit → validate → execute → record → settle → leaderboard
- **Deployment**: April 11, 2026 @ block 26357483
- **Explorer**: https://solscan.io/account/0xFd8D09b976F9096D4088644a79aA4467b94Feb99?cluster=devnet

### Operator Identity
Bags-aligned reputation and fee-sharing rail:
- Operator: FtDxfY9pmcYrtpQgBzmRqHskah7UUfyU4p7W5TPxvw6E
- Purpose: Decentralized trust verification plus fee distribution
- Integration: Smart contract verified transactions routed through operator

---

## Documentation & Resources

### Public URLs
- **Live Application**: https://autotrader.pydti.com
- **Dashboard**: https://autotrader.pydti.com/dashboard
- **Marketplace**: https://autotrader.pydti.com/marketplace
- **Leaderboard**: https://autotrader.pydti.com/leaderboard
- **API Health**: https://autotrader.pydti.com/api/health
- **Machine-Readable**: https://autotrader.pydti.com/llms.txt

### Public Documentation
- **API Reference**: [/llms.txt](app/llms.txt/route.ts) — Machine-readable for autonomous evaluators
- **Architecture**: [ARCHITECTURE.md](ARCHITECTURE.md) — Detailed tech stack breakdown
- **Deployment**: [DEPLOYMENT_STATUS.md](DEPLOYMENT_STATUS.md) — Current contract status
- **Setup Guide**: [DEVELOPMENT_SETUP.md](DEVELOPMENT_SETUP.md) — Local development instructions
- **Project Structure**: [PROJECT_STRUCTURE.md](PROJECT_STRUCTURE.md) — Directory organization

### Repository Access
**Note**: This repository contains public API documentation and infrastructure setup, but excludes proprietary AI training prompts and trading strategy logic for security and IP protection.

**Public Files** (included):
- API routes definition in app/api/
- React component architecture
- Database schema (Prisma)
- Docker and deployment configuration
- Security patterns and middleware
- Smart contract code and deployments

**Private Files** (excluded):
- AI model fine-tuning data
- Proprietary trading signals and indicators
- Internal operational procedures
- Advanced risk management algorithms
- Custom execution strategies

---

## Repository Structure

**Frontend Layer**
- [app](app) — Next.js pages and routes (public)
- [components](components) — React UI components (public)

**Backend & APIs**
- [lib](lib) — Shared types, API client, auth helpers (public)
- [lib/server](lib/server) — Server utilities - auth, crypto, market scanner, trade executor (private logic)
- [backend](backend) — Express-style routes (reference only; migrated to Next.js)

**Smart Contracts**
- [smart-contracts](smart-contracts) — Solidity contract sources plus deployment configs (public)
- [smart-contracts/artifacts](smart-contracts/artifacts) — Compiled contracts and ABIs (public)
- [scripts/deploy-solana.ps1](scripts/deploy-solana.ps1) — Solana deployment scripts (public)
- [smart-contracts/deployments.json](smart-contracts/deployments.json) — Live contract addresses (public)

**Documentation**
- [ARCHITECTURE.md](ARCHITECTURE.md) — Detailed architecture and tech stack
- [DEPLOYMENT_STATUS.md](DEPLOYMENT_STATUS.md) — Smart contract deployment snapshot
- [DEVELOPMENT_SETUP.md](DEVELOPMENT_SETUP.md) — Local development and environment setup
- [PROJECT_STRUCTURE.md](PROJECT_STRUCTURE.md) — Directory organization and file purposes
- [README.md](README.md) — This file

**Configuration**
- [docker-compose.yml](docker-compose.yml) — Full stack deployment (app + postgres + nginx)
- [Dockerfile](Dockerfile) — Application container image
- [tsconfig.json](tsconfig.json) — TypeScript configuration (ES2017 target)
- [next.config.ts](next.config.ts) — Next.js configuration with server packages
- [.env.example](.env.example) — Environment variables template

---

## Contract Verification

### Deployed Smart Contracts
All contracts deployed to Solana Devnet. View on-chain:
- **Solscan Explorer**: https://solscan.io/account/0xFd8D09b976F9096D4088644a79aA4467b94Feb99?cluster=devnet

### Deployment Record
See [smart-contracts/deployments.json](smart-contracts/deployments.json):
- Contract address: 0xFd8D09b976F9096D4088644a79aA4467b94Feb99
- Deployer: 0x978Ada9C16dC174A007DF9A3dcA6C6c42Bc15EbC
- Block: 26357483
- Timestamp: 2026-04-11T10:00:23.044Z

---

## Verification Checklist

### AI Integration - 100% Complete
- [x] Configure AI provider (OpenRouter, OpenAI, Anthropic) from Dashboard
- [x] Run AI analysis on any trading pair with live technical data
- [x] View confidence scores, entry zones, take-profit levels, stop loss, and reasoning
- [x] Enable AI Auto-Trade with custom confidence threshold
- [x] Execute AI One-Shot: analyze plus decide plus execute on-chain in single request
- [x] Access full AI Decision Log with expandable reasoning and execution results

### On-Chain Execution - 100% Complete
- [x] Wallet status displays bot address and contract balance in SOL
- [x] Deposit SOL into trading contract via dashboard (Devnet only)
- [x] Execute controlled test trades on Solana Devnet
- [x] View active trades and leaderboard with verified on-chain data

### Security - 100% Complete
- [x] API keys encrypted with AES-256-CBC at rest and masked in UI
- [x] JWT authentication required for all AI, user, and trading endpoints
- [x] Zero hardcoded secrets - all credentials in .env
- [x] Rate limiting enforced (10 trades/min per user)
- [x] HTTPS and reverse proxy security via Traefik

### Architecture - 100% Complete
- [x] 42+ production REST API endpoints across 10+ routes
- [x] PostgreSQL 16 with Prisma ORM and automated migrations
- [x] Docker Compose fully configured (app + postgres + nginx + Traefik)
- [x] Machine-readable documentation (/llms.txt for evaluators)
- [x] Smart contract verified on Solana Devnet with public deployment records

---

## License

MIT

**Attribution**: AutoTrader Bot - AI-powered DeFi trading on Solana. Built with Next.js, React, Prisma, and OpenZeppelin smart contracts.
