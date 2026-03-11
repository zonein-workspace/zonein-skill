# Zonein: Whale hunting for trading agents on Hyperliquid & Polymarket

An [OpenClaw](https://openclaw.io) skill that fetches live trading intelligence from **Polymarket** and **HyperLiquid** smart money wallets. Track top traders (>75% win-rate), follow whale movements, and create automated trading agents — all from your AI assistant.

## Features

- **Polymarket Signals** — Smart money trading signals, leaderboards, and consensus across politics, crypto, sports, and more
- **HyperLiquid Perp Signals** — Whale positions, long/short sentiment, and top performer rankings
- **Wallet Tracking** — Look up any trader's profile, PnL, and open positions
- **Trading Agents** — Create, configure, and manage automated Perp trading agents on HyperLiquid with human-in-the-loop approval (Prediction Market agents not yet supported)
- **Backtesting** — Run historical simulations against real smart money signals and OHLC data

## Prerequisites

- Python 3
- A Zonein API key (starts with `zn_`)

## Setup

1. Go to [app.zonein.xyz/pm](https://app.zonein.xyz/pm) and log in (referral code required)
2. Click **"Get API Key"** and copy your key
3. Set the key via one of:
   - **OpenClaw Gateway Dashboard** → Skills → Enable "zonein" → paste key
   - Environment variable: `export ZONEIN_API_KEY="zn_your_key_here"`
   - Config file: `~/.openclaw/openclaw.json` under `skills.entries.zonein.apiKey`

## Usage

All commands are run through the bundled Python script:

```bash
python3 scripts/zonein/scripts/zonein.py <command> [options]
```

### Key Commands

| Command | Description |
|---------|-------------|
| `signals` | Polymarket smart money signals |
| `perp-signals` | HyperLiquid perp signals |
| `leaderboard` | Top PM traders by PnL |
| `perp-top` | Top perp performers |
| `perp-coins` | Coin long/short sentiment |
| `trader <wallet>` | PM trader profile |
| `perp-trader <address>` | HyperLiquid trader profile |
| `agents` | List your trading agents |
| `agent-create` | Create a new trading agent |
| `agent-stats <id>` | Agent performance stats |
| `agent-backtest <id>` | Run historical backtest |
| `status` | Check API key status |

See [SKILL.md](./SKILL.md) for the full command reference with all parameters.

## Security

- Only your API key is sent externally (as `X-API-Key` header)
- All requests go to `https://mcp.zonein.xyz/api/v1` — no other endpoints
- Financial commands are gated behind a `--confirm` flag to prevent unintended execution
- No local files are written; only `~/.openclaw/openclaw.json` is read (for API key fallback)

## Links

- [Dashboard (Polymarket)](https://app.zonein.xyz/pm/)
- [Dashboard (Perp)](https://app.zonein.xyz/perp/)
- [API Docs](https://mcp.zonein.xyz/docs)
- [Zonein Homepage](https://zonein.xyz)

## Disclaimer

Signals reflect smart money activity — not guaranteed outcomes. Past performance does not predict future results. Never invest more than you can afford to lose.
