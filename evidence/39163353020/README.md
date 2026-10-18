# D61 evidence pack — WO-6 provision rehearsal #2 (D226)

- **Run ID:** 39163353020 — https://github.com/wally-dk24/proofdeploy/actions/runs/39163353020
- **Workflow:** measure.yml (measure), dispatched from main
- **Head SHA:** e8bf530bd2a3df06286d23f305966aa59a23a121 (PR #149 merge commit; D225 executor db snapshot-copy read)
- **Mode:** dry_run = provision; D219 inputs unchanged (target paperless-ngx/paperless-ngx via the contract store, arm skill, bug wo6-paperless-371c4f57, public_env_keys = the 2 D203 backend keys)
- **Workflow conclusion:** success (the rehearsal CLI exits non-zero unless every item passes)
- **Predictions:** predictions/wo6-rehearsal-2-d226.md @ relay 0badaa58043839430b69b89a06d82032b0c08af2 (committed 2026-10-18T20:26:41Z, before the 20:27:05Z dispatch)

## Files

- `runs.jsonl` — the run's evidence record, copied byte-identical from branch `records/run-39163353020`. sha256 `ad404bbf3f131be91728a3312860c933af1da9c376a2d2e840c70d2f7c251fdb`, 108,094 bytes.
- `job.log` — the full job log, untrimmed (43,185 bytes), extracted from the Actions logs zip for this run (zip sha256 `e75e21bcd8b158dfde7abc2e63659a46bddb8a6425ea61f39f26dffa89804da3`). The run uploaded no artifacts (artifact count 0).

## Verified from the record

- schema 1.8.0; `dry_run` = "provision"; `measured_result` = false; **no score record**.
- Both sides provision **ready** (fix^ and fix).
- `container_env_keys` = exactly the 6 contract keys (PAPERLESS_SECRET_KEY, PAPERLESS_DBENGINE, PAPERLESS_DATA_DIR, PAPERLESS_TOKEN_THROTTLE_RATE, PAPERLESS_CHANNELS_BACKEND, PAPERLESS_CACHE_BACKEND).
- Rehearsal probes — **all PASS on both sides**: (a) GET /api/profile/ 200; (b) POST /api/tags/ capture id → GET /api/tags/<id>/ 200; (c) db assertion — record detail: `db count: got 1 -> PASS` (the D225 snapshot-copy read; rehearsal #1 failed here with "attempt to write a readonly database").
- **Zero model requests:** model_request is the empty object; model_usage, model_finish_reason and model_attempts all absent/null — the mode never reads the API key.

## Redactions / secret scan

Nothing in this pack was redacted by hand; the log and record are as produced (the harness redacts registered secret values itself). The full job log and the record were scanned for the contract's 6 non-public VALUES (the 4 non-public env values + the contract's auth username and password values): **0 occurrences of every value in both files**.
