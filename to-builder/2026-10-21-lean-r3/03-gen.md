# WO-R3-G: seeded records first (judge skill v1 Part B 6)
Worktree `/home/claude/wt-r3-gen`, branch `fix/lean-r3-gen`. Files: `proofdeploy/lean/generate.py`, tests. Touch nothing else. Register row: R2.

1. Parse record ids from the seed facts mechanically: patterns `id <n>`, `id=<n>`, `#<n>`, `<word> <n>` where the word is a singular form of a collection in the index (e.g. `note 1` ↔ `/notes/{id}`). Only integers. No ids → no change in behaviour.
2. Before any other scenario on a side, for every authenticated or public GET operation in the index whose path has exactly one id parameter, read each seeded id once (`GET /notes/1`, `GET /notes/2`), counted in the request budget, recorded as scenarios like any other (they join the impact set when they hit changed lines, so they run on all 2×2 sides).
3. Tests: golden flask index + the golden seed fact → the first scenarios are `GET /notes/1` and `GET /notes/2`; no-fact case unchanged (byte-identical scenario list to today for a fixed seed); budget counting; a fact naming an id for a collection not in the index → ignored. Plant: seeded reads disabled → caught; ids parsed as strings that don't match → caught.
