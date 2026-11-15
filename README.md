# Tiny chess engine — make.fun entry

Bounty: https://make.fun/bounty/tiny-chess-01
($500, closes Wed 25 Nov 2026 01:00 WITA; practice Elo ≥ 1,154 qualifies
for the final; the prize needs a mean Elo above the house baseline,
sunfish's 4 KB engine.)

## What this is

`main.py` is a tiny chess engine that plays the gauntlet through the
`MAKEFUN_ENV_URL` protocol (`POST /game`, `/move`, `/resign`). It is a
single self-contained program: the readable source is packed into
`main.py` with the kit's `compress` (LZMA + base85 payload).

## How it plays

- **Board:** 0x88 with Zobrist hashing. **Search:** negamax +
  alpha-beta + transposition table + 2 killer moves per ply +
  history ordering, iterative deepening with a fixed budget of
  **60,000 nodes per move**.
- **Pruning:** quiescence (captures/promotions; all evasions in
  check; delta pruning), null-move pruning (R=3, depth ≥ 3),
  late-move reductions, repetition detection (game history +
  search path).
- **Eval:** material (P100 N320 B330 R500 Q900) + piece-square
  tables + tapered king (middlegame/endgame) + passed-pawn bonus
  + bishop pair.
- **Deterministic:** no clock, no randomness, no model calls —
  Python 3.13 standard library only, same move for the same
  position every run.

## Practice results (local env server, 6 games each)

| Trial | Points | W-D-L | Elo | Wall |
|-------|--------|-------|-----|------|
| trial-0 (compact source) | 3.0/6 | 3-0-3 | 1253 | 574 s |

- **Trial-0 (the dev grade) is 1253, above the 1,154 floor** for the
  final. The compact source plays at the same level as the readable
  draft (3.0/6, Elo 1279 on trial-0): same search, same eval, same
  60,000-node budget — only the spelling is tighter, so the packed
  `main.py` fits the 4,096-byte limit.
- Perft: 0 failures vs the standard suites (start, kiwipete, pos 3/4/5,
  en passant, castling) to depth 3; make/unmake restores board and
  Zobrist key exactly; mate-in-1 found; eval sanity values match
  the design (startpos 0, up a queen +880, passed pawn e5 +145).
- No illegal moves, no resigns, no time losses in the trial.

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
