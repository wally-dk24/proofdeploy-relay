# D61 evidence pack — D152 smoke run M3 — NO FALSE ALARM (matches prediction)

- Run ID: 39064107233
- Workflow: measure (`.github/workflows/measure.yml`), job `measure` (117496095219)
- Run URL: https://github.com/wally-dk24/proofdeploy/actions/runs/39064107233
- Head SHA: d9fa4acb6dd65beef2583e1c8d6d0740d7a736e9
- Dispatch ref's merge-base with main: d9fa4acb6dd65beef2583e1c8d6d0740d7a736e9
  (dispatched from main itself)
- Unit: measure.yml golden branch — unit_repo=golden/node-express-sqlite,
  clean run, clean_id `smoke-d152-node-express-sqlite-clean`, arm=skill,
  target_repo entered as the literal `golden`, base/bug/fix/clean empty
  (inputs recorded in relay 720646f).
- Label: smoke (D152) — NOT a registered measurement.
- `measured_result: false` in the evidence record — CONFIRMED (D167).

## Where the record lives (measure.yml uploads no artifact zip)

measure.yml's durable store is a records branch in the code repo, pushed by
the run itself. There is no artifact zip for this run.

- Branch: `records/run-39064107233`
- Evidence commit: c250b019c95fb2c676589f95941961acd67b1efc
  ("evidence: run-pair record 17ee736ce0554e61b8ec11ba119290ac")
- Score commit: 52fb431f639b0f4da293ab367f3a04ce1e1fc2cd
  ("score: clean smoke-d152-node-express-sqlite-clean (17ee736c)")
- On the branch, the score record is line 2 of `runs.jsonl` at the score
  commit; it is packed here verbatim as `runs-scored.jsonl`.
- sha256 of `runs.jsonl` as downloaded from the evidence commit:
  3bd22a458a2d168d03982833e540f841e47cae2f29f03b345bf7dad6baf81697
- sha256 of the run-logs zip as downloaded from the Actions API:
  e8fdb9acc5f410dcb168ffc193b018336eee214bdd51bf0bf0f2da3769994ff4
  (contains the job log packed here as `job.log`, 36,808 bytes, untrimmed)

## Scrubbing

The harness's own record already carries its redactions (10 `***REDACTED***`
markers). Before committing, every file in this pack was scanned for
credential patterns (Groq API keys `gsk_`, bearer tokens, per-run canary
values): 0 matches. No additional scrubbing was needed and none was applied.
The golden fixture's seed credentials (seeduser/seedpass) appear inside the
record's prompt because they are the fixture's own published seed data,
committed in the repo's golden contract — not a per-run secret.

## Prediction vs outcome — MATCHES

- Prediction (relay 86bab08, smoke-d152-predictions.md, M3): NO FALSE ALARM.
- Observed: workflow conclusion `success`; the run's OWN score record
  (record_type `score`, run_kind `clean`) carries **`false_alarm: false`**
  (clean-run vocabulary: `verdict` is null, `candidate_catch` is empty).
  All 3 probes PASS on the clean side (c_sha
  0ca720d4d7e9786a64de2901fa0fe1b982eb8122):
  - probe 0: GET /notes/1 — status 200, `tag == "general"`, `id == 1`,
    `done == false` (the fixture truth) → pass;
  - probe 1: GET /notes/1 unauthenticated — status 401, body contains
    "unauthorized" → pass;
  - probe 2: GET /notes/999 — status 404, body contains "not found" → pass.
  3 valid probes, 0 invalid blocks, `inconclusive_cause: null`.
- Replay (D152): the bug-pair `replay_verdict` is scoped to bug runs (its
  docstring: "the bug-run verdict") and returns "inconclusive" on a clean
  record by construction — a clean record carries no fix/fix_parent sides.
  The applicable replay — the recorded `c_results` re-run through
  `proofdeploy.replay.replay_side` and re-scored with the registered clean
  scorer `proofdeploy.runpair.score_clean_diff` — returns **False**,
  identical to the stored score record's `false_alarm: false`. The stored
  outcome is reproduced from the record exactly.
