# WO-6 blind run — predictions (skill v4 on Cerebras, D219/O20)

Date: 2026-10-18. Committed BEFORE dispatch, per the standing predictions rule.

## Setup

- Run: WO-6 blind run, plan 1.5(d) — a D67 pipeline proof. The verdict is published; a catch is NOT required for the run to pass.
- Author: skill v4, skill-author sha256 `ff601eb15b09e6e0bf8f48b2037f0ab8b2a1b0c90ac022156f76d0169380b36a`, provider Cerebras, model `gpt-oss-120b`, client pin `a35238a5…`.
- Dispatch: `measure.yml` from main `d886215cf3bd3266509adaa40ea7c01b32da6b25` (the dispatch ref contains current main; merge-base with main is itself).
- Target: paperless-ngx (real, non-golden target), bug_id `wo6-paperless-371c4f57`, arm `skill`, contract from the contract store (`readiness/contracts/paperless-ngx__paperless-ngx.yml`), `public_env_keys` = `PAPERLESS_CHANNELS_BACKEND PAPERLESS_CACHE_BACKEND`.
- Record schema 1.7.0 (carries `model_finish_reason` and `model_usage`).

## Prediction

The D67 pass conditions are predicted to HOLD:

1. Both sides (fix^ and fix) READY through the real sandboxed runner.
2. The blind author produces ≥1 valid probe from the diff and snapshot alone.
3. The probes execute on both sides.
4. The verdict is computed mechanically by the registered scorer.
5. The full record is committed and replays identically.

The VERDICT itself is predicted **UNKNOWN**: `candidate_catch` and `no_catch` are both live outcomes. Under skill v4 the golden catch rate was 7/10 across the two bug units (node 2/5, python 5/5), and real bugs are harder than golden ones, so no directional call is made.

Expected prompt size ~9K tokens — under Cerebras's limits for this account (the earlier Groq-era failure was a provider-plan ceiling, not a request-size problem; Cerebras's ceiling is far above this request).

## Relation to earlier predictions

The earlier predictions at relay `b5f78ef` were written for the Groq-era attempt and stand as written for that attempt. This file governs the skill v4 / Cerebras run.
