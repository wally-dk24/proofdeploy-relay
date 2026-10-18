# D61 evidence pack — Cerebras repeat study (D212) — golden node-express-sqlite bug, repeat slot r5

- Run ID: 39130454407
- Workflow: measure (`.github/workflows/measure.yml`), conclusion `success`
- Run URL: https://github.com/wally-dk24/proofdeploy/actions/runs/39130454407
- Dispatch ref: `main` at `24d138ad1b69b1f05734561d174556d7a53bd5b2`
  (the PR #145 merge commit; merge-base with main is itself).
- Unit: measure.yml golden branch — unit_repo=golden/node-express-sqlite,
  bug run, bug_id `smoke-d212-node-express-sqlite-bug-r5`, arm=skill,
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

- Branch: `records/run-39130454407`
- Evidence commit: d4e513195e11c809e473a4352d247319a8f71efd
- Score commit: 95b5fb7ea2fde980346eaa70229235093b9fac8a
- sha256 of `runs.jsonl` as downloaded from the evidence commit:
  f91b1de96fb2315e15583be26cc112fbc7493e38d79879efe0ae1256deb3f593
- sha256 of the run-logs zip as downloaded from the Actions API:
  be9bf91cf38ef5c11e0d92d5872b0f6cfc0d51246343d709e296c3bf133448bd (job log packed here as `job.log`, 37851 bytes, untrimmed)

## Scrubbing

The harness's own record already carries its redactions
(13 `***REDACTED***` markers). Every file in this pack was
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
- Per-side probe verdicts (evidence record): fix side probes = 0 PASS, 1 PASS;
  fix_parent (buggy) side probes = 0 FAIL, 1 FAIL.
- Invalid-block count: **0** (`invalid_probe_blocks`).
- `inconclusive_cause`: None.
- Replay: `proofdeploy.replay.replay_verdict` on the evidence record
  (run with the dispatch ref's own code, 24d138ad) returns
  **`candidate_catch`** — identical to the stored verdict.

## The probes (from the record's parsed probes)

- probe 0: GET /notes/2, asserts status == 200, body id == 2, body done == True
- probe 1: GET /notes/1, asserts status == 200, body id == 1, body done == False
