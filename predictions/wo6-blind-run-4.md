# WO-6 blind run — predictions, attempt on main e8bf530 (D228)

Date: 2026-10-18. Committed BEFORE dispatch, per the standing predictions rule.

## Setup

- Run: WO-6 blind run, plan 1.5(d) — a D67 pipeline proof. The verdict is published; a catch is NOT required for the run to pass. This is the re-run of the SAME selected bug (D67) after two harness fixes: the D220 container-env fix (PR #148) and the D225 executor db snapshot-copy read (PR #149), each proven end-to-end by a provision rehearsal on this unit (39160097729, then 39163353020 — the second passing every rehearsal item on both sides).
- Dispatch: `measure.yml` from main `e8bf530bd2a3df06286d23f305966aa59a23a121` (the dispatch ref contains current main; merge-base with main is itself), `dry_run=none`.
- Inputs (D219, unchanged): target `https://github.com/paperless-ngx/paperless-ngx`, no unit_repo (contract-store path), arm `skill`, bug_id `wo6-paperless-371c4f57`, base `3a34bdc7b078486bc85a97ce309a1c8180cdfc88`, bug `7dca0bfa6af103b76185aada3ce736b0ac1abc01`, fix `371c4f57d3ab208fc261e159fa86ca1dc80cb334`, `public_env_keys` = `PAPERLESS_CHANNELS_BACKEND PAPERLESS_CACHE_BACKEND`.
- Author: skill v4, skill-author sha256 `ff601eb15b09e6e0bf8f48b2037f0ab8b2a1b0c90ac022156f76d0169380b36a`, provider Cerebras, model `gpt-oss-120b`, client pin `a35238a5…`.
- Record schema 1.8.0 (carries `model_finish_reason` and `model_usage`); the record is expected to carry `container_env_keys` = the 6 paperless contract keys (PAPERLESS_SECRET_KEY, PAPERLESS_DBENGINE, PAPERLESS_DATA_DIR, PAPERLESS_TOKEN_THROTTLE_RATE, PAPERLESS_CHANNELS_BACKEND, PAPERLESS_CACHE_BACKEND) and `measured_result` true, `dry_run` null.

## Prediction

The D67 pass conditions are predicted to HOLD:

1. Both sides (fix^ and fix) READY through the real sandboxed runner.
2. The blind author produces ≥1 valid probe from the diff and snapshot alone.
3. The probes execute on both sides.
4. The verdict is computed mechanically by the registered scorer.
5. The full record is committed and replay reproduces the verdict.

The VERDICT itself is predicted **UNKNOWN**: `candidate_catch` and `no_catch` are both live outcomes; no lean is stated. The earlier attempt on this bug (39149875917) reached the author call (5 valid probes, finish_reason `stop`) but never provisioned; this attempt is the first expected to execute probes against both sides.

Expected prompt size ~9K tokens on Cerebras — under this account's limits.

## Relation to earlier predictions

The Groq-era predictions (relay `b5f78ef`) and the skill v4 / Cerebras predictions for the d886215 attempt (`predictions/wo6-blind-run-v4-cerebras.md`) stand as written for their attempts. This file governs the e8bf530 attempt.
