# D61 evidence pack — D152 smoke run M4 — FALSE ALARM on the clean run (NEW deviation class)

- Run ID: 39064127875
- Workflow: measure (`.github/workflows/measure.yml`), job `measure` (117497678536)
- Run URL: https://github.com/wally-dk24/proofdeploy/actions/runs/39064127875
- Head SHA: d9fa4acb6dd65beef2583e1c8d6d0740d7a736e9
- Dispatch ref's merge-base with main: d9fa4acb6dd65beef2583e1c8d6d0740d7a736e9
  (dispatched from main itself)
- Unit: measure.yml golden branch — unit_repo=golden/python-flask-sqlite,
  clean run, clean_id `smoke-d152-python-flask-sqlite-clean`, arm=skill,
  target_repo entered as the literal `golden`, base/bug/fix/clean empty
  (inputs recorded in relay 720646f).
- Label: smoke (D152) — NOT a registered measurement.
- `measured_result: false` in the evidence record — CONFIRMED (D167).

## Where the record lives (measure.yml uploads no artifact zip)

measure.yml's durable store is a records branch in the code repo, pushed by
the run itself. There is no artifact zip for this run.

- Branch: `records/run-39064127875`
- Evidence commit: 96080233186a2dd9b6042868250b7e528e26edfd
  ("evidence: run-pair record b303ace7d95242199b3c35fc6cdb02c9")
- Score commit: 483f2965c91a96eea53b4254c7b2381417b23aed
  ("score: clean smoke-d152-python-flask-sqlite-clean (b303ace7)")
- On the branch, the score record is line 2 of `runs.jsonl` at the score
  commit; it is packed here verbatim as `runs-scored.jsonl`.
- sha256 of `runs.jsonl` as downloaded from the evidence commit:
  188715361b6fa1020243333783a1bc9a1d061fd2b08707ea40b2a86d951c67e6
- sha256 of the run-logs zip as downloaded from the Actions API:
  ac2be97e8d85aafba2becb657c67c6c96d590bdef1ff4cfbe31e55296add8f6d
  (contains the job log packed here as `job.log`, 36,807 bytes, untrimmed)

## Scrubbing

The harness's own record already carries its redactions (8 `***REDACTED***`
markers). Before committing, every file in this pack was scanned for
credential patterns (Groq API keys `gsk_`, bearer tokens, per-run canary
values): 0 matches. No additional scrubbing was needed and none was applied.
The golden fixture's seed credentials (seeduser/seedpass) appear inside the
record's prompt because they are the fixture's own published seed data,
committed in the repo's golden contract — not a per-run secret.

## Prediction vs outcome — DEVIATION (false alarm; NEW cause class)

- Prediction (relay 86bab08, smoke-d152-predictions.md, M4): NO FALSE ALARM.
- Observed: workflow conclusion `success`; the run's OWN score record
  (record_type `score`, run_kind `clean`) carries **`false_alarm: true`**.
- Replay (D152): the recorded `c_results` re-run through
  `proofdeploy.replay.replay_side` give [PASS, PASS, INCONCLUSIVE], and
  the registered clean scorer `proofdeploy.runpair.score_clean_diff`
  returns **True** — identical to the stored score record. The record
  reproduces its outcome exactly; this is a real scored false alarm, not
  a recording error. (The bug-pair `replay_verdict` is scoped to bug runs
  and does not apply to a clean record.)

## Classification (from the record) — author output-format defect → inconclusive probe → scorer counts it as a false alarm

NOT the M1/M2 polarity cause, NOT the app misbehaving, NOT environment.
The chain, each link verbatim from the evidence record:

1. The author (openai/gpt-oss-120b, arm=skill, 1 attempt) wrote 3 probe
   blocks. Block 0 — the substantive probe (GET /notes/1 asserting
   status 200, `tag == "general"`, `id == 1`, `done == false`) — used
   the flat assert form `"type": "json_path"`, which the probe schema
   rejects. The record's `invalid_probe_blocks` carries it with
   `rejection_reason`: "block 0: assert[1].type must be one of
   ['body', 'db', 'header', 'status'], got 'json_path'". (In M3 the same
   author used the correct nested `"type": "body"` + `"json_path"` form
   and the block was accepted.)
2. The rejected block was recorded as c_result probe 2 with verdict
   **inconclusive**, `sent: false`, `assertions_evaluated: false`,
   detail: "author output block 0 rejected, never sent".
3. The two probes actually sent both PASSED (probe 0: unauthenticated
   GET /notes/1 → 401 `{"error":"unauthorized"}`; probe 1: GET
   /notes/999 → 404 `{"error":"not found"}`) — the clean app behaved
   correctly on every request it received.
4. The registered clean scorer counts any FAIL **or INCONCLUSIVE** probe
   as a false alarm (`score_clean_diff`: `any(r.verdict in (FAIL,
   INCONCLUSIVE))`), so the never-sent, schema-rejected block alone
   produced `false_alarm: true`.

Run-level fields: `inconclusive_cause: null` (the run did not go globally
inconclusive), 2 valid parsed probes, 1 invalid block, c_sha
9b21627fca8826644c97751ff0ba269dba12bbec. Whether a never-sent,
schema-invalid author block should be able to raise a false alarm on a
clean unit is a scorer-design question — recorded here for the reviewer;
the smoke sequence M2–M4 is complete with this run.
