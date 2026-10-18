# Predictions — WO-6 provision rehearsal #2 (D226)

Committed BEFORE the rehearsal #2 dispatch.

- **Dispatch:** measure.yml from merged main **e8bf530bd2a3df06286d23f305966aa59a23a121** (PR #149 merged under D139 after reviewer acceptance D226; merge-base for this run = that main itself).
- **Mode:** dry_run = provision (bundle → leak gate → provision both sides through the measured code → seed → fixed rehearsal probe set through the real executor → record → stop; the API key is not read in this mode).
- **Inputs (D219, unchanged):** target_repo https://github.com/paperless-ngx/paperless-ngx, unit_repo empty (contract store), arm skill, bug_id wo6-paperless-371c4f57, base 3a34bdc7b078486bc85a97ce309a1c8180cdfc88, bug 7dca0bfa6af103b76185aada3ce736b0ac1abc01, fix 371c4f57d3ab208fc261e159fa86ca1dc80cb334, clean empty, public_env_keys "PAPERLESS_CHANNELS_BACKEND PAPERLESS_CACHE_BACKEND".

## Predictions

1. **Both sides READY** (fix^ and fix provision status ready) — as in rehearsal #1 (39160097729), where the D220 env fix already proved out.
2. **container_env_keys = all 6 contract keys:** PAPERLESS_SECRET_KEY, PAPERLESS_DBENGINE, PAPERLESS_DATA_DIR, PAPERLESS_TOKEN_THROTTLE_RATE, PAPERLESS_CHANNELS_BACKEND, PAPERLESS_CACHE_BACKEND.
3. **Rehearsal probe set — ALL PASS on BOTH sides:**
   - (a) authenticated GET /api/profile/ → status 200 (passed in rehearsal #1);
   - (b) setup POST /api/tags/ {"name": "pd-rehearsal"} capturing id → GET /api/tags/<id>/ → status 200 (passed in rehearsal #1);
   - (c) db assertion SELECT COUNT(*) FROM auth_user → count >= 1 — **predicted PASS this time**, via the D225 snapshot-copy read (PR #149): the executor copies the db file plus its -wal into a private temp dir and opens the copy with query_only=ON, instead of opening the live WAL file mode=ro (rehearsal #1's failure: "attempt to write a readonly database" on both sides).
4. **Zero model requests** — model_request empty, no model_usage, the mode never reads the API key. Record marks dry_run "provision", measured_result false, no score record.
5. **No secret value in the job log** — the full job log will be scanned for the contract's non-public values; predicted 0 occurrences of every value (redaction registration from PR #148).
