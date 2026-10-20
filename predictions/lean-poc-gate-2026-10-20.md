# Lean PoC gate (Stage 2 Phase 0, D258) — predictions, committed BEFORE dispatch

Date: 2026-10-20. Written by the orchestrator (Opus, D237). Not a registered measurement (`measured_result: false`). Code: main `e31979143f5bb7e0581ab48c93451eece2437aa9` (P1–P6b merged). Workflow `lean-poc.yml`, dispatched three times from main: `units=golden`, `units=mutants`, `units=paperless` (split so a slow paperless provision cannot time out the others). Master approves each in `measurement`. Judge: gpt-oss-120b on Cerebras, pinned client `1e063204`, free tier.

## Pre-registered criteria (plan `claude/stage-2-phase-0-poc-plan-2026-10-20.md`, implemented in `experiments/lean-poc/gate.py`; counting fixed in D263)
1. golden-bug FAIL; golden-clean PASS.
2. Mutants: caught = FAIL; observable = ≥1 real difference or (flaky) hard failure; ≥ 70% of observable mutants caught; no mutant with unexercised executable changed lines may be PASS.
3. Paperless bug (B^ `17dc4828` → B `aa67fd3a`): a real verdict (not environment/provider/error); caught = FAIL and confirmed on fix^ `14b6a41b` → fix `d3710856` — reported, not required.
4. False alarms = FAIL on a clean unit: 0 on golden-clean, ≤ 1 across the 3 paperless clean units (`2ab1ac7c`, `17796357`, `5569447e`); INCONCLUSIVE on clean units reported separately.
5. Every unit ≤ 10,000 AI tokens; any provider failure reported.
Stop rules (D258): paperless bug reachable over HTTP but never executed; > 1 false alarm; < 50% of impacted operations reaching a 2xx.

## Predictions
1. golden-bug: FAIL `judged_regression` (quote "id 1 (done=false)"), < 2,000 tokens. golden-clean: PASS (the `tag` field judged correct from the intent), < 2,000 tokens. Confidence: high (the sandbox CI test already produced the bug FAIL with a stub judge).
2. Mutants: a large share are NOT observable through the golden app's few endpoints, or are observable only as fields the seed facts don't describe (status codes, error messages) → many INCONCLUSIVE `needs_information`, not FAIL. Prediction: observable 12–17 of 20; caught 6–12 of the observable; criterion 2 (≥ 70%) is AT RISK — lean: likely to miss. No mutant PASSes with unexercised lines.
3. Paperless bug: a real verdict, most likely INCONCLUSIVE (`change_not_exercised` or `needs_information`); FAIL+confirmed possible but not expected. The run is the first real test of the paperless OpenAPI index, login and inline background jobs; an `error` (our gap) on first contact is a live possibility and would be fixed and re-run, not counted.
4. Paperless clean: 0 FAIL most likely; unit 2ab1ac7c (570 source files) most likely INCONCLUSIVE `change_not_exercised`. False-alarm criterion met.
5. Tokens: every unit well under 10,000; no provider refusals (calls are small).
Overall lean: criteria 1, 4 and 5 met; 2 at risk; 3 informative. If 2 fails, that is a stop-rule-adjacent result to report honestly, with its causes classified (unobservable vs no authority vs judged wrong).

## Addendum — re-dispatch (D265), committed before the re-dispatch
Run 1 (`39501135862`, golden) failed at startup on `import coverage` (a missing runtime dependency; no unit ran, no model call). The queued mutants/paperless runs were cancelled before approval. Fixed by PR #166 (`coverage==7.16.2` runtime dependency, import guard test, runtime-install CI smoke), merged as `ecc967aee44c1d38b259d1de380c4b894bf6f9aa`. No pipeline logic changed; units, criteria and every prediction above stand unchanged. Re-dispatched from `ecc967ae` as the same three runs.
