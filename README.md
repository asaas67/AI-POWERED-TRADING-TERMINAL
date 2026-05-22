<![CDATA[<div align="center">

# 🏛️ Alpha Suite V2 — AI Trade Terminal

### _Institutional-Grade Algorithmic Trading Platform_

**Live market decisions • Signal fusion • AI-powered execution review**

[![Rust](https://img.shields.io/badge/Rust-1.80+-f74c00?logo=rust&logoColor=white)](https://www.rust-lang.org/)
[![Next.js](https://img.shields.io/badge/Next.js-16-black?logo=next.js&logoColor=white)](https://nextjs.org/)
[![Tauri](https://img.shields.io/badge/Tauri-2.11-24c8db?logo=tauri&logoColor=white)](https://tauri.app/)
[![Kafka](https://img.shields.io/badge/Redpanda-Kafka--Compatible-e4272c?logo=apachekafka&logoColor=white)](https://redpanda.com/)
[![QuestDB](https://img.shields.io/badge/QuestDB-Time--Series-d14671?logo=questdb&logoColor=white)](https://questdb.io/)
[![Docker](https://img.shields.io/badge/Docker-24+-2496ED?logo=docker&logoColor=white)](https://www.docker.com/)

---

A high-performance, event-driven trading terminal built on a **Rust microservice backbone** with a **Next.js/Tauri desktop client**. Ingests live market data from Zerodha Kite Connect, processes it through a multi-agent AI pipeline (Technical, Sentiment, Predictive, Quant-RAG), and delivers institutional-quality trade decisions with sub-second latency.

</div>

---

## 📸 Screenshots

<div align="center">

| Deep Quant Analysis (Investor) | Macro Intelligence (Investor) |
|:---:|:---:|
| ![Deep Quant Analysis view showing AI conviction scoring, setup validation, and execution plan](docs/screenshots/deep_quant.png) | ![Macro Intelligence view showing portfolio metrics, index indicators, and Quant-RAG outlook](docs/screenshots/macro_intel.png) |

</div>

---

## ✨ Key Features

- **🔴 Live Market Data** — Direct Zerodha Kite Connect WebSocket integration with proprietary binary protocol decoding (184-byte Full mode packets)
- **📊 Multi-Agent AI Pipeline** — Four specialized agents (Technical, Sentiment, Predictive, Quant-RAG) process signals in parallel via Kafka
- **🤖 AI-Powered Insights** — DeepSeek v4 Pro (via NVIDIA NIM) generates real-time anomaly analysis and market intelligence
- **📈 Predictive Ghost Line** — Linear regression ML model projects future price targets with R² confidence scoring
- **🖥️ Native Desktop App** — Tauri 2 wraps the Next.js frontend for zero-latency IPC data streaming
- **⚡ Zero-Latency Rendering** — Charts bypass React state entirely; lightweight-charts updates are pushed directly from WebSocket handlers
- **🔐 Institutional Charting** — Dark-mode canvas with EMA 9/21 ribbons, volume histograms, and crosshair overlays
- **🔄 Multi-Profile Workspace** — Hot-swappable layouts for Intraday (Scalp), Swing (1H-4H), and Investor (Macro) trading styles
- **📰 AI News Sentiment** — LLM-powered news analysis with per-article sentiment scoring and aggregate Fear/Greed gauge
- **🗄️ 5-Year Historical Archive** — QuestDB time-series storage with year-partitioned daily OHLCV candles and binary IPC transfer

---

## 🏗️ Architecture

```
┌──────────────────────────────────────────────────────────────────────────────┐
│                          ALPHA SUITE V2 — DATA FLOW                         │
├──────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│  Zerodha Kite WS (binary)                                                    │
│       │                                                                      │
│       ▼                                                                      │
│  ┌──────────┐     ┌─────────────┐     ┌─────────────────┐                    │
│  │ Ingestion │────▶│    Kafka    │────▶│  Alpha Terminal  │──▶ WS :8081       │
│  │  (Rust)   │     │  (Redpanda) │     │   (OHLC Engine)  │   (Chart Data)   │
│  └──────────┘     └──────┬──────┘     └────────┬────────┘                    │
│       │                  │                     │                             │
│       ▼                  │          market.ohlc.10m                           │
│    QuestDB              │                     │                             │
│  (Time-Series)           │          ┌─────────┴─────────┐                    │
│                          │          ▼                   ▼                    │
│                          │   ┌──────────────┐   ┌──────────────┐             │
│                          │   │  Predictive   │   │  Quant-RAG   │             │
│                          │   │  Agent (Rust) │   │  Agent (Rust) │            │
│                          │   │  LinReg ML    │   │  DeepSeek AI  │            │
│                          │   └──────┬───────┘   └──────┬───────┘             │
│                          │          │                   │                    │
│                          │       WS :8082            WS :8083                │
│                          │     (Ghost Line)        (AI Insights)             │
│                          │          │                   │                    │
│              ┌───────────┴──────┐   │                   │                    │
│              ▼                  ▼   ▼                   ▼                    │
│       ┌──────────┐     ┌──────────┐                                          │
│       │Technical │     │Sentiment │        ┌───────────────────────┐         │
│       │  Agent   │     │  Agent   │        │    Tauri Desktop UI   │         │
│       │  (Rust)  │     │ (Node.js)│        │  (Next.js + IPC Bridge)│        │
│       └────┬─────┘     └────┬─────┘        │                       │         │
│            │                │              │  • AlphaPredictiveChart│         │
│            ▼                ▼              │  • Order Book DOM      │         │
│       ┌─────────────────────────┐          │  • Swing Confluence    │         │
│       │     Aggregator (Rust)   │──────────│  • Macro Sentiment     │         │
│       │   Decision Fusion Engine│ WS :8080 │  • System Console      │         │
│       └─────────────────────────┘          └───────────────────────┘         │
│                                                                              │
└──────────────────────────────────────────────────────────────────────────────┘
```

---

## 📁 Monorepo Structure

```
Ai-trader/
├── ingestion/              # Kite WS binary parser → Kafka + QuestDB (Rust)
├── alpha-terminal/         # OHLC tumbling window engine → WS :8081 (Rust)
├── aggregator/             # Decision fusion engine → WS :8080 (Rust)
├── agents/
│   ├── technical/          # RSI, VWAP, candlestick patterns → signals (Rust)
│   ├── sentiment/          # News NLP + LLM sentiment scoring (Node.js)
│   ├── predictive/         # Linear regression ML → Ghost Line WS :8082 (Rust)
│   └── quant-rag/          # DeepSeek v4 Pro anomaly analysis → WS :8083 (Rust)
├── frontend/               # Next.js 16 + Tauri 2 desktop client
│   ├── src/                # React components, Zustand stores, layouts
│   └── src-tauri/          # Rust native shell, IPC bridge, historical loader
├── auth/                   # Identity vault — Argon2id, JWT, MFA-ready (Node.js/Fastify)
├── backend/                # Shared backend utilities
├── shared_protos/          # Universal Protobuf data contracts
├── tools/
│   └── load_tester/        # Chaos Engine — high-frequency stress tester (Rust)
├── scripts/
│   ├── powershell/         # Windows startup/shutdown scripts
│   └── linux/              # Linux startup scripts
├── docker-compose.yml      # Full infrastructure orchestration
├── .env.example            # Environment variable template
└── ARCHITECTURE.md         # Detailed system design documentation
```

---

## ⚙️ Tech Stack

| Layer | Technology | Purpose |
|---|---|---|
| **Ingestion** | Rust (tokio, rdkafka) | Binary WebSocket parsing, Kafka production |
| **Agents** | Rust + Node.js | Technical analysis, sentiment NLP, predictive ML |
| **Aggregator** | Rust (tokio) | Multi-signal fusion, consensus scoring |
| **LLM Backend** | DeepSeek v4 Pro (NVIDIA NIM) | AI market insights, anomaly analysis |
| **Message Broker** | Redpanda (Kafka-compatible) | Event streaming between all services |
| **Time-Series DB** | QuestDB | OHLCV archival, year-partitioned storage |
| **Auth DB** | PostgreSQL 16 | User identity, sessions, MFA |
| **Session Cache** | Redis 7 | JWT refresh tokens, rate limiting |
| **Frontend** | Next.js 16 + React 19 | Server-rendered UI with Zustand state |
| **Desktop Shell** | Tauri 2 | Native window, IPC bridge, secure vault |
| **Charting** | lightweight-charts 5 | WebGL candlestick rendering |
| **Auth** | Fastify + Argon2id + JWT (RS256) | Password hashing, asymmetric token signing |
| **Serialization** | Protobuf + bincode | Inter-service contracts + binary IPC |

---

## 📡 Kafka Topic Map

| Topic | Producer | Consumer(s) |
|---|---|---|
| `market.ticks` | Ingestion (Kite WS) | Alpha Terminal, Technical Agent |
| `market.ohlc.10m` | Alpha Terminal | Predictive Agent, Quant-RAG Agent |
| `technical_signals` | Technical Agent | Aggregator |
| `sentiment_signals` | Sentiment Agent | Aggregator |
| `signals.predictive` | Predictive Agent | Frontend (WS :8082) |
| `signals.insights` | Quant-RAG Agent | Frontend (WS :8083) |
| `aggregated_decisions` | Aggregator | Frontend (WS :8080) |

---

## 🚀 Quick Start

### Prerequisites

| Tool | Minimum Version | Installation |
|---|---|---|
| **Docker Engine** | 24.0+ | [docker.com](https://docs.docker.com/get-docker/) |
| **Docker Compose** | v2.20+ | Included with Docker Desktop |
| **Node.js** | 20 LTS+ | [nodejs.org](https://nodejs.org/) |
| **Rust** | 1.80+ | [rustup.rs](https://rustup.rs/) |
| **CMake** | 3.20+ | `winget install Kitware.CMake` |

### 1. Clone & Configure

```bash
git clone https://github.com/your-org/Ai-trader.git
cd Ai-trader

# Copy and fill in your API keys
cp .env.example .env
```

Edit `.env` with your credentials:

```env
# Zerodha Kite Connect (required for live data)
KITE_API_KEY=your_kite_api_key
KITE_API_SECRET=your_kite_api_secret
KITE_ACCESS_TOKEN=your_daily_token          # Expires daily at midnight IST

# Instrument tokens — comma-separated token:SYMBOL pairs
KITE_INSTRUMENT_TOKENS=738561:RELIANCE,260105:BANKNIFTY,256265:NIFTY 50

# LLM Configuration (any OpenAI-compatible endpoint)
LLM_API_URL=https://router.huggingface.co/v1/chat/completions
LLM_API_KEY=your_api_key
LLM_MODEL=deepseek-ai/DeepSeek-V3-0324

# Auth — generate once, never rotate
AUTH_PEPPER=$(openssl rand -hex 32)
```

### 2. Start Everything (One Command)

**Windows (PowerShell):**
```powershell
.\scripts\powershell\start_system.ps1
```

**Linux / macOS:**
```bash
chmod +x ignition.sh
./ignition.sh
```

This single script:
1. Cleans up stale processes on all required ports
2. Starts Docker infrastructure (Redpanda, QuestDB, PostgreSQL, Redis)
3. Waits for each service to be healthy
4. Pre-creates all Kafka topics
5. Boots services in dependency order: Ingestion → Technical → Sentiment → Aggregator → Predictive → Quant-RAG → Frontend
6. Opens the Tauri desktop window

> **💡 Tip:** Press `Ctrl+C` to gracefully shut down all services and Docker containers.

### 3. Manual Setup (Advanced)

If you prefer to start services individually:

```bash
# Step 1: Infrastructure
docker-compose up -d redpanda questdb postgres redis

# Step 2: Backend services (each in a separate terminal)
cd ingestion && cargo run --release
cd agents/technical && cargo run --release
cd agents/sentiment && npm start
cd aggregator && cargo run --release
cd agents/predictive && cargo run --release
cd agents/quant-rag && cargo run --release

# Step 3: Frontend
cd frontend && npm install && npm run tauri:dev
```

---

## 🖥️ Trading Profiles

The terminal supports three workspace modes, hot-swappable via the profile switcher:

### Intraday (Scalp)
- High-frequency 1m/5m candlestick charts
- Level-2 Order Book DOM with bid/ask depth
- Volatility heatmaps and momentum oscillators
- 9-column chart + 3-column order book grid layout

### Swing (1H-4H)
- Medium-term candlestick analysis with trend overlays
- **Swing Confluence Panel** with multi-timeframe trend bias (1H/4H/1D/1W)
- **AI News Sentiment** feed with per-article scoring and Fear/Greed gauge
- Powered by DeepSeek AI via the Quant-RAG agent

### Investor (Macro)
- Daily/Weekly timeframe charting
- **Macro Indicators** — Nifty 50, Bank Nifty, India VIX, sectoral indices
- **Portfolio Risk Metrics** — Total Return, Max Drawdown, Win Rate, Avg Conviction
- **Quant-RAG Outlook** — AI-generated sectoral analysis with probability-weighted scenarios

---

## 📊 AI Agents

### Technical Agent (`agents/technical/` — Rust)
Computes real-time technical indicators from raw tick data:
- **RSI** (Relative Strength Index) with warm-up gating
- **VWAP** (Volume-Weighted Average Price) with distance calculations
- **Candlestick Patterns** — Doji, Hammer, Engulfing, Shooting Star
- **Cross Detection** — Golden Cross, Death Cross, ORB breakouts
- **Consensus Engine** — Aggregates all indicators into a conviction score (0-100)

### Sentiment Agent (`agents/sentiment/` — Node.js)
NLP-powered news analysis pipeline:
- Fetches real-time headlines from NewsData.io and Google News RSS
- Analyzes sentiment via LLM (any OpenAI-compatible endpoint)
- Publishes per-symbol sentiment scores to Kafka
- Configurable provider: HuggingFace, OpenAI, Groq, or local Ollama

### Predictive Agent (`agents/predictive/` — Rust)
Forward-looking price projection engine:
- **Algorithm**: Least-Squares Linear Regression on 14-period rolling window of 10-minute closes
- **Confidence**: R² (Coefficient of Determination) mapped to 1-100 scale
- **Output**: `PredictiveSignal` with `predicted_close_price` and `confidence_score`
- **Visualization**: Rendered as a dashed "Ghost Line" on the chart canvas (10m timeframe only)

### Quant-RAG Agent (`agents/quant-rag/` — Rust)
Serverless AI insights powered by DeepSeek v4 Pro:
- **Anomaly Trigger**: Monitors `market.ohlc.10m` for ≥2% absolute price swings
- **LLM Pipeline**: Anomaly → DeepSeek v4 Pro → structured JSON (`headline`, `analysis_text`, `sentiment_score`)
- **Error Visibility**: API failures are surfaced to the frontend as system-level anomalies — never silent
- **WebSocket**: Broadcasts `MarketInsight` JSON on port 8083

---

## 🔌 WebSocket Ports

| Port | Service | Data |
|---|---|---|
| `8080` | Aggregator | Aggregated trade decisions (BUY/SELL/HOLD + conviction) |
| `8081` | Alpha Terminal | Live 10-minute OHLC candles |
| `8082` | Predictive Agent | Ghost Line projections (predicted close + confidence) |
| `8083` | Quant-RAG Agent | AI market insights (headline + analysis + sentiment) |

---

## 📋 Data Contracts (Protobuf)

All inter-service communication uses Protobuf schemas defined in [`shared_protos/`](shared_protos/):

| Contract | File | Key Fields |
|---|---|---|
| **Tick** | `market_data.proto` | `symbol`, `last_traded_price`, `volume`, `best_bid`, `best_ask` |
| **OHLCCandle** | `market_data.proto` | `symbol`, `open`, `high`, `low`, `close`, `volume` |
| **TechSignal** | `technical_data.proto` | `rsi_value`, `vwap_distance`, `technical_conviction_score` |
| **NewsSentiment** | `sentiment_data.proto` | `headline`, `claude_conviction_score`, `reasoning_snippet` |
| **AggregatedDecision** | `decision.proto` | `final_conviction_score`, `action_type` (BUY/SELL/HOLD) |
| **PredictiveSignal** | `predictive_data.proto` | `predicted_close_price`, `confidence_score`, `model_version` |
| **MarketInsight** | `insight_data.proto` | `headline`, `analysis_text`, `sentiment_score` |

---

## 🧪 Testing

### Rust Unit Tests (Agents + Tauri)
```bash
npm run test:rust
# or directly:
cd frontend/src-tauri && cargo test -- --nocapture
```

Covers:
- Candlestick pattern detection (Doji, Hammer, Engulfing, Shooting Star)
- RSI warm-up gating and VWAP calculations
- Consensus engine serialization and bias derivation
- Prediction engine window management and confidence scoring
- Signal evaluation logic (bullish/bearish/neutral)

### End-to-End Tests (Playwright)
```bash
npm run test:e2e
# or:
cd frontend && npx cross-env ALPHA_TEST_MODE=1 npx playwright test
```

### Chaos Engine (Load Testing)
The `/tools/load_tester` stress-tests all pipelines under institutional-grade load:

```bash
cd tools/load_tester
cargo run -- --rate 100 --anomaly-every 500 --symbol RELIANCE --anomaly-pct 5.0
```

- **100 synthetic candles/sec** to `market.ohlc.10m` using geometric random walk pricing
- **Flash crash injection** every 500th candle (±5% spike) to trigger DeepSeek LLM
- **Full pipeline coverage**: Predictive (LinReg), Quant-RAG (LLM), Alpha Terminal (OHLC WS), Tauri (60 FPS canvas)

---

## 🐳 Docker Services

```bash
# Start all infrastructure + backend services
docker-compose up -d --build

# Check health
docker-compose ps

# Tail specific service logs
docker-compose logs -f ingestion
docker-compose logs -f quant-rag-agent

# Verify Kafka topics
docker exec -it alphasuite-redpanda rpk topic list

# Stop everything and remove volumes
docker-compose down -v
```

| Container | Service | Port(s) |
|---|---|---|
| `alphasuite-redpanda` | Kafka-compatible broker | `19092` (external), `29092` (internal) |
| `alphasuite-questdb` | Time-series database | `9000` (web), `8812` (PG), `9009` (ILP) |
| `alphasuite-postgres` | Auth database | `5890` |
| `alphasuite-redis` | Session cache | `6379` |
| `alphasuite-ingestion` | Kite WS → Kafka + QuestDB | — |
| `alphasuite-alpha-terminal` | OHLC engine | `8081` |
| `alphasuite-aggregator` | Decision fusion | `8080` |
| `alphasuite-predictive` | Ghost Line ML | `8082` |
| `alphasuite-quant-rag` | DeepSeek AI insights | `8083` |

---

## 🔧 Troubleshooting

<details>
<summary><strong>Kafka Connection Refused</strong></summary>

```
ERROR: Alpha OHLC WS connection error → ws://127.0.0.1:8081
```
**Cause:** Backend services haven't started yet.  
**Fix:** Wait for `docker-compose ps` to show all services as `Up`. Services auto-reconnect every 3 seconds.
</details>

<details>
<summary><strong>KITE_ACCESS_TOKEN Expired</strong></summary>

```
ERROR: Kite WS auth failed — 403 Forbidden
```
**Fix:** Regenerate the access token via the Kite Connect login flow and update `.env`. Tokens expire daily at midnight IST.
</details>

<details>
<summary><strong>DeepSeek API Timeout</strong></summary>

```
ERROR: DeepSeek API failure: NVIDIA NIM request timeout
```
**Fix:** Verify your `LLM_API_KEY` is valid and check network connectivity to the configured `LLM_API_URL`.
</details>

<details>
<summary><strong>CMake Not Found (Local Dev)</strong></summary>

```
error: failed to run custom build command for `rdkafka-sys`
```
**Fix:** Install CMake:
- **Windows:** `winget install Kitware.CMake`
- **macOS:** `brew install cmake`
- **Linux:** `apt install cmake`
</details>

<details>
<summary><strong>Port Conflicts</strong></summary>

If services fail to start due to port conflicts, the `start_system.ps1` script automatically cleans up stale processes on ports `3000, 8080-8083, 9000, 9009, 5432, 6379, 19092`. For manual cleanup:

```powershell
# Windows
netstat -ano | findstr :8081
taskkill /PID <pid> /F

# Linux/macOS
lsof -i :8081
kill -9 <pid>
```
</details>

---

## 🔒 Security

- **Password Hashing**: Argon2id with configurable pepper (never rotate `AUTH_PEPPER`)
- **JWT Signing**: RS256 asymmetric keys (Ed25519 private/public PEM pair)
- **Secure Storage**: Tauri Stronghold plugin for client-side API key vault
- **Environment Isolation**: All secrets in `.env` (gitignored), never hardcoded
- **MFA-Ready**: TOTP (Time-Based One-Time Password) infrastructure via `otplib`

---

## 📖 Documentation

| Document | Description |
|---|---|
| [ARCHITECTURE.md](ARCHITECTURE.md) | Full system design, data flow, and phase documentation |
| [PRODUCTION_SETUP.md](PRODUCTION_SETUP.md) | Step-by-step production deployment guide |
| [CONTRACTS.md](CONTRACTS.md) | Protobuf data contract reference |
| [.env.example](.env.example) | Environment variable template with inline documentation |

---

## 🗺️ Roadmap

- [ ] Multi-timeframe OHLC generation (1m, 5m, 15m, 1H, 1D candles from backend)
- [ ] Live order execution via Kite Connect Orders API
- [ ] Portfolio position tracking and P&L analytics
- [ ] Multi-exchange support (BSE, crypto exchanges)
- [ ] Cloud deployment with Kubernetes orchestration
- [ ] Mobile companion app (React Native)

---

## 📄 License

This project is private and proprietary. All rights reserved.

---

<div align="center">

**Built with 🦀 Rust, ⚡ Kafka, and 🧠 DeepSeek AI**

*Alpha Suite V2 — Where quant meets intuition.*

</div>
]]>
