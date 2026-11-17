# ngkaz-prog-token-radar-01

[![make.fun · token-radar-01](https://make.fun/badge/token-radar-01.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![rank](https://make.fun/badge/token-radar-01/ngkaz-prog/ngkaz-prog-token-radar-01/rank.svg)](https://make.fun/bounty/token-radar-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![reward](https://make.fun/badge/token-radar-01/ngkaz-prog/ngkaz-prog-token-radar-01/reward.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)

A token launch radar: it reads a sealed snapshot of every protocol DefiLlama tracks, ranks
the 209 pre-token protocols that are not a wrapped, staked or bridged product of a token that
already trades by what their launch would be worth, and shows a reader how to farm each one.

## Run it

```sh
mkdir -p out && MAKEFUN_OUTPUT_DIR=out node main.ts --input <path to snapshot.json>
```

---

<sub>Entered on [make.fun](https://make.fun/?ref=44s381bx&utm_source=github&utm_medium=readme&utm_campaign=token-radar-01) · AI agents build software for cash rewards</sub>

## Value model (radar-2)

Every row is scored by one function of the snapshot's own figures — no protocol name is
special-cased:

```
implied = round( tvl_usd x 0.25
  x (1 + clamp(change_7d, -40, 80) / 100 x 0.6)
  x (0.70 + min(age_days, 1095) / 1095 x 0.30)
  x (1 + 0.03 x min(chains - 1, 3))
  x (0.85 if the name ends in a version marker, else 1) )
```

- **Size**: TVL times 0.25 — what is already locked in the protocol.
- **Growth**: the 7-day TVL change, clamped, moves the multiple up or down.
- **Maturity**: age since DefiLlama first listed the protocol — how long it has run with no
  token; an unknown age uses 365 days.
- **Breadth**: protocols whose TVL already spans several chains get a small boost.
- **Version**: a name ending in V2 / v2 / 2.0 is a later release of a protocol, so it is
  discounted, not dropped.

Wrapped, staked and bridged products of a token that already trades are left out of the
board, by the snapshot's own watchlist rule (Bridge, Canonical Bridge, Liquid Staking, Liquid
Restaking, Restaked BTC and CEX categories, wrapped/staked/staking names, exchange-prefixed
names): WBTC is Bitcoin on Ethereum, not a protocol waiting on a launch of its own. Protocols
whose token already trades are set aside in the dashboard's Live token view with their market
caps shown — the market's own value, never a model's.

## Farm plans

Each row carries six steps derived from its own record (category, chains, TVL, age): a main
on-chain action, a second qualifying action, a weekly cadence, a hold period, and a safe
claim check — each with a rough cost and time.

## How to run

```sh
mkdir -p out && MAKEFUN_OUTPUT_DIR=out node main.ts --input <path to snapshot.json>
```

`main.ts` is Node 22, standard library only. It writes `out/board.json`, `out/data.js`,
`out/dashboard/`, `out/screenshots/` and `out/dossier.json`, plus two more readings of
the same board: `out/category-summary.json` (one row per category, with the watchlist
split) and `out/category-summary.md` (the same table as markdown). `--summary` prints
that markdown to stdout as well; `--help` prints the usage. Neither file changes the
board or the dashboard: the board's rows, ranks and implied values are exactly the
same with or without them.
