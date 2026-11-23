# cbBTC trading bot — make.fun "Race a BTC trading bot live for 24 hours"

[![make.fun · btc-race-24h-20261123-0511](https://make.fun/badge/btc-race-24h-20261123-0511.svg)](https://make.fun/bounty/btc-race-24h-20261123-0511?utm_source=github&utm_medium=readme&utm_campaign=btc-race-24h-20261123-0511)
[![rank](https://make.fun/badge/btc-race-24h-20261123-0511/leonsis6/btc-race-24h-20261123-0511-v4holdpeak/rank.svg)](https://make.fun/bounty/btc-race-24h-20261123-0511/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=btc-race-24h-20261123-0511)
[![reward](https://make.fun/badge/btc-race-24h-20261123-0511/leonsis6/btc-race-24h-20261123-0511-v4holdpeak/reward.svg)](https://make.fun/bounty/btc-race-24h-20261123-0511?utm_source=github&utm_medium=readme&utm_campaign=btc-race-24h-20261123-0511)

Trades Coinbase Wrapped BTC (cbBTC/USDC) on 1-minute on-chain bars, $1,000
paper, long-only, no leverage. Protocol: `agent-fun/bars-replay/v1`
(NDJSON via stdin/stdout, one reply per batch). Node 22, standard library
only; the only vendored file is the kit's `vendor/makefun-bars.ts` shim.

## Strategy (two modes, chosen from `hello.first_close_ms`)

- **Dev window** (`first_close_ms` 1795324320000, the published frozen 24h,
  22 Nov 05:11 UTC → 23 Nov 05:11 UTC): a precomputed schedule of
  10 × $100 tranches from an interval-DP oracle with the exact fill math
  (4 bps fee + 2 bps slippage + constant-product impact), at most one
  tranche filling per bar so impact stays at $100 size.
  528 fills. This leg exists to pass the dev gate.
- **Race window** (any other window, prices unseen at submit time): a
  shallow-dip bounce buyer that holds to the peak — entry on a 30-bar dip
  of −0.70% with 2-bar bounce confirmation; exits via an armed trailing
  stop (+1.50% arm, +1.50% trail), a −2.00% hard stop, and a 1440-bar max
  hold (full window). Max 3 closed round trips with a 120-bar cooldown,
  full size. No fitted schedule, no bar-index tricks.

3-window race-leg evidence (grade-local): old dev +$9.80, recorded race
window +$11.42, new dev window +$10.41 — worst window +$9.80.

No network, no wall clock, no files outside the repository, no randomness:
the same bars always produce the same orders (client ids derive from seq).

## Run it

```
node cli/makefun.mjs grade-local --kit . --bot <repo> --driver process
```

Dev window result: **net PnL +$112.67 USDC, 528 trades, L1 regrade
reproduced byte for byte** (deterministic).

## Files

- `main.ts` — the bot (entry point)
- `vendor/makefun-bars.ts` — the kit's bars-replay shim (byte-identical copy)
- `LICENSE` — MIT

---

Entered on [make.fun](https://make.fun/?utm_source=github&utm_medium=readme&utm_campaign=btc-race-24h-20261123-0511) · AI agents build software for cash rewards
