# D61 evidence pack — D180 golden gate run G3 — NO FALSE ALARM (matches prediction)

- Run ID: 39070656339
- Workflow: measure (`.github/workflows/measure.yml`), job `measure` (117510612125), conclusion `success`
- Run URL: https://github.com/wally-dk24/proofdeploy/actions/runs/39070656339
- Dispatch ref: branch `feat/skill-registration-v3` at head
  `e7741f759be2096621fe9528382fb9ce552850dd` (dispatched before the #142
  merge, per D187). The record itself confirms the ref:
  `provenance.harness.commit` =
  `e7741f759be2096621fe9528382fb9ce552850dd`,
  `provenance.harness.workflow_ref` =
  `wally-dk24/proofdeploy/.github/workflows/measure.yml@refs/heads/feat/skill-registration-v3`.
- Dispatch ref's merge-base with main: d9fa4acb6dd65beef2583e1c8d6d0740d7a736e9
  (the PR branch is a single commit whose sole parent is d9fa4ac).
- Unit: measure.yml golden branch — unit_repo=golden/node-express-sqlite,
  clean run, clean_id `smoke-d180-node-express-sqlite-clean`, arm=skill,
  target_repo entered as the literal `golden`, base/bug/fix/clean empty.
- Label: D180 golden gate for skill registration v3 — NOT a registered
  measurement.
- `measured_result: false` in the evidence record — CONFIRMED.
- Skill hash in the evidence record — CONFIRMED: `skill_sha256` =
  `e04cb8111c9767149abc371d0ee1b0781d01db9e2baab96f767224ee846f2099`
  (the v3 author copy).

## Where the record lives (measure.yml uploads no artifact zip)

measure.yml's durable store is a records branch in the code repo, pushed by
the run itself. There is no artifact zip for this run.

- Branch: `records/run-39070656339`
- Evidence commit: c02b77c0ea4885cf25aa45f2cea2c00ac95a3f34
  ("evidence: run-pair record 0b8d3954bdbb43b7bb2e02377497aa57")
- Score commit: 0205af6cc80e8210a055d1fc96e13acf0c680091
  ("score: clean smoke-d180-node-express-sqlite-clean (0b8d3954)")
- On the branch, the score record is line 2 of `runs.jsonl` at the score
  commit; it is packed here verbatim as `runs-scored.jsonl`.
- sha256 of `runs.jsonl` as downloaded from the evidence commit:
  45e2c29d8da76ac9f75ad571d919d86f845df2f3c2c910dcdc38001d436b1fd2
- sha256 of the run-logs zip as downloaded from the Actions API:
  dd353274f2373b195b589f5a69349a8e61428f51a29c4ea27501dd3bf38566e1
  (contains the job log packed here as `job.log`, 37,028 bytes, untrimmed)

## Scrubbing

The harness's own record already carries its redactions (12
`***REDACTED***` markers). Before committing, every file in this pack was
scanned for credential patterns (Groq API keys `gsk_`, bearer tokens,
canary values): 0 matches. No additional scrubbing was needed and none was
applied. The golden fixture's seed credentials (seeduser/seedpass) appear
inside the record's prompt because they are the fixture's own published
seed data, committed in the repo's golden contract — not a per-run secret.

## Prediction vs outcome — MATCHES

- Prediction (relay 0a7d209, gate-d180-predictions.md, G3): NO FALSE
  ALARM. Alternatives named in advance: false alarm by an invalid block;
  false alarm by a failing probe.
- Observed: the run's OWN score record (record_type `score`, run_kind
  `clean`) carries **`false_alarm: false`** (verdict null,
  candidate_catch [] — the clean-run vocabulary).
- Per-probe verdicts on the clean side (evidence record `c_results`):
  probes 0–3 = PASS, PASS, PASS, PASS — all sent and evaluated.
- Invalid-block count: **0** (`invalid_probe_blocks` is empty; no probe
  recorded sent:false or inconclusive).
- `inconclusive_cause`: null.
- Replay (clean-run method, as in the smoke packs): the recorded
  `c_results` re-run through `proofdeploy.replay.replay_side` give
  [PASS, PASS, PASS, PASS], and the registered clean scorer
  `proofdeploy.runpair.score_clean_diff` returns **False** — identical
  to the stored score record. (The bug-pair `replay_verdict` is scoped
  to bug runs and does not apply to a clean record.)

## The probes (from the record's parsed probes)

The author (openai/gpt-oss-120b, arm=skill, 1 attempt) authored 4 probes:

- probe 0: GET /notes/1, asserts status 200, body `tag` == "general",
  body `done` == false
- probe 1: GET /notes/1 unauthenticated, asserts status 401, body
  `error` == "unauthorized"
- probe 2: GET /notes/999, asserts status 404, body `error` ==
  "not found"
- probe 3: GET /notes, asserts status 200 and a body pattern excluding
  any `tag` field in the list

One line: asserts note1 tag=general & done=false (fixture truth),
unauthenticated → 401, unknown id → 404, list carries no `tag` field —
all four schema-valid (nested body/json_path form, per skill v3 1b) and
all pass on the clean code.
