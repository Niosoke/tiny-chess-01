# tiny-chess-01

Original 0x88 chess engine in Python. `main.py` is an LZMA payload
written by the kit's `makefun compress` (source kept as `src.py` while
developing; the engine ships as `main.py` only).

Negamax alpha-beta, quiescence, transposition table, MVV-LVA +
killer + history ordering, null-move pruning, PVS + LMR, iterative
deepening to a fixed 70,000 nodes a move. Material + piece-square
eval, endgame king table, bishop-pair bonus. Deterministic: no clock,
no unseeded randomness. MIT licensed. Build log: SESSION-TRANSCRIPT.md.
