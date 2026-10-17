# D61 evidence pack — D152 smoke run M2 — MISS, inverted pattern (REPEAT of the M1 cause, D175)

- Run ID: 39064086675
- Workflow: measure (`.github/workflows/measure.yml`), job `measure` (117489338296)
- Run URL: https://github.com/wally-dk24/proofdeploy/actions/runs/39064086675
- Head SHA: d9fa4acb6dd65beef2583e1c8d6d0740d7a736e9
- Dispatch ref's merge-base with main: d9fa4acb6dd65beef2583e1c8d6d0740d7a736e9
  (dispatched from main itself)
- Unit: measure.yml golden branch — unit_repo=golden/python-flask-sqlite,
  bug run, bug_id `smoke-d152-python-flask-sqlite-bug`, arm=skill,
  target_repo entered as the literal `golden`, base/bug/fix/clean empty
  (inputs recorded in relay 720646f).
- Label: smoke (D152) — NOT a registered measurement.
- `measured_result: false` in the evidence record — CONFIRMED (D167).

## Where the record lives (measure.yml uploads no artifact zip)

measure.yml's durable store is a records branch in the code repo, pushed by
the run itself. There is no artifact zip for this run.

- Branch: `records/run-39064086675`
- Evidence commit: 9ce11a625f3547d6d7ee2226906cfa351c3ad58d
  ("evidence: run-pair record 5032f1fedcab47209f9d29d8d9387a98")
- Score commit: fe3335806d1ad8dea1489efc4408a19aff066156
  ("score: bug smoke-d152-python-flask-sqlite-bug (5032f1fe)")
- On the branch, the score record is line 2 of `runs.jsonl` at the score
  commit; it is packed here verbatim as `runs-scored.jsonl`.
- sha256 of `runs.jsonl` as downloaded from the evidence commit:
  7d25f9dd72efc7a6490531bd2a38d0e2b113dce31adfb560cfbf1ce5052979b4
- sha256 of the run-logs zip as downloaded from the Actions API:
  7bfdc70e90b073797e6db6858bead703dcd5bee4bd1a2eb6ba36821b2a130134
  (contains the job log packed here as `job.log`, 36,916 bytes, untrimmed)

## Scrubbing

The harness's own record already carries its redactions (18 `***REDACTED***`
markers). Before committing, every file in this pack was scanned for
credential patterns (Groq API keys `gsk_`, bearer tokens, per-run canary
values): 0 matches. No additional scrubbing was needed and none was applied.
The golden fixture's seed credentials (seeduser/seedpass) appear inside the
record's prompt because they are the fixture's own published seed data,
committed in the repo's golden contract — not a per-run secret.

## Prediction vs outcome — MISS (same-cause repeat; NOT a new stop, per the D181 ruling)

- Prediction (relay 86bab08, smoke-d152-predictions.md, M2): CAUGHT
  (fix^ fails, fix passes).
- Observed: workflow conclusion `success`, but the run's OWN score record
  (record_type `score`) carries the verdict **`no_catch`** — a MISS with
  the INVERTED pattern: both probes PASS on the buggy side (fix_parent)
  and both FAIL on the fixed side (fix).
- Replay (D152): `proofdeploy.replay.replay_verdict` on the evidence
  record returns `no_catch` — identical to the stored verdict. The record
  replays byte-for-byte; the verdict is real, not a recording error.

## Classification (from the record) — REPEAT of the M1 cause

Sides are labelled correctly — no harness swap. The fix side
(fix_sha 6a3b88d1dd821ae7cb0c4d5d43da4a84e37f700f) behaves per the fixture
truth; the fix_parent side (4bf638c031f29ef33ee870546b44d9c1ff797ad6)
carries the golden bug (the inverted `done` flag). The AUTHOR
(openai/gpt-oss-120b, arm=skill) asserted the buggy side's polarity:

- probe 0: GET /notes/1 asserts `done == true` (fixture truth: false) —
  fix_parent body `{"done":true,"id":1,"title":"seeded open note"}` → pass;
  fix body `{"done":false,"id":1,"title":"seeded open note"}` → FAIL.
- probe 1: GET /notes/2 asserts `done == false` (fixture truth: true) —
  fix_parent body `{"done":false,"id":2,"title":"seeded done note"}` → pass;
  fix body `{"done":true,"id":2,"title":"seeded done note"}` → FAIL.

2 valid probes, 0 invalid blocks, `inconclusive_cause: null`. NOT harness,
NOT environment, NOT INCONCLUSIVE — the same authoring cause as M1 (the
author anchors on the pre-fix side's behavior as the oracle, D175). Per the
reviewer's D181 sequence the smoke continues to M3/M4.
