# D61 evidence pack — D152 smoke run V2

- Run ID: 39061653324
- Workflow: verify (`.github/workflows/verify.yml`), job `verify` (117481998740)
- Run URL: https://github.com/wally-dk24/proofdeploy/actions/runs/39061653324
- Head SHA: d9fa4acb6dd65beef2583e1c8d6d0740d7a736e9
- Dispatch ref's merge-base with main: d9fa4acb6dd65beef2583e1c8d6d0740d7a736e9
  (dispatched from main itself)
- Unit: golden python-flask-sqlite, FIX side (verify inputs: app=python-flask-sqlite, change=fix)
- Label: smoke (D152) — NOT a registered measurement.

## Artifact

- Name: verify-python-flask-sqlite-fix-39061653324
- sha256 of the artifact zip as downloaded:
  a41c0496703edfd458a41faa36554d8b26c91580ebb970f642ae3a2952332ab4

## Scrubbing

The harness's own records already carry its redactions (`***REDACTED***` in
place of minted bearer tokens and the contract's secret values). Before
committing, every file in this pack was scanned for credential patterns
(Groq API keys, bearer tokens, per-run canary tokens): 0 matches. No
additional scrubbing was needed and none was applied. The golden fixture's
seed credentials appear inside the record's prompt because they are the
fixture's own published seed data, committed in the repo's golden
contract — not a per-run secret.

## Prediction vs outcome

- Prediction (relay 86bab08, smoke-d152-predictions.md, V2): PASS, exit 0.
- Observed: workflow conclusion `success`; verify verdict **PASS**,
  exit code **0**; 2 probes authored, 2/2 probe verdicts `pass`;
  `measured_result: false` in the evidence record. MATCHES the prediction.

## Files

- `verify-output.json` — the verify verdict output (verdict, exit code, per-probe results).
- `verify-exit-code.txt` — the propagated verify exit code (0).
- `verify-run.log` — the run's own log as uploaded in the artifact.
- `runs.jsonl` — the evidence record (verify-records/runs.jsonl in the artifact).
- `job.log` — full GitHub job log for the verify job (69,517 bytes, under the 1 MB cap; untrimmed).
