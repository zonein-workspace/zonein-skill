# Agent Creation Flow

Currently supports **Perp Trading** on Hyperliquid (including HIP-3 assets like `xyz:TSLA`). PM agents not yet supported.

**⚠️ CRITICAL Rules:**
1. **NEVER** present `--type` or agent type names to the user. Ask about trading style in plain language, infer type internally. Read [Agent Config](AGENT_CONFIG.md) for the type mapping guide.
2. **ONE command creates everything.** Server auto-fills `trigger_conditions`, `prompt_config`, `trading_risk` if not provided.
3. **ALWAYS ask Q7 (withdrawal address)** before creating.

## Step 1: Collect Configuration

**Q1: Which coins?** → `--assets`
Options: BTC, ETH, SOL, HYPE. HIP-3: `dex:COIN` (e.g. `xyz:TSLA,xyz:NVDA,xyz:GOLD`).

**Q2: Trading style?** → AI infers `--type`
Ask naturally: "How do you like to trade?" Examples: follow whales, quick scalps, patient swings, high accuracy only, ride momentum. Map internally using [Agent Config Reference](AGENT_CONFIG.md).

**Q3: Risk profile?** → `--leverage`, `--risk-per-trade`, `--max-daily-loss`
- Conservative: 3x, SL 3%/TP 9%, max 3 positions, 1% daily loss
- Moderate: 5x, SL 5%/TP 10%, max 5 positions, 3% daily loss
- Aggressive: 10x, SL 5%/TP 7.5%, max 8 positions, 5% daily loss

**Q4: Trading strategy?** → `--trigger-conditions` + `--prompt-config`

**MUST show 3 random strategy examples before asking.** Without examples, users give vague answers. Examples:
- *"Enter LONG when SM long_ratio ≥50% with ≥3 wallets AND SuperTrend 'buy' on 4h AND ADX ≥15. Exit when SM flips AND SuperTrend=sell."*
- *"Quick entries when SM wallet_count ≥5. RSI 35-65. TP 1.5%, SL 0.8%."*
- *"SHORT when funding ≥0.04% AND RSI 4h ≥72 AND SM short_ratio ≥50%. LONG when funding ≤-0.03% AND RSI ≤28."*

See [Workflows Reference](WORKFLOWS.md) for all 13 strategy examples.

Ask: *"Describe your strategy in 2-3 sentences with specific metrics. What triggers entry? What triggers exit?"*

**If user provides details →** build `trigger_conditions` JSON using [Trigger Conditions Reference](TRIGGER_CONDITIONS.md). Key pattern:
```
entry.long = AND(sm.long_ratio>=50, sm.wallet_count>=3, OR(ta.4h.supertrend=="buy", ta.4h.adx>=15))
exit.long  = AND(sm.short_ratio>=55, ta.4h.supertrend=="sell")   // SL/TP on exchange is primary exit
```
Include in `agent-create`. Summarize back to user in plain language. **Never show JSON to user.**

**If user says "defaults" →** server auto-fills from preset. Proceed.

**Q5: Execution mode?** → `--execution-mode`
- **auto** — Fully automated. Best for trusted configs.
- **hitl** — Agent creates trade plans for user approval. **Recommend for new agents.**

**Q6 (optional): Additional notes?** → added to `--prompt-config`

**Q7: Withdrawal address?** → `--withdrawal-addresses`
**ALWAYS ask.** Without it, funds can be withdrawn to ANY address.

## Step 2: Create Agent

Build ONE `agent-create` call. Example:
```
agent-create --name "BTC Momentum" --type momentum_hunter --assets BTC,ETH --leverage 5 --risk-per-trade 1 --max-daily-loss 3 --execution-mode hitl --withdrawal-addresses 0x...
```

Server auto-generates `trigger_conditions`, `prompt_config`, `trading_risk` if not provided. For customization, include `--trigger-conditions '{...}'` and `--prompt-config '{...}'` — see [Commands](COMMANDS.md) for full params.

## Step 3: Review & Deploy
1. `agent-get <agent_id>` — review full config
2. `agent-deploy <agent_id>` — validate and enable (ask user before adding `--confirm`)

**If deploy fails (400 Bad Request):**
- Response contains `errors` (missing fields) and `fix_hint` (exact command to run)
- **Fix ALL errors in ONE `agent-update` command** — do NOT patch one at a time
- Then retry `agent-deploy` ONCE
- **NEVER** tell user to "go to app.zonein.xyz to deploy" — that feature does not exist
- **NEVER** blame CLI or backend — all parameters are supported via `agent-update`

**`agent-update` supports FULL config changes** (same fields as `agent-create`):
`--prompt-config`, `--trigger-conditions`, `--trading-risk`, `--signal-weights`, `--strength-thresholds`, `--timeframe-weights`, `--assets`, `--categories`, `--leverage`, `--execution-mode`, `--withdrawal-addresses`, plus shorthand: `--trading-strategy`, `--custom-rules`, `--risk-management`.
NEVER tell user to delete and recreate an agent — use `agent-update` to change any config.

3. **Telegram:** Run `telegram-config`. If not connected, recommend setup — essential for HITL agents. See [Workflows](WORKFLOWS.md) for setup flow.

## Step 4: Fund the Agent

**🚨 MANDATORY ADDRESS VERIFICATION:**
1. Read `CRITICAL_AGENT_ID` and `CRITICAL_DEPOSIT_ADDRESS` from `agent-create` output
2. **VERIFY:** Run `agent-deposit <agent_id>` to cross-check. If mismatch → STOP
3. Present deposit info to user using the **safe deposit format** below:

**Safe deposit format** (use EXACTLY this layout from `agent-deposit` response):
```
💰 Deposit USDC to your agent

📋 Address: `{deposit_address}` (Arbitrum One only)

✅ SAFEST — scan QR or click payment link to auto-fill your wallet:
🔲 QR Code: {qr_code_url}
💳 Payment link: {payment_link}

🔗 Verify on Arbiscan: {verify_address_url}

⚠️ ONLY send USDC on Arbitrum (chain 42161). Wrong chain/token = lost funds.
Gas fees sponsored by Zonein — no ETH needed.
```

4. `agent-balance <agent_id>` — confirm `arbitrum_usdc` arrived
5. `agent-fund <agent_id>` — bridge to Hyperliquid (ask user, then add `--confirm`)
6. `agent-balance <agent_id>` — confirm `account_value`

## Step 5: Monitor
- `agent-overview <id>` — PnL, ROI, win rate, status
- `agent-stats <id>` — Sharpe, drawdown, profit factor
- `agent-trades <id>` — trade history
- `agent-positions <id>` — open positions
- `agent-disable <id>` — stop trading

For position management, market overview, deep analysis, and **HITL trade plan workflows**, see [Workflows Reference](WORKFLOWS.md).
