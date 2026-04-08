# Hyperliquid Trading Agent — Smart Money Skill

> **Compatible with:** Claude.ai (Web) · Claude Desktop (MCP) · Claude Code · Cursor · Windsurf · Manus · GPT Custom Actions · OpenClaw · Gemini

An autonomous Hyperliquid trading agent skill powered by smart money signals from **500+ whale wallets**. Create, backtest, and deploy AI trading agents on **Hyperliquid** (perps, spot, HIP-3 stocks) and **Polymarket** (prediction markets). Real-time composite AI signals (Smart Money + Technical Analysis + Derivatives), multi-timeframe analysis, and a self-learning execution engine — all from your AI assistant.

## Features

- **Smart Money Signals** — Track top traders (>75% win-rate) across Polymarket predictions and HyperLiquid perpetuals
- **AI Dashboard** — Composite signals combining SM (40%), TA (35%), and Market data (25%) across perp, spot, HIP-3, and prediction markets
- **Technical Analysis** — Multi-timeframe TA indicators (RSI, MACD, SuperTrend, Bollinger Bands, etc.) via TAAPI.io
- **Derivatives Data** — Open interest, funding rates, long/short ratios, liquidation maps via CoinGlass
- **Trading Agents** — Create, configure, deploy, and monitor automated Perp agents on HyperLiquid with customizable trigger conditions
- **Human-in-the-Loop (HITL)** — Agent creates trade plans for your approval via chat or Telegram
- **HIP-3 Trading** — Stocks (TSLA, NVDA), commodities (GOLD), indices on Hyperliquid DEXs
- **Backtesting** — Run historical simulations with streaming progress and performance reports
- **Telegram Notifications** — Instant trade plan alerts with one-tap approve/reject buttons
- **Wallet Tracking** — Look up any trader's profile, PnL, categories, and open positions

> **Note:** Polymarket data reading works. PM trading agents are not yet supported.

## Prerequisites

- Python 3
- A ZoneIn API key (starts with `zn_`)

## Installation

**Quick start (any platform):**
```bash
export ZONEIN_API_KEY="zn_your_key_here"
python3 scripts/zonein.py status
```

| Platform | How to install |
|:---|:---|
| **Claude.ai (Web)** | Settings → Connectors → Add Custom Connector → Name: `ZoneIn Trading`, URL: `https://mcp.zonein.xyz/mcp` |
| **Claude Desktop** | Add `zonein-mcp-server/mcp_server.py` to `claude_desktop_config.json` → restart |
| **Claude Code** | `cp -r zonein-skill .claude/skills/hyperliquid-trading-agent` + set `ZONEIN_API_KEY` |
| **Cursor / Windsurf** | Copy to `.cursor/skills/` or add MCP server in Settings → MCP |
| **Manus** | Skills → + Add → Import from GitHub → `https://github.com/zonein-workspace/zonein-skill` |
| **GPT Custom Actions** | GPT Builder → Actions → Import URL: `https://mcp.zonein.xyz/docs/openapi.json` → Auth: API Key, Header `X-API-Key` |
| **OpenClaw** | Gateway Dashboard → `/skills` → enable **hyperliquid-trading-agent** → paste API key |

➜ **Step-by-step with screenshots:** [Platform Setup Guide](references/PLATFORM_SETUP.md)

## Usage

All commands run through the bundled CLI script:

```bash
python3 scripts/zonein.py <command> [options]
```

### Commands Overview

| Category | Commands |
|----------|----------|
| **Polymarket** | `signals`, `leaderboard`, `consensus`, `trader`, `pm-top`, `trader-positions`, `trader-trades` |
| **Perp (HyperLiquid)** | `perp-signals`, `perp-traders`, `perp-top`, `perp-coins`, `perp-trader`, `perp-categories`, `perp-category-stats` |
| **AI Dashboard** | `dashboard`, `dashboard-latest`, `dashboard-asset`, `agent-signal` |
| **Technical Analysis** | `ta`, `ta-single` |
| **Derivatives** | `derivatives`, `fear-greed`, `derivatives-pairs`, `liquidation-map` |
| **Agent Management** | `agents`, `agent-get`, `agent-create`, `agent-update`, `agent-deploy`, `agent-enable`, `agent-disable`, `agent-pause`, `agent-delete` |
| **Agent Trading** | `agent-open`, `agent-close`, `agent-update-sl-tp`, `agent-fund`, `agent-withdraw`, `agent-positions`, `agent-balance`, `agent-orders` |
| **Agent Analytics** | `agent-overview`, `agent-stats`, `agent-performance`, `agent-trades`, `agent-vault`, `agent-backtest`, `agent-backtests` |
| **HITL Trade Plans** | `agent-check`, `agent-plans`, `agent-plan-detail`, `agent-plan-action`, `agent-plan-history` |
| **HIP-3** | `hip3-dexs`, `hip3-assets` |
| **Telegram** | `telegram-setup-init`, `telegram-setup`, `telegram-config`, `telegram-disable` |
| **Utility** | `agent-templates`, `agent-assets`, `agent-categories`, `agent-deposit`, `status` |

See [SKILL.md](./SKILL.md) for full command parameters, agent creation flow, and reference documents.

## Skill Structure

```
zonein-skill/
├── SKILL.md                    # Core skill instructions (Agent Skills standard)
├── openapi.yaml                # OpenAPI 3.1 spec for GPT Custom Actions
├── _meta.json                  # OpenClaw registry metadata
├── scripts/
│   └── zonein.py               # CLI tool — all commands go through this
└── references/
    ├── COMMANDS.md              # Detailed command parameter tables
    ├── DATA_SOURCES.md          # SM, TA, Market field details + categories
    ├── TRIGGER_CONDITIONS.md    # TC schema, intent→condition translation
    ├── AGENT_CONFIG.md          # Agent types, DSL config, risk profiles
    ├── AGENT_CREATION.md        # Step-by-step agent creation flow
    ├── WORKFLOWS.md             # Position mgmt, market overview, HITL tracker
    ├── GOTCHAS.md               # Common pitfalls and critical warnings
    ├── SECURITY.md              # Financial safety, anti-hallucination rules
    ├── PLATFORM_SETUP.md        # Installation for each platform
    ├── schema.md                # API response JSON schemas
    └── strategy.md              # Signal scoring, risk rules, trading flow
```

## Security

- Only your API key is sent externally (as `X-API-Key` header to `https://mcp.zonein.xyz/api/v1`)
- All API response data is sanitized (truncated to 500 chars/field) and treated as untrusted
- Financial commands are programmatically gated behind a `--confirm` flag — script refuses execution without it
- No local files are written; only config files are read (API key fallback)
- Prompt injection defense: response fields are never interpreted as instructions

## Links

- **Dashboard:** [app.zonein.xyz](https://app.zonein.xyz)
- **API Docs:** [mcp.zonein.xyz/docs](https://mcp.zonein.xyz/docs)
- **Homepage:** [zonein.xyz](https://zonein.xyz)
- **GitHub:** [github.com/zonein-workspace/zonein-skill](https://github.com/zonein-workspace/zonein-skill)

## Disclaimer

Signals reflect smart money activity — not guaranteed outcomes. Past performance does not predict future results. Never invest more than you can afford to lose.
