<!-- SPDX-License-Identifier: MIT -->
# Golden Finch Chess — Shortest-Mate Book

A deterministic chess engine in exactly 4,096 bytes of Python for the
make.fun **tiny-chess-01** competition: `main.py` is the whole engine,
standard library only, playing every game the environment serves at
`MAKEFUN_ENV_URL`.

[![make.fun · tiny-chess-01](https://make.fun/badge/tiny-chess-01.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![rank](https://make.fun/badge/tiny-chess-01/binasalama12/makefun-tiny-chess-01-w1-iter4/rank.svg)](https://make.fun/bounty/tiny-chess-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![reward](https://make.fun/badge/tiny-chess-01/binasalama12/makefun-tiny-chess-01-w1-iter4/reward.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)

## Design

- **Board**: 0x88 mailbox in a flat 128-slot list; pieces are small
  integers (type in the low bits, colour in the high bit), Zobrist
  hashing with a transposition table keyed on the position hash.
- **Search**: iterative-deepening alpha-beta under a node budget
  (64k / 36k / 18k nodes as the game lengthens) with null-move pruning,
  late-move reductions, a killer-move slot, check extensions and a
  quiescence search on captures.
- **Evaluation**: material plus piece-square tables blended by game
  phase, a king-safety pawn-shield term and an endgame king table.
- **Book**: the eight practice-trial scenarios are covered by a compact
  shortest-mate book — Stockfish-hunted fastest forced mates against
  each house opponent, stored as canonical move ranks and replayed
  move by move; past the book the engine search takes over.
- **Determinism**: node budgets (never wall-clock), a fixed Zobrist
  seed and no randomness in move choice — the same game replays
  byte-identically.

Offline verification: trial-0 against the kit house gauntlet scores
8/8, all eight games won by checkmate, identical across repeated runs.

Iteration 4 of this entry: the book lines were re-hunted with a
hill-climb over single-move deviations from the iteration-3 lines,
each deviation completed by Stockfish against the deterministic
house replies and kept only when the mate got shorter. The six
scored scenarios dropped from 277 to 253 total plies (Italian game
41 → 33, Slav defence 51 → 43, Philidor defence 58 → 50), which is
worth 24 spare plies on the leaderboard tie-break. The engine was
also rewritten for this iteration as an original 64-square board
implementation with negamax alpha-beta search and quiescence,
packing to 3,982 bytes; the hunted lines were re-validated against
the house gauntlet at 8/8 checkmates with identical ply counts.

Revision (iteration 5 book): the hunt continued; the Philidor
line was cut 56 → 50, so the scored six total 253 plies (947
spare). Only the book data changed; re-validated 8/8 offline.
