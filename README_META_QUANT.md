# 🧠 Meta-Quant — AI-Augmented Quantitative Trading System

**Multi-broker, multi-market prediction & execution engine**

[![Python](https://img.shields.io/badge/Python-3.13-blue)](https://python.org)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.x-blue)](https://typescriptlang.org)
[![License](https://img.shields.io/badge/License-MIT-green)](LICENSE)

---

## 🏆 Overview

Meta-Quant is an **autonomous quantitative trading ecosystem** combining LLM-powered prediction models with real-time market execution. The system spans traditional forex (FP Markets/cTrader), cryptocurrency prediction markets (Polymarket/Polygon), and centralized exchanges (Bitget), unified under a single OpenClaw workflow orchestrator.

### Key capabilities

- **FIFA World Cup 2026 Prediction** — Real-time market odds vs ML model comparison with automated edge detection
- **Polymarket CLOB Integration** — Full REST client for America's largest prediction market ($2.12B volume on World Cup 2026 markets alone)
- **cTrader cBot Bridge** — Automated trade execution via cTrader's cAlgo platform (.NET 6)
- **Multi-asset coverage** — Sports, crypto, politics, and forex prediction markets

---

## 🏗️ Architecture

```
┌─────────────────────────────────────────────┐
│              OpenClaw Orchestrator           │
│  ┌──────────┐ ┌──────────┐ ┌──────────────┐ │
│  │ Workflow  │ │ Agents   │ │ Checkpoints   │ │
│  │ Engine    │ │ Pipeline │ │ (Redis/File)  │ │
│  └──────────┘ └──────────┘ └──────────────┘ │
└────────────────────┬────────────────────────┘
                     │
         ┌───────────┼───────────┐
         ▼           ▼           ▼
┌────────────┐ ┌────────┐ ┌──────────────┐
│ Polymarket │ │cTrader │ │   Dashboard   │
│ CLOB API   │ │ cBot   │ │  (React/WS)   │
│  (Python)  │ │ (.NET) │ │  Port 3000    │
└────────────┘ └────────┘ └──────────────┘
         │           │
         ▼           ▼
┌────────────┐ ┌────────────┐
│ Polygon    │ │ FP Markets │
│ USDC/MATIC │ │  Forex/DMA │
└────────────┘ └────────────┘
```

---

## 🧩 Components

### 1. Polymarket Connector (`polymarket/`)

Full-featured Python client for Polymarket's CLOB and Gamma APIs:

```python
from polymarket.connector import PolymarketConnector

c = PolymarketConnector()

# Get live World Cup 2026 odds (all 48 teams)
odds = c.get_world_cup_winner_odds()
# → [{"team": "Spain", "probability": 0.17, "volume": 40_452_020}, ...]

# Place authenticated orders (after wallet setup)
c.place_order(token_id=..., price=0.17, size=10, side="BUY")
```

**Features:**
- ✅ REST client for CLOB API (markets, orderbook, orders)
- ✅ Gamma API integration (event-level data, 48 team odds)
- ✅ Wallet management (Polygon USDC: generate, sign, balance check)
- ✅ Trading engine with edge detection
- ✅ OpenAI SDK compatible endpoint support

### 2. cTrader cBot (`robots/MetaQuantSignalBot.cs`)

C# cBot bridging cTrader to the Meta-Quant backend:

- **Signal polling** — HTTP GET from dashboard API (`:3001`)
- **Auto-auth** — JWT login with automatic re-authentication
- **Trade execution** — `ExecuteMarketOrder()` with normalized symbols
- **Safety** — Dry-run mode by default (`AutoExecute = false`)

### 3. OpenClaw Workflows (`workflows/`)

Automated TypeScript workflows running 3×/day:

| Workflow | Frequency | Pipeline |
|----------|-----------|----------|
| `polymarket_trading.ts` | 3×/day (08h, 14h, 20h Paris) | Fetch odds → Evaluate edge → Execute |
| `daily_trading.ts` | 2×/day (10h, 16h Paris) | Market data → Signal gen → Paper trade |
| `infra-health-check` | Every 5 min | Redis, Prometheus, Grafana, Dashboard |

**Safety:**
- 🛑 Kill switch (Redis + file)
- 📝 Paper trading default
- 💾 Redis checkpoints before every critical step
- 🔐 JWT auth on all external API calls

### 4. Infrastructure

| Service | Role | Port |
|---------|------|------|
| Redis 7.4 | Cache, checkpoints, pub/sub | 6379 |
| QuestDB | Time-series market data | — |
| Prometheus + Grafana | Metrics & monitoring | 9090 / 3003 |
| React Dashboard | UI for trading signals & system health | 3000 |
| Express API | Backend for cBot & dashboard | 3001 |

---

## 🚀 Quick Start

### Prerequisites
```bash
python3 -m venv venv && source venv/bin/activate
pip install web3 eth-account requests python-dotenv
```

### Setup Wallet
```bash
python3 scripts/polymarket_setup.py --new-wallet
```

### Run Trading Workflow
```bash
openclaw run workflow /data/.openclaw/workflows/polymarket_trading.ts
```

---

## 📊 Live Data — FIFA World Cup 2026

As of June 2026, the system tracks **48 national teams** on Polymarket with **$2.12B total volume**:

| Team | Probability | Volume |
|------|-----------|--------|
| 🇪🇸 Spain | 17.0% | $40.4M |
| 🇫🇷 France | 16.1% | $46.8M |
| 🇵🇹 Portugal | 10.8% | $42.6M |
| 🏴󠁧󠁢󠁥󠁮󠁧󠁿 England | 10.4% | $35.0M |
| 🇦🇷 Argentina | 8.6% | $37.7M |
| 🇧🇷 Brazil | 8.5% | $36.6M |
| 🇩🇪 Germany | 5.1% | $39.8M |
| ... | | |

---

## 🔒 Security

- **No direct LLM-to-broker execution** — signals require human validation or explicit flag
- **Paper trading by default** — real mode behind `POLY_AUTO_TRADE=true`
- **All API keys in `.env`** — never committed
- **Redis checkpoints** — full recovery after crash
- **Kill switch** — emergency stop via Redis or file

---

## 📈 Roadmap

- [x] Polymarket CLOB connector
- [x] cTrader cBot bridge
- [x] OpenClaw automated workflows
- [x] World Cup 2026 live odds pipeline
- [ ] ML prediction model (XGBoost + ensemble)
- [ ] Real-money trading activation
- [ ] Telegram alerts & dashboard
- [ ] AlphaEvolve optimizer integration

---

## 🛠️ Tech Stack

`Python 3.13` · `TypeScript 5.x` · `C# (.NET 6)` · `Web3.py` · `Redis` · `QuestDB` · `Prometheus` · `Grafana` · `React` · `Docker` · `OpenClaw` · `Polygon` · `cTrader/cAlgo`

---

*Built by [Sidijohn](https://github.com/sidijohn) — Quantitative Analyst & Systems Developer*