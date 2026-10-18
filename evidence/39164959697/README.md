# D61 evidence pack — WO-6 blind run 39164959697 (D228)

- Run ID: 39164959697 — https://github.com/wally-dk24/proofdeploy/actions/runs/39164959697
- Workflow: measure.yml, dispatched from main `e8bf530bd2a3df06286d23f305966aa59a23a121`, `dry_run=none`, D219 inputs (target paperless-ngx, arm skill, bug_id `wo6-paperless-371c4f57`).
- Predictions: `predictions/wo6-blind-run-4.md`, relay commit `a2745370ef4858104bd589c92073eed8bc04f379` @ 2026-10-18T20:47:32Z, before the dispatch at 20:48:05Z.
- Workflow conclusion: success. Records branch `records/run-39164959697` @ `0b15436586d8e62dd8e41ee968d4a6141217b12d`.
- Artifacts: the run uploaded NO artifacts (artifacts total_count = 0), so there is no artifact zip; the records were taken from the records branch and the log from the Actions job log.

## Files

- `runs.jsonl` — the evidence + score records exactly as committed on the records branch. sha256 `5b2090e5ff0ebc612403be3a8b91f597d67e1ff90fc2604bfa013cc4109626f1`.
- `job.log` — the full Actions job log, untrimmed (61,820 bytes). sha256 `fcf24c2c594f17d79a5d7cf042e41bef5224467edbf5969b6c1bac761d655181`.
- `replay.txt` — replay of the evidence record with `proofdeploy.replay.replay_verdict` at main e8bf530: stored `no_catch`, replay `no_catch`, reproduces.

## Result (metadata only — probe contents are not reproduced here)

- Score verdict: **no_catch**. Both sides provision READY; 8 valid probes, 0 invalid blocks; per-side probe verdicts identical in shape: probes 0–4, 6, 7 `fail` on both sides; probe 5 `inconclusive` (class `auth`) on both sides. No probe discriminated, so the mechanical verdict is no_catch.
- Identity: contract store_path `readiness/contracts/paperless-ngx__paperless-ngx.yml` sha256 `eb60559b…`; rendered fixture (bundle fixture-description.txt) sha256 `17edd561…`; skill `ff601eb1…`; prompt pin `fa72a0e6…`; client/prompt-builder pin `a35238a5…`; public_contract_env_keys exactly the 2 declared keys; container_env_keys exactly the 6 contract keys; provider cerebras, model gpt-oss-120b, endpoint api.cerebras.ai; schema 1.8.0; measured_result true; dry_run null.
- Author call: finish_reason `stop`; usage prompt 7,937 / completion 2,910 / total 10,847 tokens (reasoning 1,781).

## Redactions

The records and log are published as produced by the harness (tokens and bootstrap credentials are redacted by value in the records themselves). Before committing, both files were additionally scanned for the contract's 6 non-public values (the 4 non-public env values + the auth username/password values): **0 occurrences of every value in both files**. No further redaction was applied by hand.
