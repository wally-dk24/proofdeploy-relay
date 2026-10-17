# WO-6 Phase C — paperless-ngx REG-E auth proof (D158/D160)

- **Run ID:** 39038612957 — workflow `readiness.yml`, `mode=auth-proof`, `repo=paperless-ngx/paperless-ngx`, `attempt=1`
- **Run URL:** https://github.com/wally-dk24/proofdeploy/actions/runs/39038612957
- **Dispatched from:** branch `feat/d158-paperless-rege` (PR #138), head `a894d0887b43fa53c06f50d0d26fa5e2980ab893`
- **Dispatch ref's merge-base with main:** `6dc1a4df3230578c88549a05f93070d1ab487f60` (= main's tip; main is fully contained in the dispatch ref — D155 statement)
- **Prediction:** relay `evidence/wo6-phase-c/predictions.md`, commit `198e43d64671347a43ebe631add711a4b31e8c27` pushed 2026-10-17T13:56:04Z, before dispatch (run created 2026-10-17T13:56:16Z)
- **Artifact:** `readiness-paperless-ngx-paperless-ngx-1` (id 12102819781), zip sha256 as downloaded: `a0534e587a166361dd12547b4cdbec48c9ec37f35f8f714e7a03401562207634`
- **Contract:** `readiness/contracts/paperless-ngx__paperless-ngx.yml` sha256 `fa3e12e1656873d99d7661f63be73cb9461c09d27a4550fe275f290e43500843` (D160 head; fixture `auth_mechanism` in the fixed presentation form, the other four fixture values byte-identical to D158). `presentation_scheme()` on this contract returns `Token` (foreground check before dispatch).
- **Fixture render:** sha256 `4731c82a8ec34bb84eef78ac8f1e0b884c3a35d7c4fa88f650981497cb43c281` (relay `evidence/wo6-phase-c/paperless-ngx-fixture-render.md`, relay commit `9f85a73`); the run row's `fixture_render_sha256` matches it exactly and `fixture_render_error` is null.

## Verdict: PROVEN (matches the prediction on every leg)

Side WO6-C1:C (registered SHA `0cb2cdbfdd91a2078236f8851cd310261284591c`) provisioned READY (Python 3.14.8, uv, network `none+socket`); the harness log shows tools under `/pd-home/.pd-tools` (6 mentions) and the venv at `/pd-home/.venv` (9 mentions), with `/checkout/.pd-tools` 0 and `/checkout/.venv` 0. Auth block (`rows-authproof.json`, unmodified):

| leg | request | Authorization | HTTP | acquisitions by kind | verdict |
|---|---|---|---|---|---|
| 1 (authenticated) | GET /api/profile/ | `Token <redacted:acquired_token>` | 200 | `{"login": 1}` | pass |
| 2 (authenticated) | GET /api/profile/ | `Token <redacted:acquired_token>` | 200 | `{"login": 1}` | pass |
| 3 (bare) | GET /api/profile/ | none sent (`authorization: null`) | 401 | `{}` (0) | pass |

Each authenticated leg acquired its own fresh login at `POST /api/token/` (provider detail lines in the row) and the response body is the seeded superuser's profile (`email: root@localhost` — the app's default, `PAPERLESS_ADMIN_MAIL` not declared). The bare leg's body is `{"detail": "Authentication credentials were not provided."}`. Totals: `acquisitions_after_two_authenticated: 2`, `acquisition_kinds: {"login": 2}`, `auth.status: PROVEN`, `passed: true`.

## Files

- `rows-authproof.json` — the run's rows file, byte-identical to the artifact's.
- `pd-logs/authproof-WO6-C1-clean-a1.log` — the side's full pd-log (23,267 bytes, no trimming; there were no failing sides, so this is the READY/reference side).

## Redactions

- By the harness (already in the artifact): the acquired token appears as `<redacted:acquired_token>` (row counter `acquired_token: 2`); the per-run generated bootstrap values are masked in the pd-log (`PAPERLESS_ADMIN_USER=***REDACTED***`, `PAPERLESS_ADMIN_PASSWORD=<redacted>`).
- For this pack: the contract's throwaway `PAPERLESS_SECRET_KEY` value appeared 11 times in sandbox command lines in the pd-log; each was replaced with `<redacted:PAPERLESS_SECRET_KEY>` (11 replacements; 0 occurrences of the value remain). JWT-shape grep over both pack files: 0 matches.
