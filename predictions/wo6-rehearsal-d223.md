# WO-6 provision rehearsal — predictions (D223)

Committed BEFORE dispatch, 2026-10-18. Append-only; these predictions govern
the rehearsal run only (not the blind re-run that may follow it).

## What runs

- measure.yml from proofdeploy main
  `a3c9541fefe157d07feb647d3a339ea0380437b5` (the PR #148 merge; the dispatch
  ref's merge-base with main is itself).
- `dry_run = provision`, with the D219 inputs unchanged:
  target_repo `https://github.com/paperless-ngx/paperless-ngx`, unit_repo
  EMPTY (contract-store path
  `readiness/contracts/paperless-ngx__paperless-ngx.yml`), arm `skill`,
  bug_id `wo6-paperless-371c4f57`,
  base_ref `3a34bdc7b078486bc85a97ce309a1c8180cdfc88`,
  bug_ref `7dca0bfa6af103b76185aada3ce736b0ac1abc01`,
  fix_ref `371c4f57d3ab208fc261e159fa86ca1dc80cb334`, clean_ref empty,
  public_env_keys `PAPERLESS_CHANNELS_BACKEND PAPERLESS_CACHE_BACKEND`.
- The mode: bundle → leak gate → provision BOTH sides through the measured
  code → seed → run the committed rehearsal probe set
  (`readiness/rehearsal/paperless-ngx__paperless-ngx.json`) through the real
  executor on both sides → record → stop. It never scores and is never a
  measured result.

## Predictions

1. **Both sides READY** through the measured provisioning path (the D220 env
   fix delivers the contract's full env to the install and app containers,
   as readiness does).
2. **`container_env_keys` = exactly the 6 contract keys:**
   `PAPERLESS_SECRET_KEY`, `PAPERLESS_DBENGINE`, `PAPERLESS_DATA_DIR`,
   `PAPERLESS_TOKEN_THROTTLE_RATE`, `PAPERLESS_CHANNELS_BACKEND`,
   `PAPERLESS_CACHE_BACKEND`.
3. **Rehearsal probes PASS on BOTH sides:**
   - (a) authenticated `GET /api/profile/` → status 200;
   - (b) setup `POST /api/tags/` with body `{"name": "pd-rehearsal"}`
     capturing `id`, then act `GET /api/tags/${id}/` → status 200;
   - (c) db assertion `SELECT COUNT(*) FROM auth_user` → count ≥ 1.
4. **Zero model requests** — the rehearsal mode never reads the API key and
   never calls the model; the record carries no model request/usage and
   `dry_run = "provision"`, `measured_result = false`, and no score record.
5. **No secret value anywhere in the job log** — every non-public contract
   env value is registered for redaction; a scan of the full log for the
   contract's non-public values finds zero occurrences.

## Item most likely to fail

**(c), the db assertion.** The executor reads the app's live sqlite file
read-only from the host while the container holds it; paperless forces
`PRAGMA journal_mode=WAL`, so a read-only open against a WAL database with
a hot `-wal` written by the userns-remapped container can fail, which would
surface as an INCONCLUSIVE (environment-class) db probe rather than rows.
Items (a) and the provisioning itself are backed by the earlier auth proof
and readiness runs; (b) is the first write+capture probe against this app
and is the second-most-likely failure.
