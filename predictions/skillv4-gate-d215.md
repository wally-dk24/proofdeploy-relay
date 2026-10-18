# Skill v4 gate predictions (D215)

Committed before any dispatch. PR: wally-dk24/proofdeploy #147 (skill registration v4, D216), head `e07d8b63b091cd29ded520f24838de810342b188` on main `37568e43835b21db0239df4bdb580084587b846a`. Dispatches happen only after the reviewer's acceptance and PR CI SUCCESS, from the accepted head (D187).

## Gate rule (verbatim)

16 runs, skill v4, from the accepted PR head, measured_result false, label skill v4 gate (D215): 5 × golden node-express-sqlite bug, 5 × golden python-flask-sqlite bug, 3 × golden node-express-sqlite clean, 3 × golden python-flask-sqlite clean. Pass = ≥4/5 catches on EACH bug unit AND 0 false alarms in the 6 cleans (an invalid block on a clean = false alarm). Fail → stop; replan with Master (no further skill iteration). An environment/transport INCONCLUSIVE is re-run once in the same slot. Reviewer prediction: about 4/5 per bug unit (band 3–5); 0 false alarms most likely.

## Wally's prediction

Wally's prediction: 4/5 catches on the node unit (band 3–5), 3–4/5 on the python unit; 0 false alarms in the 6 cleans most likely — A6/B3 target exactly the observed failure (asserting the diff's values against the fixture), so the polarity flips should mostly disappear; the residual risk is the author naming a source and still writing the diff's value.

Unit IDs: skillv4-gate-<app>-bug-r1…r5 and skillv4-gate-<app>-clean-r1…r3. Every record must show skill = ff601eb15b09e6e0bf8f48b2037f0ab8b2a1b0c90ac022156f76d0169380b36a (skill v4 author copy), provider cerebras, model_finish_reason and model_usage present (schema 1.7.0).
