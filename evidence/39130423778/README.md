# D61 evidence pack — Cerebras repeat study (D212) — golden node-express-sqlite bug, repeat slot r1

- Run ID: 39130423778
- Workflow: measure (`.github/workflows/measure.yml`), conclusion `success`
- Run URL: https://github.com/wally-dk24/proofdeploy/actions/runs/39130423778
- Dispatch ref: `main` at `24d138ad1b69b1f05734561d174556d7a53bd5b2`
  (the PR #145 merge commit; merge-base with main is itself).
- Unit: measure.yml golden branch — unit_repo=golden/node-express-sqlite,
  bug run, bug_id `smoke-d212-node-express-sqlite-bug-r1`, arm=skill,
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

- Branch: `records/run-39130423778`
- Evidence commit: f91f5c9499ef99640c8baf3510ac093c363f892a
- Score commit: 2f6bf0a207438a887582e5b532eccbff7ea8becb
- sha256 of `runs.jsonl` as downloaded from the evidence commit:
  130eaf89904c6c024ab1367a75c649ff953fb93f8a45db5fecd98925784d20d0
- sha256 of the run-logs zip as downloaded from the Actions API:
  58d24792464caf973dca49565928dc2516eb1c0e278b6383e6a1de6ee0832c36 (job log packed here as `job.log`, 37895 bytes, untrimmed)

## Scrubbing

The harness's own record already carries its redactions
(26 `***REDACTED***` markers). Every file in this pack was
scanned for credential patterns (Groq `gsk_`, Cerebras `csk-`, bearer
tokens): 0 matches. No additional scrubbing was applied. The golden
fixture's seed credentials (seeduser/seedpass) are the fixture's own
published seed data, not a per-run secret.

## Outcome

- Study prediction (relay e46432a, cerebras-repeat-d212.md, committed
  2026-10-18T13:10:54Z, before dispatch): about 3/5 catches per unit
  (band 2–4). G1 39129231446 is reported alongside, not counted.
- Observed: the run's OWN score record carries verdict **`candidate_catch`**,
  candidate_catch [0, 1, 2].
- Per-side probe verdicts (evidence record): fix side probes = 0 PASS, 1 PASS, 2 PASS;
  fix_parent (buggy) side probes = 0 FAIL, 1 FAIL, 2 FAIL.
- Invalid-block count: **0** (`invalid_probe_blocks`).
- `inconclusive_cause`: None.
- Replay: `proofdeploy.replay.replay_verdict` on the evidence record
  (run with the dispatch ref's own code, 24d138ad) returns
  **`candidate_catch`** — identical to the stored verdict.

## The probes (from the record's parsed probes)

- probe 0: GET /notes/1, asserts status == 200, body done == False
- probe 1: GET /notes/2, asserts status == 200, body done == True
- probe 2: GET /notes/${NEW_NOTE_ID}, asserts status == 200, body done == False
