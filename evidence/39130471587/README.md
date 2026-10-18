# D61 evidence pack — Cerebras repeat study (D212) — golden python-flask-sqlite bug, repeat slot r2

- Run ID: 39130471587
- Workflow: measure (`.github/workflows/measure.yml`), conclusion `success`
- Run URL: https://github.com/wally-dk24/proofdeploy/actions/runs/39130471587
- Dispatch ref: `main` at `24d138ad1b69b1f05734561d174556d7a53bd5b2`
  (the PR #145 merge commit; merge-base with main is itself).
- Unit: measure.yml golden branch — unit_repo=golden/python-flask-sqlite,
  bug run, bug_id `smoke-d212-python-flask-sqlite-bug-r2`, arm=skill,
  target_repo entered as the literal `golden`, base/bug/fix/clean empty.
- Label: repeat (D212) — NOT a registered measurement.
- `measured_result: false` in the evidence record — CONFIRMED.
- Skill hash in the evidence record — CONFIRMED: `skill_sha256` =
  `e04cb8111c9767149abc371d0ee1b0781d01db9e2baab96f767224ee846f2099`.

## Per-record checks

- `model_request.provider` = `cerebras` — CONFIRMED.
- `model_request.model` = `gpt-oss-120b`; endpoint
  `https://api.cerebras.ai/v1/chat/completions` — CONFIRMED.
- Model client pin: `author_prompt_builder_sha256` =
  `a35238a5a38b36ae691ab97861a064938787c5b14a49c380c009067a99f3785b`
  — CONFIRMED. Author prompt pin `fa72a0e6c4a1fa9fb204986e08c8eef4b11fc13786f41389ecf2f42ea8555cf3` — CONFIRMED.
- `finish_reason` is not carried by record schema 1.6.0 (the D211
  instrumentation gap); the record shows `model_attempts` =
  1 author call(s).

## Where the record lives (measure.yml uploads no artifact zip)

- Branch: `records/run-39130471587`
- Evidence commit: c77c5b07081bc7a0afbca33f9fa910d12b9cb566
- Score commit: fe76bb5c884ef389253d29a39606a2be465bf8f8
- sha256 of `runs.jsonl` as downloaded from the evidence commit:
  37d65fdf252d3345d112fe455634542e6529f868d508a8ed2d638f14014c5e5a
- sha256 of the run-logs zip as downloaded from the Actions API:
  274e18b33e21f0d779e3f6bb7250fdbd272925232efa69e0f51bfb651ee032cf (job log packed here as `job.log`, 37851 bytes, untrimmed)

## Scrubbing

The harness's own record already carries its redactions
(28 `***REDACTED***` markers). Every file in this pack was
scanned for credential patterns (Groq `gsk_`, Cerebras `csk-`, bearer
tokens): 0 matches. No additional scrubbing was applied. The golden
fixture's seed credentials (seeduser/seedpass) are the fixture's own
published seed data, not a per-run secret.

## Outcome

- Study prediction (relay e46432a, cerebras-repeat-d212.md, committed
  2026-10-18T13:10:54Z, before dispatch): about 3/5 catches per unit
  (band 2–4). G1 39129231446 is reported alongside, not counted.
- Observed: the run's OWN score record carries verdict **`candidate_catch`**,
  candidate_catch [0, 1].
- Per-side probe verdicts (evidence record): fix side probes = 0 PASS, 1 PASS, 2 PASS, 3 PASS;
  fix_parent (buggy) side probes = 0 FAIL, 1 FAIL, 2 PASS, 3 PASS.
- Invalid-block count: **0** (`invalid_probe_blocks`).
- `inconclusive_cause`: None.
- Replay: `proofdeploy.replay.replay_verdict` on the evidence record
  (run with the dispatch ref's own code, 24d138ad) returns
  **`candidate_catch`** — identical to the stored verdict.

## The probes (from the record's parsed probes)

- probe 0: GET /notes/1, asserts status == 200, body done == False
- probe 1: GET /notes/2, asserts status == 200, body done == True
- probe 2: GET /notes/1, asserts status == 401, body None == None
- probe 3: GET /notes/999, asserts status == 404, body None == None
