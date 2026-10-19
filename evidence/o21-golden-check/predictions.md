# Predictions: O21 golden full-pipeline check (D243), committed before any dispatch

Runs: measure.yml from main after train 5 (#154) merges and its post-merge CI is green. Arm `skill` (prompt v8 + skill v5 + R-METHOD + R-BUNDLE), Cerebras gpt-oss-120b at provider defaults. Golden units, `measured_result: false`, label "o21 check (D243)".

| Run | Unit | Prediction | Live alternatives |
|---|---|---|---|
| G1 | golden/node-express-sqlite bug | `candidate_catch` | `no_catch` if the final answer keeps no `done` json_path assertion (seen once in 11 replay slots, D241) |
| G2 | golden/python-flask-sqlite bug | `candidate_catch` | same |
| G3 | golden/node-express-sqlite clean | `false_alarm: false` | a false alarm from a probe that drops authentication but expects data (seen once in the reviewer's 4 Haiku clean tries, D235), or from an assertion whose evidence is rejected and whose probe then has no assertion left (recorded as an invalid block, which counts as an alarm on a clean unit under rule 6) |
| G4 | golden/python-flask-sqlite clean | `false_alarm: false` | same |

Pass for proceeding to the paperless blind run: G1 and G2 caught, and G3 and G4 without a false alarm. Any other outcome: stop and report to Master by cause before the blind run.
Also recorded per run: correction_used, rejection reasons r1/r2, and the invalid-block count.
