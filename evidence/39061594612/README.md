# D61 evidence pack — D152 smoke run V1

- Run ID: 39061594612
- Workflow: verify (`.github/workflows/verify.yml`), job `verify` (117480522783)
- Run URL: https://github.com/wally-dk24/proofdeploy/actions/runs/39061594612
- Head SHA: d9fa4acb6dd65beef2583e1c8d6d0740d7a736e9
- Dispatch ref's merge-base with main: d9fa4acb6dd65beef2583e1c8d6d0740d7a736e9
  (dispatched from main itself)
- Unit: golden node-express-sqlite, FIX side (verify inputs: app=node-express-sqlite, change=fix)
- Label: smoke (D152) — NOT a registered measurement.

## Artifact

- Name: verify-node-express-sqlite-fix-39061594612
- sha256 of the artifact zip as downloaded:
  44a7d1c05062855f5a2c083981f6fa7d3ca3b4da789fb7a1723bbea9ea758824

## Scrubbing

The harness's own records already carry its redactions (`***REDACTED***` in
place of minted bearer tokens and the contract's CANARY_SECRET value).
Before committing, every file in this pack was scanned for credential
patterns (Groq API keys, bearer tokens, per-run canary tokens): 0 matches.
No additional scrubbing was needed and none was applied. The golden
fixture's seed credentials (seeduser/seedpass) appear inside the record's
prompt because they are the fixture's own published seed data, committed
in the repo's golden contract — not a per-run secret.

## Prediction vs outcome

- Prediction (relay 86bab08, smoke-d152-predictions.md, V1): PASS, exit 0.
- Observed: workflow conclusion `success`; verify verdict **PASS**,
  exit code **0**; 3 probes authored, 3/3 probe verdicts `pass`;
  `measured_result: false` in the evidence record. MATCHES the prediction.

## Files

- `verify-output.json` — the verify verdict output (verdict, exit code, per-probe results).
- `verify-exit-code.txt` — the propagated verify exit code (0).
- `verify-run.log` — the run's own log as uploaded in the artifact.
- `runs.jsonl` — the evidence record (verify-records/runs.jsonl in the artifact).
- `job.log` — full GitHub job log for the verify job (76,387 bytes, under the 1 MB cap; untrimmed).
