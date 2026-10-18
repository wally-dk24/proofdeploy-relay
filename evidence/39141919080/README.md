# D61 evidence pack — skill v4 gate (D215) — node-express-sqlite-bug-r3 (run 39141919080)

- Run ID: 39141919080
- Workflow: measure (`.github/workflows/measure.yml`), conclusion `success`
- Run URL: https://github.com/wally-dk24/proofdeploy/actions/runs/39141919080
- Dispatch ref: `feat/d216-skill-v4` at `e07d8b63b091cd29ded520f24838de810342b188`
  (PR #147 head, accepted D217; merge-base with main `37568e4` is main itself).
- Unit: measure.yml golden branch — unit_repo=golden/node-express-sqlite,
  bug run, bug_id `skillv4-gate-node-express-sqlite-bug-r3`, arm=skill, target_repo entered as the literal `golden`,
  base/bug/fix/clean inputs empty (golden materialization supplies the refs).
- Label: skill v4 gate (D215) — NOT a registered measurement.
- `measured_result: false` in the evidence record — CONFIRMED.
- Record schema 1.7.0 (D211 instrumentation live): `model_finish_reason` =
  `stop`; `model_usage` present:
  prompt_tokens=3585, completion_tokens=1918,
  reasoning_tokens=1523, total_tokens=5503.
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

- Branch: `records/run-39141919080`
- Evidence commit: 48841ce181179fdd277e9a5673bf04c045a10bec
- Score commit: 15f9e710a6d14de8c115bab8ef441481070fdcbd
- sha256 of `runs.jsonl` as downloaded from the evidence commit:
  70103086cab5fe830bcc832c08453977c2e0b8837011e165de5da7d947d6e2d3
- sha256 of the run-logs zip as downloaded from the Actions API:
  5a885fbf2aa1aafeb529196e0b0580a9a6dc294422bca8d170d140828600104e (artifact name: the run's logs archive via the Actions API;
  job log packed here as `job.log`, 38031 bytes).

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
- Observed: the run's OWN score record carries verdict **`candidate_catch`**,
  candidate_catch [0, 1, 2].
- Per-side probe verdicts (evidence record): fix side probes = 0 PASS, 1 PASS, 2 PASS;
  fix_parent (buggy) side probes = 0 FAIL, 1 FAIL, 2 FAIL.
- Invalid-block count: **0** (`invalid_probe_blocks`).
- `inconclusive_cause`: None.
- Replay with the dispatch ref's own code (e07d8b6):
  `{"verdict": "candidate_catch"}` — matches the stored score.

## The probes (from the record's parsed probes)

- probe 0: GET /notes/1, asserts status == 200, body done == False
- probe 1: GET /notes/2, asserts status == 200, body done == True
- probe 2: GET /notes/${NOTE_ID}, asserts status == 200, body done == False
