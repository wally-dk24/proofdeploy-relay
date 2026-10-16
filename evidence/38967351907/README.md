# D61 evidence pack — Directus auth proof (D113/D137, generic driver)

- **Run ID:** 38967351907 (GitHub Actions, repo `wally-dk24/proofdeploy`)
- **Workflow:** `.github/workflows/readiness.yml`, dispatched with `mode=auth-proof`, `repo=directus/directus`, `attempt=1`, `--ref feat/expires-unit-generic-proof`
- **Head SHA:** `d1bc3efa9c0e94cc43d83b6300916135b0c4552b` (PR #134 — D137: `expires_in_unit` + generic contract-driven auth-proof driver)
- **Run conclusion:** success
- **Artifact:** `readiness-directus-directus-1` (id 12075684699), zip sha256 as downloaded: `4c113f0e017cf16334b60fd01ae493491de67087e240584dfb0917317fad3c1c`
- **Contents:** `rows-authproof.json` (the run's rows, labelled `auth-proof` — evidence, never measured records) + `pd-logs/` for all four sides.
- **Predictions:** committed to this relay BEFORE the first dispatch — `evidence/d137-auth-proofs/predictions.md` (relay commit `6b92370`, 2026-10-16T21:09:06Z; this run was created 21:09:13Z).

## Result: PROVEN on all 4 sides — exactly as predicted

Endpoint per side: `GET /users/me` (the driver's registered Directus spec; expected unauthenticated status 401). Each side's fixture render, recomputed by the harness from the committed contract, is sha256 `dbbdcfd4b8287be91918d2c2aaade11a4c9c7b8496ac16f0f9409b1f1656b610` — the signed D119 render, unchanged by the D137 `expires_in_unit: milliseconds` auth-block addition.

| Side | Registered SHA | Side status | Req 1 (auth) | Req 2 (auth) | Req 3 (no Authorization) |
|---|---|---|---|---|---|
| `C3` C | `dc922b790a15949e8abfce9c1f4523d4e5f89492` | READY | 200, acquisitions `{"login": 1}` | 200, acquisitions `{"login": 1}` | 401 |
| `C4` C | `a5da59da94bb33c8d644df93a21403eca4c1a4e7` | READY | 200, acquisitions `{"login": 1}` | 200, acquisitions `{"login": 1}` | 401 |
| `checkpoint-bug-directus-1` fix^ | `7e9b03504c6094bac8dace713c11a6f909016057` | READY | 200, acquisitions `{"login": 1}` | 200, acquisitions `{"login": 1}` | 401 |
| `checkpoint-bug-directus-1` fix | `93a3f39342e171d3ae3017c2aa9a744b5e1c3f8a` | READY | 200, acquisitions `{"login": 1}` | 200, acquisitions `{"login": 1}` | 401 |

Per side: two authenticated requests, each with its OWN fresh acquisition through the provider's refresh-mode login (`POST /auth/login`), 2 logins and 0 refreshes in total — the REG-E item 4 shape after D137 withdrew D113's forced-refresh leg (the measured path builds a fresh context per probe). The unauthenticated request carries no Authorization header and is refused with 401.

## Redactions

The harness scrubs every emitted string BY VALUE with the side's secret set (per-side bootstrap values, every acquired token) plus a JWT-shape sweep, and records counts per category on each row. In this run every row's counts are **0 in all categories** (`key_file` 0, `bootstrap_value` 0, `acquired_token` 0, `jwt_shape` 0): no secret value appeared in any emitted string (Authorization headers are recorded by shape only, `Bearer <redacted:acquired_token>`). Independent check on this pack: a JWT-shape grep (`eyJ`) over the rows and all four logs returns 0 matches.

## Trimming

No trimming was needed: the largest file is 21,072 bytes (limit 1 MB per file). All files are complete and unmodified apart from the copy itself.
