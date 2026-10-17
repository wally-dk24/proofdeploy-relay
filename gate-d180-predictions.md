# D180 golden gate — predictions (committed BEFORE the first dispatch)

Gate for skill registration v3 (D179/D180/D185). Four smoke-style runs on the
golden apps only, skill arm, `measured_result: false`. Dispatched from the
skill v3 PR branch head `e7741f759be2096621fe9528382fb9ce552850dd`
(branch `feat/skill-registration-v3`, containing main `d9fa4ac`; dispatched
BEFORE the #142 merge per D187). Every record must show ref `e7741f7` and
skill hash `e04cb8111c9767149abc371d0ee1b0781d01db9e2baab96f767224ee846f2099`
(the v3 author copy). Inputs: arm=skill; unit_repo=golden/<app>;
target_repo="golden" (literal); base/bug/fix/clean empty. Master approves
each run in GitHub (D100). Run all four even if one misses; stop only on a
harness/environment failure. Report invalid-block counts per run.

Gate rule (from the signed registration): both bug units caught AND no
false alarm on either clean unit → the v3 registration applies from the
WO-6 blind run onward. At most 2 iterations; otherwise stop and replan
with Master (D179, D180).

Predictions (reviewer, D186):

- G1 measure golden/node-express-sqlite bug
  (bug_id smoke-d180-node-express-sqlite-bug) → CAUGHT.
  Alternatives: no_catch, same cause as M1/M2 (the author asserts the
  pre-fix side's polarity); INCONCLUSIVE probe.
- G2 measure golden/python-flask-sqlite bug
  (bug_id smoke-d180-python-flask-sqlite-bug) → CAUGHT.
  Alternatives: no_catch, same cause as M1/M2; INCONCLUSIVE probe.
- G3 measure golden/node-express-sqlite clean
  (clean_id smoke-d180-node-express-sqlite-clean) → NO FALSE ALARM.
  Alternatives: false alarm by an invalid block; false alarm by a
  failing probe.
- G4 measure golden/python-flask-sqlite clean
  (clean_id smoke-d180-python-flask-sqlite-clean) → NO FALSE ALARM.
  Alternatives: false alarm by an invalid block; false alarm by a
  failing probe.
