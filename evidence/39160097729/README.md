# D61 evidence pack — WO-6 provision rehearsal (D223)

- **Run ID:** 39160097729 —
  https://github.com/wally-dk24/proofdeploy/actions/runs/39160097729
- **Workflow:** measure.yml (`workflow_dispatch`), dispatched from main
  `a3c9541fefe157d07feb647d3a339ea0380437b5` (PR #148 merge; the dispatch
  ref's merge-base with main is itself) with `dry_run = provision` and the
  D219 inputs unchanged: target_repo
  `https://github.com/paperless-ngx/paperless-ngx`, unit_repo empty
  (contract-store path
  `readiness/contracts/paperless-ngx__paperless-ngx.yml`), arm `skill`,
  bug_id `wo6-paperless-371c4f57`,
  base `3a34bdc7b078486bc85a97ce309a1c8180cdfc88`,
  bug `7dca0bfa6af103b76185aada3ce736b0ac1abc01`,
  fix `371c4f57d3ab208fc261e159fa86ca1dc80cb334`, clean empty,
  public_env_keys `PAPERLESS_CHANNELS_BACKEND PAPERLESS_CACHE_BACKEND`.
- **Workflow conclusion:** failure — by design of the mode: the rehearsal
  exits non-zero unless every side is READY and every rehearsal probe
  passes (item (c) did not pass; see below).
- **Predictions:** `predictions/wo6-rehearsal-d223.md`, relay commit
  `fefd82911150cdaed2d231cc06cedfe3645cba21` at 2026-10-18T19:43:33Z,
  before the dispatch at 2026-10-18T19:43:56Z.
- **Records:** branch `records/run-39160097729` at
  `41ba8d3637b26c310ecc96d29556d0d689cc6557` — one evidence record
  (schema 1.8.0), no score record. `runs.jsonl` here is that file as
  fetched from the branch, sha256
  `a03bfbc03c406e6b102e73979ab5fce500af86c14a885306689a7c25fba6b0a1`.
- **Artifact:** the run uploaded no Actions artifacts. `job.log` is the
  full job log as served by the Actions logs API (zip sha256
  `e750857d7787f1754ae870df71b2bf38f9372ad347627cbd7df5257e2154379c`;
  unzipped text 65,390 bytes, sha256
  `10abc979c581cf9c8320be8aa079ce50d77cbec44d23cd5d42db55ca025e5367`),
  untrimmed (under the 1 MB pack limit, so no cuts were needed).

## Outcome (from the record)

- `dry_run = "provision"`, `measured_result = false`, no score record.
- **Both sides READY** (`fix_parent_provision` and `fix_provision`
  status `ready`).
- **`container_env_keys` = exactly the 6 contract keys:**
  PAPERLESS_CACHE_BACKEND, PAPERLESS_CHANNELS_BACKEND,
  PAPERLESS_DATA_DIR, PAPERLESS_DBENGINE, PAPERLESS_SECRET_KEY,
  PAPERLESS_TOKEN_THROTTLE_RATE.
- **Zero model requests:** `model_request` is the empty object,
  `model_usage` absent, `model_finish_reason` null, no model attempts,
  no raw model response — the mode never read the API key.
- **Rehearsal probes** (same result on BOTH sides):
  - (a) authenticated GET /api/profile/ → **pass**;
  - (b) setup POST /api/tags/ capturing id → GET /api/tags/<id>/ →
    **pass**;
  - (c) db assertion `SELECT COUNT(*) FROM auth_user` →
    **inconclusive, reason `environment`**. The probe's own detail lines
    in the record read, verbatim: `DB query failed: attempt to write a
    readonly database` and `db assertion: query failed or was rejected`.
    (The probe's act request, GET /api/profile/, returned 200 on both
    sides; only the db assertion failed.) This is the failure the
    predictions flagged as most likely: the read-only db read against
    the app's live WAL sqlite file.

## Redaction / scrub

The record itself carries the harness's redaction markers
(`***REDACTED***`) where credentials would appear. On top of that, the
full job log was scanned for the contract's 6 non-public values (the 4
non-public env values — SECRET_KEY, DBENGINE, DATA_DIR,
TOKEN_THROTTLE_RATE — and the contract's auth username and password
values): **0 occurrences of every value**. No credential, token,
password, or API key appears in this pack; nothing further was redacted
by hand, and no file needed trimming.
