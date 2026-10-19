# Second WO-6 blind run (plan 1.5(e)) — predictions, committed BEFORE dispatch

Date: 2026-10-19. Standing predictions rule; written by the orchestrator (Opus, D237) without any knowledge of the bug's content (blind: IDs, dates and check letters only).

## Selection (verified by the orchestrator by ID only)

- Walk: fresh WO-C walk under R-SEL from main `8be4796c488eb9ef17994d804b17786aef559d33` (PR #155 merged, D249), script sha256 `0007be5d…df92b`; record branch `records/wo6-bug-selection-2026-10-19` @ `63b5d1682fe0782be0a7f3be1e2e4265d083c3bc` (record sha256 `bc8ea796…`, walk.jsonl `23a6adba…`).
- Walked 11: rejected a 7, b 3 (commit 9 `371c4f57` at (b) "upgrade-path only", R-SEL working), selected commit 11.
- Fix `b992f9fc05bedd2f26f7291028b0b38471e697db` (committer date 2026-07-24T13:39:11Z), fix^ `6d249b49327fdab58ff915d3f5435e49de742464` (its only parent).
- B `aed9abe48c02b1b7df07e429db3f2ed8c3a58ce8` (2026-04-02T19:38:22Z), B^ `e01a762e81dd65dff927988aa51594906d8a417f`; B is contained in tag `v3.0.0-beta.rc1` (creatordate 2026-05-05T16:06:00Z, before the fix).
- Check (d): readiness run `39326973603` from main `8be4796c`: fix^ and fix READY, `userns-remap`, `none+socket`, contract `eb60559b…`, render `17edd561…`.
- No provision rehearsal: already-proven app, unchanged contract/seed/auth/executor path (D248).

## Dispatch

`measure.yml` from main `8be4796c` (contains current main), `dry_run=none`: target `https://github.com/paperless-ngx/paperless-ngx`, unit_repo empty (contract-store path, D202), arm `skill`, bug_id `wo6-paperless-b992f9fc`, base `e01a762e…`, bug `aed9abe4…`, fix `b992f9fc…`, `public_env_keys` = `PAPERLESS_CHANNELS_BACKEND PAPERLESS_CACHE_BACKEND`. Master approves in the `measurement` environment.

## Required on the record (anything else voids the run)

Schema 1.9.0; `measured_result` true; `dry_run` null; contract_source `readiness/contracts/paperless-ngx__paperless-ngx.yml` sha `eb60559b…`; fixture render `17edd561…`; skill-author `75d52a77…`; prompt template v8 `09c55dca…`; model_client `1e063204…`; provider cerebras, model `gpt-oss-120b`; public keys exactly the 2; `container_env_keys` the 6 contract keys.

## Prediction

1. D67 pipeline conditions HOLD: both sides READY; ≥1 valid probe; probes executed on both sides; mechanical verdict; record committed; replay reproduces.
2. R-BUNDLE: the author's bundle includes `src/paperless/urls.py` (route declarations), so probes target routes that exist (no invented-route 302/403 pattern as in D229).
3. R-METHOD: every surviving assertion carries verbatim evidence; at most one correction round; `finish_reason` stop.
4. Verdict: **UNKNOWN, no lean.** `candidate_catch` and `no_catch` are both live. This is the first real-app bug under R-SEL; whether its effect is observable through the HTTP probe plane on a fresh instance is not known to anyone blind to it. An INCONCLUSIVE of class environment is fixed and the same bug re-run (D67).
