# D61 evidence pack — D152 smoke run M1 — STOP RULE FIRED (miss, inverted pattern)

- Run ID: 39064060901
- Workflow: measure (`.github/workflows/measure.yml`), job `measure` (117487870968)
- Run URL: https://github.com/wally-dk24/proofdeploy/actions/runs/39064060901
- Head SHA: d9fa4acb6dd65beef2583e1c8d6d0740d7a736e9
- Dispatch ref's merge-base with main: d9fa4acb6dd65beef2583e1c8d6d0740d7a736e9
  (dispatched from main itself)
- Unit: measure.yml golden branch — unit_repo=golden/node-express-sqlite,
  bug run, bug_id `smoke-d152-node-express-sqlite-bug`, arm=skill,
  target_repo entered as the literal `golden`, base/bug/fix/clean empty
  (inputs recorded in relay 720646f).
- Label: smoke (D152) — NOT a registered measurement.
- `measured_result: false` in the evidence record — CONFIRMED (D167).

## Where the record lives (measure.yml uploads no artifact zip)

measure.yml's durable store is a records branch in the code repo, pushed by
the run itself. There is no artifact zip for this run.

- Branch: `records/run-39064060901`
- Evidence commit: a765e1be236987ecde89927f1a8629b2a4db7d69
  ("evidence: run-pair record ff137a648ec64c6faaa318517057fed4")
- Score commit: 72275e3831836c3ae95bfdfd2ef24c27d90d32c0
  ("score: bug smoke-d152-node-express-sqlite-bug (ff137a64)")
- sha256 of `runs.jsonl` as downloaded from the evidence commit:
  d188ba2d5bb8a907746c29dd896243f5de1dde4c66aefefb17cb9664d96fb20c
- sha256 of the run-logs zip as downloaded from the Actions API:
  c6c6b09ee1a9a5f34126a625f583224d310ce407d85750b6405b10e30221c257
  (contains the job log packed here as `job.log`)

## Scrubbing

The harness's own record already carries its redactions (15 `***REDACTED***`
markers). Before committing, every file in this pack was scanned for
credential patterns (Groq API keys `gsk_`, bearer tokens, per-run canary
values): 0 matches. No additional scrubbing was needed and none was applied.
The golden fixture's seed credentials (seeduser/seedpass) appear inside the
record's prompt because they are the fixture's own published seed data,
committed in the repo's golden contract — not a per-run secret.

## Prediction vs outcome — DEVIATION (stop rule)

- Prediction (relay 86bab08, smoke-d152-predictions.md, M1): CAUGHT
  (fix^ fails, fix passes).
- Observed: workflow conclusion `success`, but the run's OWN score record
  (runs-scored.jsonl line 1, record_type `score`) carries the verdict
  **`no_catch`** — a MISS with the INVERTED pattern: all 3 probes PASS on
  the buggy side (fix_parent) and probes 0–1 FAIL on the fixed side (fix).
- Replay (D152): `proofdeploy.replay.replay_verdict` on the evidence record
  returns `no_catch` — identical to the stored verdict. The record replays
  byte-for-byte; the verdict is real, not a recording error.

## Classification (from the record)

Sides are labelled correctly — no harness swap. The fix side
(fix_sha 81038c8ecee5019dcfd79215bfa283184e4eeac1) behaves per the fixture
truth; the fix_parent side (56af13171d5853ce59a7e806530bb418ffa95a29)
carries the golden bug (the inverted `done` flag). The AUTHOR asserted the
buggy side's polarity (raw from the record):

- probe 0 asserts GET /notes/1 `done == true` (fixture truth: false) —
  fix_parent body `{"id":1,"title":"seeded open note","done":true}` → pass;
  fix body `{"id":1,"title":"seeded open note","done":false}` → FAIL.
- probe 1 asserts GET /notes/2 `done == false` (fixture truth: true) —
  fix_parent body `{"id":2,"title":"seeded done note","done":false}` → pass;
  fix body `{"id":2,"title":"seeded done note","done":true}` → FAIL.
- probe 2 (unauthenticated → 401 "unauthorized") is polarity-neutral —
  pass on both sides.

Cause class: AUTHORING miss — the same family as the V3/V4 verify misses
(D175): shown the pair, the author (openai/gpt-oss-120b, arm=skill) asserted
the pre-fix side's behavior as the expected behavior, so its probes agreed
with the bug and disagreed with the fix, and the registered scorer
mechanically returned `no_catch`. NOT harness, NOT environment, NOT
INCONCLUSIVE: both sides provisioned and executed, 3 valid probes,
0 invalid blocks, `inconclusive_cause: null`.

Per the D152 stop rule, M2–M4 were NOT approved after this result; they
remain queued/unapproved in GitHub pending the reviewer's ruling.

## Files

- `runs.jsonl` — the evidence record (from the evidence commit above).
- `runs-scored.jsonl` — evidence record + the score record (verdict line).
- `job.log` — full GitHub job log for the measure job (36,910 bytes,
  under the 1 MB cap; untrimmed).
