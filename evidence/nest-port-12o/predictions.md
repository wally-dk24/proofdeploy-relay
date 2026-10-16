# Predictions — nest readiness re-run under plan 1.2(o) (app.port)

Posted BEFORE the dispatch, per the standing predictions-first rule (D83/D90).

- **Run**: `readiness.yml`, repo `nestjs/nest`, attempt=2, dispatched from PR branch `feat/app-port-12o` (the PR that adds `app.port` + the nest contract's `port: 3000` declaration).
- **Change under test**: the nest contract now declares `app: {path: ".", port: 3000}` (plan 1.2(o), D98). `sample/01-cats-app/src/main.ts` (blob `261ee54a`, identical at all 6 H2–H4 SHAs) calls `app.listen(3000)` and never reads `$PORT`. Before this change, install+build succeeded and the app booted on 3000 while the bridge probed the sandbox-assigned port — all 6 sides START_FAILED `readiness timeout` on both attempts (re-run evidence: relay `53ec85f`, run 38919681428).

## Predictions

| unit | side | registered SHA | prediction |
|---|---|---|---|
| H2 | fix^ | a0b14b58 | **READY** |
| H2 | fix | 1d1dbf7b | **READY** |
| H3 | fix^ | 40d07dc6 | **READY** |
| H3 | fix | 21648746 | **READY** |
| H4 | fix^ | 697ff89e | **READY** |
| H4 | fix | 1685a435 | **READY** |

**Expected: READY 6/6 on H2–H4.** Mechanism: the bridge now targets the declared port 3000 — the port the sample actually binds — instead of the assigned `$PORT` (still exported, still ignored by the app). Every row for these sides should carry `app_port: 3000`.

Out of scope, unchanged: H1 (both sides) and heldout-clean-1..4 will still type-refuse `not supported: lockfile out of sync with its manifest (npm)` (D33 drift class) and are not retried by the harness; their rows should also carry `app_port: 3000` (the contract declares it for the whole repo) without any status change.
