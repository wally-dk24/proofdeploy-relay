# D61 evidence pack — D180 golden gate run G2 — bug CAUGHT (matches prediction)

- Run ID: 39070633706
- Workflow: measure (`.github/workflows/measure.yml`), job `measure` (117508799770), conclusion `success`
- Run URL: https://github.com/wally-dk24/proofdeploy/actions/runs/39070633706
- Dispatch ref: branch `feat/skill-registration-v3` at head
  `e7741f759be2096621fe9528382fb9ce552850dd` (dispatched before the #142
  merge, per D187). The record itself confirms the ref:
  `provenance.harness.commit` =
  `e7741f759be2096621fe9528382fb9ce552850dd`,
  `provenance.harness.workflow_ref` =
  `wally-dk24/proofdeploy/.github/workflows/measure.yml@refs/heads/feat/skill-registration-v3`.
- Dispatch ref's merge-base with main: d9fa4acb6dd65beef2583e1c8d6d0740d7a736e9
  (the PR branch is a single commit whose sole parent is d9fa4ac).
- Unit: measure.yml golden branch — unit_repo=golden/python-flask-sqlite,
  bug run, bug_id `smoke-d180-python-flask-sqlite-bug`, arm=skill,
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

- Branch: `records/run-39070633706`
- Evidence commit: a660474d16b5352f1afb26d321045afdd1d8f6ac
  ("evidence: run-pair record 98ff71c8024947a1aae82029fdb879c3")
- Score commit: 83762198012bb9fcde379ec0198e715c8d1b1dd2
  ("score: bug smoke-d180-python-flask-sqlite-bug (98ff71c8)")
- On the branch, the score record is line 2 of `runs.jsonl` at the score
  commit; it is packed here verbatim as `runs-scored.jsonl`.
- sha256 of `runs.jsonl` as downloaded from the evidence commit:
  95f811abb567a766834ed3139e964e79b3a6046d1b036901635ea30cdfa11d14
- sha256 of the run-logs zip as downloaded from the Actions API:
  41a6754bf51fdd3c4b9f47719f2afe767d3f50f8eb3aff864d84193e63cdddbd
  (contains the job log packed here as `job.log`, 37,242 bytes, untrimmed)

## Scrubbing

The harness's own record already carries its redactions (18
`***REDACTED***` markers). Before committing, every file in this pack was
scanned for credential patterns (Groq API keys `gsk_`, bearer tokens,
canary values): 0 matches. No additional scrubbing was needed and none was
applied. The golden fixture's seed credentials (seeduser/seedpass) appear
inside the record's prompt and probe setup bodies because they are the
fixture's own published seed data, committed in the repo's golden
contract — not a per-run secret.

## Prediction vs outcome — MATCHES

- Prediction (relay 0a7d209, gate-d180-predictions.md, G2): CAUGHT.
  Alternatives named in advance: no_catch with the same cause as M1/M2;
  INCONCLUSIVE probe.
- Observed: the run's OWN score record (record_type `score`, run_kind
  `bug`) carries verdict **`candidate_catch`**, candidate_catch [0, 1].
- Per-side probe verdicts (evidence record): fix side probes 0, 1 =
  PASS, PASS; fix_parent (buggy) side probes 0, 1 = FAIL, FAIL. Both
  probes were sent and evaluated on both sides.
- Invalid-block count: **0** (`invalid_probe_blocks` is empty; no probe
  recorded sent:false or inconclusive).
- `inconclusive_cause`: null.
- Replay (D152 method): `proofdeploy.replay.replay_verdict` on the
  evidence record (run with the dispatch ref's own code, e7741f7) returns
  **`candidate_catch`** — identical to the stored verdict. The record
  reproduces its verdict.

## The probes (from the record's parsed probes)

The author (openai/gpt-oss-120b, arm=skill, 1 attempt) authored 2 probes,
each logging in as seeduser in its setup, then:

- probe 0: GET /notes/2, asserts status 200 and body `done` == true
- probe 1: GET /notes/1, asserts status 200 and body `done` == false

One line: asserts note1 done=false, note2 done=true — the fixture-truth
polarity (the D146 seed note: id 1 done=false, id 2 done=true). As on G1,
the author under skill v3 took the expected behaviour from the fixture,
so the probes pass on the fixed code and fail on the buggy code.
