# Gotchas & Common Pitfalls

- **`--confirm` is a LAUNCH KEY, not a flag.** NEVER include it until user explicitly says "yes" to a summary you showed. User saying "open BTC long" is a REQUEST — not approval. Show summary first, wait for "yes", THEN add `--confirm`.
- **`agent-plan-action reject` does NOT need `--confirm`.** Only `approve` and `edit` do.
- **PM agents not supported yet.** PM data reading (signals, leaderboard, consensus) works. Agent creation is perp-only.
- **HIP-3 fees are 2x standard.** Always mention this when creating HIP-3 agents. Factor into TP.
- **High leverage needs wide SL.** 15x+ leverage requires min 5% SL, 10x+ needs 4%, 5x+ needs 3%. Tight SL with high leverage = instant stop-out.
- **Minimum hold times enforced.** Scalping agents: 1h minimum hold. Others: 3h minimum hold. Prevents rapid cycling.
- **Withdrawal requires disable first.** `agent-disable` before `agent-withdraw`.
- **No withdrawal whitelist = ANY address accepted.** If user didn't set `--withdrawal-addresses` during create, warn them to add one via `agent-update` before funding.
- **Deploy errors return `fix_hint`.** Read and execute it — don't guess.
- **HITL plans expire after 2 hours.** If user doesn't respond, plan auto-expires.
- **app.zonein.xyz is view-only.** No deploy, config edit, or fund buttons. ALL agent operations go through CLI.
- **Position sizes are in USD notional.** Double-check the amount with user before executing. $1000 ≠ $100.
- **`agent-withdraw` is full sweep.** No `--amount` param — it withdraws ALL funds from the vault. Cannot withdraw partial amounts. Agent must be disabled first.
- **`agent-delete` has NO `--confirm` gate.** It executes directly via DELETE API. Ask user for confirmation verbally before running — the script has no programmatic safety gate for this.
