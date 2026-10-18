# D61 evidence pack — Cerebras golden gate run G1 — bug NOT caught (MISS; gate stops)

- Run ID: 39129231446
- Workflow: measure (`.github/workflows/measure.yml`), job `measure` (117680778683), conclusion `success`
- Run URL: https://github.com/wally-dk24/proofdeploy/actions/runs/39129231446
- Dispatch ref: `main` at `24d138ad1b69b1f05734561d174556d7a53bd5b2`
  (the PR #145 merge commit). The record itself confirms the ref:
  `provenance.harness.commit` =
  `24d138ad1b69b1f05734561d174556d7a53bd5b2`,
  `provenance.harness.workflow_ref` =
  `wally-dk24/proofdeploy/.github/workflows/measure.yml@refs/heads/main`.
- Dispatch ref's merge-base with main: 24d138ad1b69b1f05734561d174556d7a53bd5b2
  (the dispatch ref IS main; merge-base is itself).
- Unit: measure.yml golden branch — unit_repo=golden/node-express-sqlite,
  bug run, bug_id `smoke-d180-node-express-sqlite-bug-cerebras`, arm=skill,
  target_repo entered as the literal `golden`, base/bug/fix/clean empty.
- Label: Cerebras golden gate for the D206 provider amendment (plan
  1.5(d); D206(4), D209) — NOT a registered measurement.
- `measured_result: false` in the evidence record — CONFIRMED.
- Skill hash in the evidence record — CONFIRMED: `skill_sha256` =
  `e04cb8111c9767149abc371d0ee1b0781d01db9e2baab96f767224ee846f2099`
  (the same value appears at `bundle_manifest.skill_sha256` and
  `bundle_manifest.files.skill-author.md`).

## Per-record checks required by the gate ruling

- `model_request.provider` = `cerebras` — CONFIRMED.
- `model_request.model` = `gpt-oss-120b` (top-level `model_id` agrees);
  `model_request.endpoint` =
  `https://api.cerebras.ai/v1/chat/completions`;
  `model_request.reasoning_effort` = null, `seed` = null, `tools` = null,
  `temperature` = 0.2; `credential_source` = `env` — CONFIRMED.
- Model client pin: `author_prompt_builder_sha256` =
  `a35238a5a38b36ae691ab97861a064938787c5b14a49c380c009067a99f3785b`
  — CONFIRMED (the D206 client pin). Author prompt pin
  `author_prompt_sha256` =
  `fa72a0e6c4a1fa9fb204986e08c8eef4b11fc13786f41389ecf2f42ea8555cf3`
  (prompt v7) — CONFIRMED.
- `finish_reason`: the gate ruling asks for every response to show
  `finish_reason: stop`. This value is NOT present in the evidence
  record (record schema 1.6.0 carries no finish_reason field) and does
  not appear anywhere in the job log either, so it cannot be confirmed
  from the run's artifacts. What the record does show: exactly one
  author call (`model_attempts` = 1), whose response parsed into 3
  valid probes.

## Where the record lives (measure.yml uploads no artifact zip)

measure.yml's durable store is a records branch in the code repo, pushed
by the run itself. There is no artifact zip for this run.

- Branch: `records/run-39129231446`
- Evidence commit: ae184afad7c4912d2bab8649c576a5ac33c29381
  ("evidence: run-pair record 71fe939b795d4a91b00910f2bc59eca5")
- Score commit: 808962c442da81d6128732c703569b16fbfe9fbe
  ("score: bug smoke-d180-node-express-sqlite-bug-cerebras (71fe939b)")
- On the branch, the score record is line 2 of `runs.jsonl` at the score
  commit; it is packed here verbatim as `runs-scored.jsonl`.
- sha256 of `runs.jsonl` as downloaded from the evidence commit:
  3ec1d31dad2adf87efea3b1d35dac5c5a764fa740f820a7ed42a78b7361e63da
- sha256 of the run-logs zip as downloaded from the Actions API:
  e48c9c387ac928ae5eff9c63e23008310ddb18d90802c5e03c565fb7478f911d
  (contains the job log packed here as `job.log`, 37,753 bytes, untrimmed)

## Scrubbing

The harness's own record already carries its redactions (15
`***REDACTED***` markers). Before committing, every file in this pack was
scanned for credential patterns (Groq API keys `gsk_`, Cerebras API keys
`csk-`, bearer tokens): 0 matches. No additional scrubbing was needed and
none was applied. The golden fixture's seed credentials
(seeduser/seedpass) appear inside the record's prompt because they are
the fixture's own published seed data, committed in the repo's golden
contract — not a per-run secret.

## Prediction vs outcome — MISS

- Prediction (relay 777ee626, cerebras-gate-predictions.md, committed
  2026-10-18T12:53:44Z, before dispatch): candidate_catch.
- Observed: the run's OWN score record (record_type `score`, run_kind
  `bug`) carries verdict **`no_catch`**, candidate_catch [].
- Per-side probe verdicts (evidence record): fix side probes 0, 1, 2 =
  FAIL, FAIL, PASS; fix_parent (buggy) side probes 0, 1, 2 = PASS, PASS,
  PASS. All three probes were sent and evaluated on both sides.
- Invalid-block count: **0** (`invalid_probe_blocks` is empty).
- `inconclusive_cause`: null.
- Replay: `proofdeploy.replay.replay_verdict` on the evidence record
  (run with the dispatch ref's own code, 24d138ad) returns
  **`no_catch`** — identical to the stored verdict. The record
  reproduces its verdict.

## The probes (from the record's parsed probes)

The author (gpt-oss-120b via Cerebras, arm=skill, 1 attempt) authored 3
probes:

- probe 0: GET /notes/1, asserts status 200 and body `done` == true
- probe 1: GET /notes/2, asserts status 200 and body `done` == false
- probe 2: GET /notes/1, asserts status 401

One line: probes 0 and 1 assert the INVERTED values — the fixture truth
(the D146 seed note) is id 1 done=false and id 2 done=true, so both
substantive probes pass on the buggy code and fail on the fixed code;
probe 2 passes on both sides. This is the D175 single-side-author
inversion recurring on Cerebras, the opposite polarity from the D180
Groq-era catch on the same app under the same skill v3 — the divergence
the signed D206 amendment named in advance ("same weights is not the
same as identical outputs"). Per the gate rule (D206(4)): a miss stops
the gate — G2–G4 were not dispatched; no tuning, no re-run.
