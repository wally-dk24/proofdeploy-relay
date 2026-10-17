# D61 evidence pack — D152 smoke run V4 — MISS, as predicted by the D176 revised prediction

- Run ID: 39061713964
- Workflow: verify (`.github/workflows/verify.yml`), job `verify` (117484049518)
- Run URL: https://github.com/wally-dk24/proofdeploy/actions/runs/39061713964
- Head SHA: d9fa4acb6dd65beef2583e1c8d6d0740d7a736e9
- Dispatch ref's merge-base with main: d9fa4acb6dd65beef2583e1c8d6d0740d7a736e9
  (dispatched from main itself)
- Unit: golden python-flask-sqlite, BUG side (verify inputs: app=python-flask-sqlite, change=bug)
- Label: smoke (D152) — NOT a registered measurement.

## Artifact

- Name: verify-python-flask-sqlite-bug-39061713964
- sha256 of the artifact zip as downloaded:
  2f0eb7caf07844e763039770fbd6e6c2e82a7c90de131b9376322580677f163e

## Scrubbing

The harness's own records already carry its redactions (`***REDACTED***` in
place of minted bearer tokens and the contract's secret values). Before
committing, every file in this pack was scanned for credential patterns
(Groq API keys, bearer tokens, per-run canary tokens): 0 matches. No
additional scrubbing was needed and none was applied. The golden fixture's
seed credentials appear inside the record's prompt because they are the
fixture's own published seed data, committed in the repo's golden
contract — not a per-run secret.

## Predictions vs outcome

Two predictions stand on record for V4:

- Original (relay 86bab08, smoke-d152-predictions.md, V4): FAIL, exit 1
  (catch). Superseded for V4 only by D176, after V3's miss established the
  single-side cause.
- Revised (D176, committed to the relay at 720646f BEFORE Master approved
  this run): "V4 verify python-flask-sqlite bug side → likely PASS exit 0
  (a miss, same single-side cause as V3, D175); alternative: FAIL exit 1
  if the author uses the fixture truth as the oracle."

- Observed: workflow conclusion `success`; verify verdict **PASS**,
  exit code **0**; 3 probes authored, 3/3 probe verdicts `pass`;
  `measured_result: false`. **This matches the REVISED prediction — a
  miss.** The named alternative (the author using the fixture truth as
  the oracle) did not occur. Per D176, neither outcome was a stop.

## Polarity finding (from the record in this pack)

Yes — the author again asserted the inverted (buggy) values, exactly as
in V3. The buggy side returned note 1 `done: true`, note 2 `done: false`,
and a newly created note `done: true`, and the author's probes asserted
precisely those values:

- probe 0: GET /notes/1 asserts `done == true` (fixture truth: false) → passed
- probe 1: GET /notes/2 asserts `done == false` (fixture truth: true) → passed
- probe 2: creates a note, GET asserts `done == true` (fixture truth for a
  new note: false) → passed

Cause class (D175): **authoring miss, not a harness or infrastructure
failure** — the same single-side cause as V3. Provisioning was READY
(record: `c_provision.status: "ready"`), 3 valid probes were sent and
evaluated (`inconclusive_cause: null`). The author (openai/gpt-oss-120b)
inferred the intended behavior from the change under test; on a bug-side
unit the change IS the defect, so diff-inferred assertions ratified the
bug, and the fixture's stated seed truth in the same prompt ("id 1
(done=false) and id 2 (done=true)") was not used as the oracle. Per the
Master decision (D176, Option A), this is recorded as a known miss;
verify's intent input goes to Product v1 (plan 2.6).

## Files

- `verify-output.json` — the verify verdict output (verdict, exit code, per-probe results).
- `verify-exit-code.txt` — the propagated verify exit code (0).
- `verify-run.log` — the run's own log as uploaded in the artifact.
- `runs.jsonl` — the evidence record (verify-records/runs.jsonl in the artifact).
- `job.log` — full GitHub job log for the verify job (46,027 bytes, under the 1 MB cap; untrimmed).
