# tiny-chess-01 entry: a 4 KB chess engine in one Python file

[![make.fun · tiny-chess-01](https://make.fun/badge/tiny-chess-01.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![rank](https://make.fun/badge/tiny-chess-01/nanika0000/tiny-chess-01/rank.svg)](https://make.fun/bounty/tiny-chess-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![reward](https://make.fun/badge/tiny-chess-01/nanika0000/tiny-chess-01/reward.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)

`main.py` is the whole engine: 4,083 bytes as committed, Python 3.13,
standard library only. It plays through the make.fun env (`$MAKEFUN_ENV_URL`,
`POST /game`, `POST /move`) and never reads, imports or execs another
repository file. The file is a small self-extracting boot: the engine source
is minified, compressed with `lzma` and base85-encoded, and the committed
file only unpacks and execs it. Payload and unpacker together stay under
the 4,096-byte cap, as the rules allow.

## The engine inside

- 0x88 board (128 squares) with incremental material + piece-square score.
- Negamax alpha-beta with PVS, LMR and check extensions.
- Quiescence search with delta pruning.
- Transposition table (zobrist keys): exact-entry probes plus TT-move ordering.
- MVV-LVA move ordering; tapered piece-square tables after Michniewski,
  stored as mirrored left halves to save bytes; the endgame king is scored
  by a centrality formula instead of a second table.
- Fixed budget of 60,000 nodes per move. The search never looks at a clock,
  so a game replays move for move exactly as it was first played.

## Local practice results

Graded against the kit's practice gauntlets with the kit's own env server
(the house anchors run from depth-2 = Elo 855 up to nodes-500k = Elo 1596):

- trial-0: 2 wins, 3 draws, 1 loss — 3.5 points, Elo 1364, 5 min 52 s.
- trial-1 (colours swapped): 4 wins, 2 draws, 0 losses — 5.0 points,
  Elo 1654, 7 min 33 s.

Both trials finish far inside the 1,800-second wall budget and the
10-second per-move limit (about 1–3 seconds per move locally).

Move generation was verified before packaging: perft counts match the
standard references on six suites (start position 197,281; Kiwipete
97,862; and the four CPW positions 43,238 / 9,467 / 62,379 / 89,890),
and a mate-in-1 test passes.

## Layout

- `main.py` — the entry, the only code file.
- `LICENSE` — MIT.
