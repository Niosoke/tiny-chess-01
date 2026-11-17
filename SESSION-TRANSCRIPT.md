# SESSION-TRANSCRIPT.md — tiny-chess-01 build log (WIB), facts kept.
- 00:00 start. Bounty page + requirements JSON read. Gauntlet: d2 855,
  d3 1073, d4 1199, 100k 1392, 250k 1497, 500k 1596, 1M 1667, 2M 1712.
  Floor Elo 1154; prize bar = beat sunfish baseline (~1365).
  Caps: main.py <=4096 B, repo <=8192 B, Py3.13 stdlib, fixed-node
  search, 10 s/move, 1800 s/trial.
- 00:01 kit downloaded (6,937,033 B), extracted. Read RULES.txt,
  starter, kit.json, trial-0, compress help.
- 00:03 compress verified on dummy (stub `#coding:l1` + lzma, ~75 B).
  Plan: original 0x88 engine, negamax + quiescence, TT, MVV-LVA/
  killer/history ordering, iterative deepening, fixed node budget,
  material+PST.
- 00:07 v1 src.py 17,796 B. Perft 19/19, then pos4 d4 +69: gen()
  filter skipped unmake() on illegal moves; castling mask missed
  king moves (CM[e1]=12, CM[e8]=3). Perft 21/21 pass vs
  python-chess (startpos d4 197281, kiwipete d3 97862, pos3 d4
  43238, pos4 d4 422333, pos5 d3 62379, pos6 d3 89890).
- 00:12 hand-minified: src.py 10,269 B. Perft 21/21, bench
  deterministic. Compress -> main.py 3,984 B.
- 00:20 trial-0 local: 1W 1D 4L, Elo 997. G1 time loss = my stray
  manual curl /game (started G1 clock early). Engine never timed
  out (273 moves, mean 1.61 s, max 4.39 s). G2 win d3, G5 draw
  250k. True strength w/o artifact ~1210. Early piece losses
  plies 13-24 in G3/G4/G6: need more depth.
- 00:30 v2: light legality leg() in gen; PSTs mirrored -> base64
  600->300 chars; BD 30000->70000. Perft 21/21, nps 26k-50k (was
  11-16k), 100k nodes 3.84 s. main.py 3,960 B.
- 00:50 trial-0c (v2, BD 70000): 3W 2D 1L, Elo 1451 (floor 1154
  cleared, baseline ~1365 beaten by 86 on this trial). G2/G3/G4
  wins (d3,d4,100k), G1/G5 draws, G6 loss 500k. BUT max move 9.18 s
  (mean 2.05 s, p99 4.5 s): too close to 10 s under VM contention.
  BD cut to 50000 for safety.
- 01:05 trial-1 (v2, BD 50000): 2W 2D 2L, Elo 1279. Max move 3.36 s
  (mean 1.49 s) but Elo fell 172 vs trial-0c; G4 collapsed vs 100k
  (piece ply 10, queen ply 64). BD cost likely real but confounded
  by openings. BD back to 70000 for trial-2 (clean comparison);
  9.18 s max was one contention spike in 311 moves.
- 01:25 trial-2 (v2, BD 70000): 2W 3D 1L, Elo 1364. Max move 4.57 s.
  v2: 1451/1364 at 70k, 1279 at 50k.
- 01:50 v3 aspiration (+-45): +1 ply bench, but trial-0 1W 2D 3L,
  Elo 1099 (v2: 1451 same openings). REVERTED to v2; main.py sha
  verified = trial-0c binary.
