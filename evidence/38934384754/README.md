# D61 evidence pack — nest readiness re-run under plan 1.2(o) (app.port)

- **Run ID:** 38934384754
- **Workflow:** `readiness.yml` (workflow_dispatch, inputs: repo=nestjs/nest, attempt=2)
- **Dispatched from:** PR branch `feat/app-port-12o`, head SHA `e37daab5d3e2c9b19dfcd8459e0ec3e94c095fc0` (PR #123; base main `3eab21ac139307e37f5025eb7938743eb3b1fa02`)
- **Run window:** 2026-10-16T16:45:04Z → 17:06:07Z, conclusion **success**
- **Artifact:** `readiness-nestjs-nest-2`, id 12061172383, 101,898 bytes
- **Artifact zip sha256 (as downloaded):** `3679207487af30f603337fc0f245203b85110311e94a93610e7c68054c7008db`
- **Predictions (committed before dispatch, 16:44:57Z):** https://github.com/wally-dk24/proofdeploy-relay/blob/main/evidence/nest-port-12o/predictions.md (relay commit `ab0a7f71500b6a91b72cdd402c03f85a505c1cdc`)

## Contents

- `rows-attempt1.json`, `rows-attempt2.json` — as downloaded from the run artifact.
- `pd-logs/` — the log of **every non-READY side** (H1 fix + fix^, heldout-clean-1..4; attempt 1 only — typed sides are never retried, and there were no attempt-2 rows) plus **one READY reference**: `readiness-H2-fix-a1.log`.
- This README. (The artifact's `regd-tree-*.json` members are not part of D61's named contents; the full artifact above remains the primary record.)

## Redactions

**Zero redactions required.** Pattern scan over every included file (password / passwd / secret / api-key / Bearer / JWT shape `eyJ…` / token): 0 hits in every category. No trimming anywhere (largest file: rows-attempt1.json, 17,044 bytes).

## Results (rows-attempt1.json; attempt 2 wrote 0 rows — nothing retriable)

| unit | side | status | app_port |
|---|---|---|---|
| H2 | fix^ `a0b14b58` | **READY** | 3000 |
| H2 | fix `1d1dbf7b` | **READY** | 3000 |
| H3 | fix^ `40d07dc6` | **READY** | 3000 |
| H3 | fix `21648746` | **READY** | 3000 |
| H4 | fix^ `697ff89e` | **READY** | 3000 |
| H4 | fix `1685a435` | **READY** | 3000 |
| H1 | fix^ / fix | BUILD_FAILED, typed `not supported: lockfile out of sync with its manifest (npm)` (D33 drift — out of scope, unchanged) | 3000 |
| heldout-clean-1..4 | C | BUILD_FAILED, same typed drift refusal (unchanged) | 3000 |

**H2–H4: READY 6/6**, network mode `none+socket` on every row.

Mechanism, from the READY reference log (`pd-logs/readiness-H2-fix-a1.log`): `app.port declared (plan 1.2(o)): bridge targets 3000; PORT env stays 41361` — the container is started with `-e PORT=41361` while the bridge is invoked as `/pd-sock/app.sock 3000`; the app logs `Nest application successfully started` and `Application is running on: http://[::1]:3000`; the side ends `sandbox app READY`. Every H2–H4 side log carries the same `app.port declared` line exactly once; H1/clean logs carry none (those sides stop at install, before any start).

## Prediction scorecard

- H2/H3/H4 READY 6/6, `app_port: 3000` on every row — **exactly as predicted (6/6)**.
- H1 + the four cleans still type-refuse on drift with rows carrying `app_port: 3000` and no status change — **exactly as predicted** (out of scope).
