# Cerebras repeat study (D212) — predictions (committed BEFORE any dispatch)

Master chose O18 option A (D212): a repeat study on the golden bug units on
Cerebras, after the Cerebras golden gate stopped at G1
(run 39129231446, verdict `no_catch`; the reviewer confirmed first-hand that
the G1 prompt and message hashes were byte-identical to the Groq-era G1 that
caught it, so the only changed input was the provider — with one sample per
provider at temperature 0.2, provider effect and luck cannot be told apart
from the gate alone).

## The rule (D212, verbatim)

"10 runs, skill arm, main 24d138a, measured_result false, label repeat (D212): 5 × golden node-express-sqlite bug, 5 × golden python-flask-sqlite bug. ≥4/5 catches on BOTH → G3 + G4 cleans, then the blind run. 2–3/5 on either → stop; skill iteration 2 (D180) to Master. ≤1/5 on either → model/provider options to Master. G1 39129231446 is reported alongside, not counted. An environment/transport INCONCLUSIVE is re-run once in the same slot (recorded). An invalid-block or no-valid-probe result counts as a miss. Reviewer prediction: about 3/5 per unit (band 2–4)."

## Units

Same input shape as the D180/D209 gate runs: `measure.yml` from main
`24d138ad1b69b1f05734561d174556d7a53bd5b2` (merge-base with main is the
commit itself, D155), arm=`skill`, unit_repo=`golden/<app>`, target_repo
entered as the literal `golden`, base/bug/fix/clean empty. Master approves
each run in GitHub (D100); no API approvals.

- golden/node-express-sqlite bug:
  `smoke-d212-node-express-sqlite-bug-r1` … `smoke-d212-node-express-sqlite-bug-r5`
- golden/python-flask-sqlite bug:
  `smoke-d212-python-flask-sqlite-bug-r1` … `smoke-d212-python-flask-sqlite-bug-r5`

Per-record checks (every run): provider `cerebras`; model `gpt-oss-120b`;
model_client pin `a35238a5a38b36ae691ab97861a064938787c5b14a49c380c009067a99f3785b`;
skill hash `e04cb8111c9767149abc371d0ee1b0781d01db9e2baab96f767224ee846f2099`;
`measured_result: false`; replay reproduces the stored verdict.

## Prediction

Wally's prediction: about 3/5 catches per unit (band 2–4), concurring with the reviewer — the G1 prompt and message hashes were byte-identical to the Groq catch, so the study measures run-to-run variance of the same author at temperature 0.2 on a new serving stack.
