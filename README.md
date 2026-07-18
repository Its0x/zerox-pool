# Zero X Pool

A **SOLO-hybrid Bitcoin Cash mining pool** — find a block, take half.

**Live:** [0x.al](https://0x.al) · **Stats:** [bchstats.0x.al](https://bchstats.0x.al) · **Stratum:** `bch.0x.al:3333`

---

## The payout model

When the pool finds a block, the reward splits like this:

- **50%** → the miner who *found* the block, as a flat finder's bonus (the "jackpot").
- **The rest** → split among all *other* miners, in proportion to the shares they submitted since the pool's last block.
- **0.1%** → pool fee.

Unlike a pure solo pool (winner takes all, everyone else gets nothing), here **everyone who mined that round gets paid** when a block is found — you don't have to be the finder to earn.

## Zero custody — the pool never touches your coins

Every payout is written **directly into the block's coinbase transaction**, straight to each miner's own address. There is:

- no pool wallet,
- no account balance,
- no minimum payout,
- nothing to withdraw.

The Bitcoin Cash protocol itself pays you. The operator **cannot** hold, freeze, delay, or steal your rewards — because it never has them.

## Audit your own coinbase

Don't trust — verify. Every block this pool finds is a public receipt:

1. Open a block this pool mined in a block explorer (e.g. [Blockchair](https://blockchair.com/bitcoin-cash)).
2. Look at the **coinbase transaction's outputs**.
3. Confirm the split matches the rules above — ~50% to the finder, the remainder proportional to the other miners, 0.1% to the pool.

The exact code that builds every coinbase is in this repo: see `src/stratifier.c` — `plan_para_payouts()` and `find_or_generate_userwb__()`. Read it, build it, run it yourself.

## Mine on it

```
Stratum:  stratum+tcp://bch.0x.al:3333
Username: YOUR_BCH_ADDRESS.worker_name
Password: anything (not checked)
```

Any SHA-256 miner works (ASIC, Bitaxe, rented hashrate). Your BCH address **is** your account — rewards arrive there automatically.

**Ports** (difficulty auto-tunes; pick by hashrate):

| Port  | For                             | Difficulty        |
|-------|---------------------------------|-------------------|
| 3333  | Bitaxe / USB / small miners     | auto (starts low) |
| 6666  | single ASICs / mid-size rigs    | auto              |
| 9999  | farms & big rigs                | auto (starts 500k)|
| 5000  | rented rigs needing fixed diff  | fixed 500,000     |
| 10000 | rented rigs, higher fixed diff  | fixed 1,000,000   |

All standard BCH address formats work: CashAddr (`q…`/`p…`) and legacy Base58 (`1…`/`3…`), with or without the `bitcoincash:` prefix. Prefixed and unprefixed spellings of the same address are treated as one account.

## Honest expectations

This is a **lottery.** BCH's network hashrate is enormous, so a small pool may go a long time between blocks — you could mine for weeks and find nothing. That's normal, not a malfunction. The appeal is a *realistic* shot at a full block (far better odds than BTC solo), plus a consolation share whenever anyone finds one — not steady income.

## What's different from upstream

This is a fork of [asicseer-pool](https://github.com/cculianu/asicseer-pool) (itself derived from Con Kolivas' ckpool), adding:

- **`finder_percent`** config option — the flat finder's-bonus payout, built into personalized per-miner coinbases so the split is trustless and paid on-chain.
- Share accounting that resets each time the pool finds a block (no rolling window).
- Per-port start difficulties (`startdiff` as an array parallel to `serverurl`).
- Cashaddr-prefix normalization and a regtest SIGFPE fix.

**License:** GPLv3 — see [COPYING](COPYING). Upstream project documentation follows in [`README`](README).
