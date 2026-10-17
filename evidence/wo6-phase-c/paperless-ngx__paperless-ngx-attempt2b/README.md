# D61 evidence pack — WO-6 Phase C, candidate #1: paperless-ngx/paperless-ngx (attempt 2b — D155 round-2 completion)

- Run: `39034534273` — workflow `readiness.yml`, `repo=paperless-ngx/paperless-ngx`,
  `attempt=2` (the pass semantics: attempt 1 provisions the side once,
  attempt 2 re-provisions any non-READY side; one artifact carries both),
  `mode=provision`, dispatched 2026-10-17T12:56:32Z from branch
  `feat/wo6-regc-add2-script` at head
  `0977687fe05899afe3f3fa1e93e46f00a84bd259`, completed
  **success** 2026-10-17T13:01:56Z.
- **Dispatch ref's merge-base with main (D155 lesson, stated in every
  package): `6dc1a4df3230578c88549a05f93070d1ab487f60`** — the main tip
  itself: the branch was refreshed by merging main into it (merge
  commit `bde86feb58310a3996d5db28b8406ddaaad98946`), so the dispatch
  ran on a ref that contains current main. `git merge-base
  --is-ancestor 6dc1a4d HEAD` exits 0.
- Why this pack exists: the round-2 READY (run `39033362054`, pack
  `paperless-ngx__paperless-ngx-attempt2/`) was **not accepted**
  (D155): it had been dispatched from the stale #108 branch (tool
  state inside the app tree) and its contract's build string
  differed from D151. This re-run **completes round 2** of the D152
  fix budget (our process slip, not a new paperless blocker); it
  does **not** consume round 3.
- Branch refresh (D155 step a): merge commit `bde86fe` (parents
  `66d7556` + `6dc1a4d`); the single conflict,
  `tests/expected_host_nodeids.txt`, was resolved **only** by
  regenerating the manifest inside the merge commit
  (`scripts/check_test_manifest.py --write`, D141 precedent) — the
  regenerated file equals (main's file ∪ the branch's 55 added
  `test_wo6_regc_add2*.py` nodeids) with main's 4 deletions honored
  (diff against the mechanically constructed expected set: empty);
  foreground check: `OK … full=1203 host=1114 sandbox=89`;
  `scripts/wo6/regc_add2_run.py` still sha256
  `b80013adf2056165733d8a90fbc4c0f73fd4608737f5b2ebce3c998ecfeee636`;
  the 55 wo6 tests pass on the merged tree.
- Contract fix (D155 step b): commit
  `0977687fe05899afe3f3fa1e93e46f00a84bd259`, pushed before the
  dispatch. Contract: `readiness/contracts/paperless-ngx__paperless-ngx.yml`,
  sha256 `0eae66ea96e1406be4ae8824d279702afc64cca0fc09aa0ca7a01eeddd14f36b`.
  The build step is now the D151 string exactly:
  `mkdir -p data/index media/documents/originals media/documents/thumbnails consume`,
  and the provenance comment cites the app's `init-folders/run`
  script's `extra_dirs` (data/index, media/documents/originals,
  media/documents/thumbnails — where paperless writes its search
  index, original documents and thumbnails).
- Candidate: survivor #1 — 46,745 stars, app_path `src` (survivor
  record), registered HEAD `0cb2cdbfdd91a2078236f8851cd310261284591c`.
  Harness commit (row's `harness_sha`):
  `342e18a97dae70ec09ca487532e909a5376e08dd`.
- Prediction: `evidence/wo6-phase-c/predictions.md` (relay commit
  `a3612ff2b4a5eccc22003635e19442a3946ec690`, pushed
  2026-10-17T12:56:23Z, before the dispatch at 12:56:32Z): READY on
  the current-main harness with the full D151 sub-dir set. The
  outcome matched the prediction.
- Artifact: `readiness-paperless-ngx-paperless-ngx-2` (id `12101178441`);
  zip sha256 as downloaded:
  `fd09da5f0f606f0a337038ce3cc32fd5ce862a23711e50f2f14d2032765ad692`.

## Result — READY on attempt 1

Attempt-1 row: unit `WO6-C1`, role `C`, status **`READY`**,
`typed_reason` null, `last_error` null; runtime python 3.14.8
(declared `>=3.11`), package manager `uv`, install plan sha256
`e64bcb869b9d52e4348e0a429b062f370a6abc93e5036ba29e21df4267293871`,
network mode `none+socket`. The attempt-2 rows file is empty — no
non-READY side existed to re-provision.

### Harness freshness evidence (D155 c) — grep counts over the pd-log

- `/pd-home/.pd-tools`: **6** occurrences
- `/pd-home/.venv`: **8** occurrences
- `/checkout/.pd-tools`: **0** occurrences
- `/checkout/.venv`: **0** occurrences

The log shows the current-main layout throughout: the app venv is
created at `/pd-home/.venv` (`python3 -m venv /pd-home/.venv`), the
tools venv at `/pd-home/.pd-tools`, `HOME=/pd-home`,
`UV_PROJECT_ENVIRONMENT=/pd-home/.venv` (uv sync step), and migrate
runs as `/pd-home/.venv/bin/python src/manage.py migrate --noinput`.
(The stale round-2 log's `/checkout/.pd-tools/bin/uv` and
`UV_PROJECT_ENVIRONMENT=/checkout/.venv` are gone.)

### App evidence from the pd-log, in order

- Build step (install step 7) ran exactly:
  `mkdir -p data/index media/documents/originals media/documents/thumbnails consume`
- Migrate (install step 8): **66 migrations `Applying … OK`, 0
  failed** — the full set through `documents`, `paperless_mail`,
  `socialaccount`, etc. `SystemCheckError` occurrences in the log:
  **0** (the round-1 stopper stays cleared).
- The app booted (`Paperless-ngx version: v3.3.1`); the readiness
  poll saw `GET /` → **302** and `GET /accounts/login/?next=/` →
  **200**, and the harness recorded
  `sandbox app READY at http://127.0.0.1:33205`.

READY log: `pd-logs/readiness-WO6-C1-clean-a1.log` (this pack).

### One row difference from the stale-harness round-2 row, named

The current-main harness emits four row fields the stale harness's
round-2 row did not carry (`app_port`, `fixture_template`,
`fixture_render_sha256`, `fixture_render_error`). This row's
`fixture_render_error` reads verbatim: `fixture render failed:
fixture mapping missing fields: repo_name, seed_note,
auth_mechanism, flags_note (our gap)` — the paperless contract
carries no `fixture:` block (it is a provisioning-readiness
contract), and the field does not affect the row's verdict:
`status` is `READY`, `typed_reason` and `last_error` are null. It
is recorded here so the difference is on the record, not
discovered later.

## Contents

- `rows-attempt1.json`, `rows-attempt2.json` — the artifact's rows
  files, as downloaded, unmodified (sha256
  `d4cbafe3e40474e61333e4d47a44d4565c48f64d3de1171b658a9eed688dc868`
  and `72bd68906603ecee896d5b34f44960595bc392095047ec81f916e4094ca8f788`).
- `pd-logs/readiness-WO6-C1-clean-a1.log` — the side's log in full
  (20,326 bytes; no trimming), scrubbed as stated below.

## Redaction

`PAPERLESS_SECRET_KEY`'s contract value (a throwaway committed for
this ephemeral provision) appears in the sandbox command lines in
the log: 10 occurrences, each replaced with
`<redacted:PAPERLESS_SECRET_KEY>`. No other credentials, tokens, or
per-run secrets appear in the pack (grep over the log for JWT-shaped
strings: 0 matches). The rows files needed no redaction.
