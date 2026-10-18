# D61 evidence pack — skill v4 gate (D215) — node-express-sqlite-bug-r2 (run 39141910203)

- Run ID: 39141910203
- Workflow: measure (`.github/workflows/measure.yml`), conclusion `success`
- Run URL: https://github.com/wally-dk24/proofdeploy/actions/runs/39141910203
- Dispatch ref: `feat/d216-skill-v4` at `e07d8b63b091cd29ded520f24838de810342b188`
  (PR #147 head, accepted D217; merge-base with main `37568e4` is main itself).
- Unit: measure.yml golden branch — unit_repo=golden/node-express-sqlite,
  bug run, bug_id `skillv4-gate-node-express-sqlite-bug-r2`, arm=skill, target_repo entered as the literal `golden`,
  base/bug/fix/clean inputs empty (golden materialization supplies the refs).
- Label: skill v4 gate (D215) — NOT a registered measurement.
- `measured_result: false` in the evidence record — CONFIRMED.
- Record schema 1.7.0 (D211 instrumentation live): `model_finish_reason` =
  `stop`; `model_usage` present:
  prompt_tokens=3585, completion_tokens=1708,
  reasoning_tokens=1312, total_tokens=5293.
- Skill hash in the evidence record — CONFIRMED: `skill_sha256` =
  `ff601eb15b09e6e0bf8f48b2037f0ab8b2a1b0c90ac022156f76d0169380b36a` (skill v4).

## Per-record checks

- `model_request.provider` = `cerebras` — CONFIRMED.
- `model_request.model` = `gpt-oss-120b`; endpoint
  `https://api.cerebras.ai/v1/chat/completions` — CONFIRMED.
- Model client pin: `author_prompt_builder_sha256` =
  `a35238a5a38b36ae691ab97861a064938787c5b14a49c380c009067a99f3785b`
  — CONFIRMED. Author prompt pin `fa72a0e6c4a1fa9fb204986e08c8eef4b11fc13786f41389ecf2f42ea8555cf3` — CONFIRMED.
- The author call: `model_attempts` = 1,
  finish_reason `stop`.

## Where the record lives (measure.yml uploads no artifact zip)

- Branch: `records/run-39141910203`
- Evidence commit: 8639b22c1cd8075a1528ef9a0dc231f1fec9be8e
- Score commit: c4075521a24469bd49ab54c91c386104a202d555
- sha256 of `runs.jsonl` as downloaded from the evidence commit:
  d51d5f76c6749e8cc3a0a3aae6029cf52e8a770ebd5951b815869a91c3bd70ce
- sha256 of the run-logs zip as downloaded from the Actions API:
  d2c27c7d9530caf7a080d42f0defc53d494dbd429b1528c8eaf1628571384509 (artifact name: the run's logs archive via the Actions API;
  job log packed here as `job.log`, 37885 bytes).

## Scrubbing

The harness's own record already carries its redactions
(19 `***REDACTED***` markers). Every file in this pack was
scanned for credential patterns (Groq `gsk_`, Cerebras `csk-`, bearer
tokens): 0 matches. No additional scrubbing was applied. The golden
fixture's seed credentials (seeduser/seedpass) are the fixture's own
published seed data, not a per-run secret.

## Outcome

- Gate prediction (relay 5cd40fb4, skillv4-gate-d215.md, committed
  2026-10-18T15:06:21Z, before dispatch): reviewer — about 4/5 per bug
  unit (band 3–5), 0 false alarms most likely.
- Observed: the run's OWN score record carries verdict **`no_catch`**,
  candidate_catch [].
- Per-side probe verdicts (evidence record): fix side probes = 0 FAIL, 1 FAIL, 2 PASS, 3 PASS;
  fix_parent (buggy) side probes = 0 PASS, 1 PASS, 2 PASS, 3 PASS.
- Invalid-block count: **0** (`invalid_probe_blocks`).
- `inconclusive_cause`: None.
- Replay with the dispatch ref's own code (e07d8b6):
  `{"verdict": "no_catch"}` — matches the stored score.

## The probes (from the record's parsed probes)

- probe 0: GET /notes/1, asserts status == 200, body done == True
- probe 1: GET /notes/2, asserts status == 200, body done == False
- probe 2: GET /notes/1, asserts status == 401, body error == unauthorized
- probe 3: GET /notes/9999, asserts status == 404, body error == not found
