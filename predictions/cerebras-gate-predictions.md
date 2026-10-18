# Cerebras golden gate — predictions (committed BEFORE any dispatch)

Gate for the provider amendment (D206/D207, signed by Master; PR #145 merged
as main `24d138ad1b69b1f05734561d174556d7a53bd5b2`). This re-runs the D180
skill v3 golden gate on the new provider, Cerebras, before any measured use
of Cerebras — per the signed amendment: "Earlier Groq-era runs (smoke, D152;
gate, D189) stay on the record as Groq-era evidence and are not counted."

## Gate definition (D206(4))

- Four `measure.yml` runs, dispatched from the merge commit
  `24d138ad1b69b1f05734561d174556d7a53bd5b2` on `main` (merge-base with
  main is the commit itself, D155).
- Same inputs as the D180 gate: arm=`skill`; unit_repo=`golden/<app>`;
  target_repo entered as the literal `golden`; base/bug/fix/clean empty.
  Master approves each run in GitHub (D100).
- PASS = both bug units score `candidate_catch` AND both clean units score
  `false_alarm: false` AND every model response has
  `finish_reason: stop`. On any miss: stop and report; no tuning.
- Per-record checks (every record, all four runs):
  - provider `cerebras`
  - model `gpt-oss-120b`
  - model_client pin
    `a35238a5a38b36ae691ab97861a064938787c5b14a49c380c009067a99f3785b`
  - skill hash `e04cb8111c9767149abc371d0ee1b0781d01db9e2baab96f767224ee846f2099`
    (skill v3)
  - `finish_reason: stop` on every model response
  - `measured_result: false` — this gate is not a registered measurement
  - dispatch ref `24d138ad1b69b1f05734561d174556d7a53bd5b2`
    (the merge commit; its merge-base with main is itself)
- Units (the D180 gate IDs with the suffix `-cerebras` appended), in gate
  order:
  - G1: golden/node-express-sqlite bug,
    bug_id `smoke-d180-node-express-sqlite-bug-cerebras`
  - G2: golden/python-flask-sqlite bug,
    bug_id `smoke-d180-python-flask-sqlite-bug-cerebras`
  - G3: golden/node-express-sqlite clean,
    clean_id `smoke-d180-node-express-sqlite-clean-cerebras`
  - G4: golden/python-flask-sqlite clean,
    clean_id `smoke-d180-python-flask-sqlite-clean-cerebras`

## Predictions

All four runs are predicted to MATCH their D180 Groq-era outcomes:

- G1 (node bug) → `candidate_catch` (D180: candidate_catch [0, 1]).
- G2 (python bug) → `candidate_catch` (D180: candidate_catch [0, 1]).
- G3 (node clean) → `false_alarm: false` (D180: false_alarm: false).
- G4 (python clean) → `false_alarm: false` (D180: false_alarm: false).

Basis: the model weights (gpt-oss-120b) and the skill (v3,
`e04cb8111c9767149abc371d0ee1b0781d01db9e2baab96f767224ee846f2099`) are
identical to the D180 gate; only the serving host changed.

Flagged uncertainty (per the signed amendment): same weights is not the
same as identical outputs — serving stacks can differ in numerics and
defaults. A divergence on any run is therefore a real possibility, and any
miss stops the gate with no tuning, exactly as the gate rule states.
