# D61 evidence pack — D180 golden gate run G1 — bug CAUGHT (matches prediction)

- Run ID: 39070612735
- Workflow: measure (`.github/workflows/measure.yml`), job `measure` (117507259011), conclusion `success`
- Run URL: https://github.com/wally-dk24/proofdeploy/actions/runs/39070612735
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
  bug run, bug_id `smoke-d180-node-express-sqlite-bug`, arm=skill,
  target_repo entered as the literal `golden`, base/bug/fix/clean empty.
- Label: D180 golden gate for skill registration v3 — NOT a registered
  measurement.
- `measured_result: false` in the evidence record — CONFIRMED.
- Skill hash in the evidence record — CONFIRMED: `skill_sha256` =
  `e04cb8111c9767149abc371d0ee1b0781d01db9e2baab96f767224ee846f2099`
  (the v3 author copy; the same value appears at
  `bundle_manifest.skill_sha256` and `bundle_manifest.files.skill-author.md`).

## Where the record lives (measure.yml uploads no artifact zip)

measure.yml's durable store is a records branch in the code repo, pushed by
the run itself. There is no artifact zip for this run.

- Branch: `records/run-39070612735`
- Evidence commit: 90c01db61074d2ee7e752660f3d21f1f4d2e910e
  ("evidence: run-pair record fd7e5fca12d54f84a99a21b20d4da68c")
- Score commit: ab2a9406d4a523bca8d04be5a4ea1ae66db677e7
  ("score: bug smoke-d180-node-express-sqlite-bug (fd7e5fca)")
- On the branch, the score record is line 2 of `runs.jsonl` at the score
  commit; it is packed here verbatim as `runs-scored.jsonl`.
- sha256 of `runs.jsonl` as downloaded from the evidence commit:
  597a4e286bc9735eeb92a167b6b7497480e80f974e8aeabe372be8874626274c
- sha256 of the run-logs zip as downloaded from the Actions API:
  e7d277784e6097b0db03b5a4272e7b41cb369ef939b8db14f9d46b59099d9a37
  (contains the job log packed here as `job.log`, 37,239 bytes, untrimmed)

## Scrubbing

The harness's own record already carries its redactions (13
`***REDACTED***` markers). Before committing, every file in this pack was
scanned for credential patterns (Groq API keys `gsk_`, bearer tokens,
canary values): 0 matches. No additional scrubbing was needed and none was
applied. The golden fixture's seed credentials (seeduser/seedpass) appear
inside the record's prompt because they are the fixture's own published
seed data, committed in the repo's golden contract — not a per-run secret.

## Prediction vs outcome — MATCHES

- Prediction (relay 0a7d209, gate-d180-predictions.md, G1): CAUGHT.
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

The author (openai/gpt-oss-120b, arm=skill, 1 attempt) authored 2 probes:

- probe 0: GET /notes/1, asserts status 200 and body `done` == false
- probe 1: GET /notes/2, asserts status 200 and body `done` == true

One line: asserts note1 done=false, note2 done=true — the fixture-truth
polarity (the D146 seed note: id 1 done=false, id 2 done=true). This is
the opposite polarity from the M1/M2 smoke misses, where the author
asserted the buggy side's inverted values; under skill v3 sections
1a/1b the author took the expected behaviour from the fixture, so the
probes pass on the fixed code and fail on the buggy code.
