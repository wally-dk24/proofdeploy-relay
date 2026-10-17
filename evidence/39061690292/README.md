# D61 evidence pack — D152 smoke run V3 — ⚠️ PREDICTION MISSED (STOP RULE FIRED)

- Run ID: 39061690292
- Workflow: verify (`.github/workflows/verify.yml`), job `verify` (117483078960)
- Run URL: https://github.com/wally-dk24/proofdeploy/actions/runs/39061690292
- Head SHA: d9fa4acb6dd65beef2583e1c8d6d0740d7a736e9
- Dispatch ref's merge-base with main: d9fa4acb6dd65beef2583e1c8d6d0740d7a736e9
  (dispatched from main itself)
- Unit: golden node-express-sqlite, BUG side (verify inputs: app=node-express-sqlite, change=bug)
- Label: smoke (D152) — NOT a registered measurement.

## Artifact

- Name: verify-node-express-sqlite-bug-39061690292
- sha256 of the artifact zip as downloaded:
  5c6da08a35042696159fda0bd3d123b1b892e58e7f9179107c59a57101827795

## Scrubbing

The harness's own records already carry its redactions (`***REDACTED***` in
place of minted bearer tokens and the contract's secret values). Before
committing, every file in this pack was scanned for credential patterns
(Groq API keys, bearer tokens, per-run canary tokens): 0 matches. No
additional scrubbing was needed and none was applied. The golden fixture's
seed credentials appear inside the record's prompt because they are the
fixture's own published seed data, committed in the repo's golden
contract — not a per-run secret.

## Prediction vs outcome — DEVIATION

- Prediction (relay 86bab08, smoke-d152-predictions.md, V3): FAIL, exit 1 (catch).
- Observed: workflow conclusion `success`; verify verdict **PASS**,
  exit code **0**; 3 probes authored, 3/3 probe verdicts `pass`;
  `measured_result: false`. **This is a MISS — the seeded bug verified clean.**

## Classification by cause (from the record in this pack)

The golden bug: in `GET /notes/:id`, `done: note.done === 1` was changed to
`done: note.done !== 1`, inverting the `done` flag. The diff presented to
the author (record prompt, `runs.jsonl`) is exactly that one-line change.

The author (openai/gpt-oss-120b) had the fixture's ground truth in the same
prompt — "id 1 (done=false) and id 2 (done=true)" — but authored probes
asserting the INVERTED (buggy) values:

- probe 0: GET /notes/1 asserts `done == true` (fixture truth: false) → passed
- probe 1: GET /notes/2 asserts `done == false` (fixture truth: true) → passed
- probe 2: creates a note (stored done=0), GET asserts `done == true` → passed

Cause class: **authoring miss, not a harness or infrastructure failure.**
Provisioning was READY, the leak check passed, 3 valid probes were sent
and evaluated (`inconclusive_cause: null`). The author inferred the
intended behavior from the diff itself; on a bug-side unit the diff IS
the defect, so diff-inferred assertions ratified the bug instead of
catching it, and the fixture's stated seed truth was not used as the
oracle. (Contrast V1, run 39061594612: shown the fix diff, the same model
authored probes matching the fixture truth, which passed on fixed code.)

Per the D152 stop rule, shepherding stopped here: V4 (run 39061713964)
was left waiting at the approval gate, unapproved, and M1–M4 were not
dispatched by this shepherd.

## Files

- `verify-output.json` — the verify verdict output (verdict, exit code, per-probe results).
- `verify-exit-code.txt` — the propagated verify exit code (0).
- `verify-run.log` — the run's own log as uploaded in the artifact.
- `runs.jsonl` — the evidence record (verify-records/runs.jsonl in the artifact).
- `job.log` — full GitHub job log for the verify job (76,384 bytes, under the 1 MB cap; untrimmed).
