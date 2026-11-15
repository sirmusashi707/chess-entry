# Tiny chess engine — make.fun entry

Bounty: https://make.fun/bounty/tiny-chess-01
($500, closes Wed 25 Nov 2026 01:00 WITA; practice Elo ≥ 1,154 qualifies
for the final 25 Nov–2 Dec; the prize needs a mean Elo above the house
baseline, sunfish.)

## What this is

`main.py` is a tiny chess engine — **4,088 bytes as committed**
(limit 4,096) — that plays through the `MAKEFUN_ENV_URL` protocol
(`POST /game`, `/move`, `/resign`). It is a single self-contained
program: an LZMA + base85 compressed core, unpacked at import.

## How it plays

- **Board:** 0x88. **Search:** negamax + alpha-beta + transposition
  table + 1 killer move per ply + MVV-LVA ordering, iterative
  deepening with a fixed budget of **30,000 nodes per move**.
- **Pruning:** quiescence (captures/promotions; all evasions in check),
  null-move pruning (R=2, depth ≥ 3), late-move reductions,
  futility pruning (depth ≤ 2). Repetition detection.
- **Eval:** material (sunfish values P100 N280 B320 R479 Q929) +
  procedural piece-square tables + tapered king (middlegame/endgame)
  + passed-pawn bonus + bishop pair +30.
- **Deterministic:** no clock, no randomness, no model calls —
  Python 3.13 standard library only, same move for the same position
  every run.

## Practice results (local env server, 8 games each)

| Trial | v4 engine | Sunfish baseline (official) |
|-------|-----------|-----------------------------|
| trial-0 | 3.0 pts, Elo 1253 | 1326 |
| trial-1 | 4.0 pts, Elo 1396 | 1396 |
| trial-2 | 4.0 pts, Elo 1396 | 1253 |
| **Mean** | **Elo 1348** | **1325** |

- **Mean 1348 vs baseline 1325 → lead +23 Elo.** All 3 practice
  trials above the 1,154 cutoff for the final.
- Perft: 0 failures vs python-chess (start, kiwipete, pos 3/4/5,
  en passant, castling) to depth 3.
- No illegal moves, no resigns, no time losses in any trial.
- Honest note: one move in trial-1 took 8,152 ms (limit 10,000 ms
  per move) — a more complex position in the final could time out.

## How to run

```sh
MAKEFUN_ENV_URL="http://127.0.0.1:<port>" python3 -P -s main.py
```

## Constraints (per the bounty brief)

- **Python 3.13 standard library only** — no third-party packages,
  no network except the env protocol, no model calls
  (a model call is a disqualification).
- `main.py` ≤ 4,096 bytes as committed; deterministic;
  ≤ 10 seconds per move, 1,800 seconds per trial.

## Files

- `main.py` — the entry (stdlib only)
- `LICENSE` — MIT

## License

MIT — see `LICENSE`.
