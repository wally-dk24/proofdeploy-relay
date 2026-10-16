# Evidence pack — readiness run 38919780168 (25-side re-run, D90; D61)

- **Run ID:** 38919780168 (GitHub Actions, repo `wally-dk24/proofdeploy`)
- **Workflow:** `.github/workflows/readiness.yml` (`repo=django/django`, `attempt=2` — attempt 1 for every side plus in-run re-provision of non-READY sides only; typed not-supported sides are never retried)
- **Head SHA dispatched from:** `a21b2897e400879d1ed4f81f7e9847f39742dbcd` (main; post-merge CI on this SHA was green before dispatch)
- **Run conclusion:** success (job 2026-10-16T14:56:00Z → 2026-10-16T15:01:22Z)
- **Artifact name:** `readiness-django-django-2` (artifact id 12053174818)
- **Artifact zip sha256 (as downloaded):** `53a3d86d79ced8d9c2a7bde09a0b9440c6fba963fb52d5408cb08e44990b4c3b`
- **Predictions:** committed before the first dispatch of this re-run — `evidence/rerun25-2026-10-16/predictions.md` (relay commit `c501a2da1a115f3bd9f989b36fa7966ae7fd0c71`, 2026-10-16T14:45:03Z; first dispatch 14:55:14Z).

## Contents

- `rows-attempt1.json`
- `rows-attempt2.json`
- `pd-logs/readiness-T4-fix-a1.log`
- `pd-logs/readiness-T4-fix-parent-a1.log`
- `pd-logs/readiness-train-C1-clean-a1.log`

All three sides were refused at plan time with a typed not-supported reason, so the harness did not re-provision them: `rows-attempt2.json` carries an empty rows list and there are no attempt-2 logs. No side reached READY, so there is no READY-side reference log.

## Outcome (final status per side, attempt 2 where it ran) — READY 0/3

- `T4` fix: **BUILD_FAILED** — typed_reason: `not supported: no frozen lockfile found in snapshot for runtime 'node'`
- `T4` fix^: **BUILD_FAILED** — typed_reason: `not supported: no frozen lockfile found in snapshot for runtime 'node'`
- `train-C1` C: **BUILD_FAILED** — typed_reason: `not supported: no frozen lockfile found in snapshot for runtime 'node'`

## Redaction statement (D61)

All included files were scanned before committing for credential material (password/token/api-key assignments, `Authorization`/`Bearer` values, credential-bearing URLs, key formats). **Zero redactions were required: no credential, token, password, API key, or per-run secret value appears in any included file.**

## Trimming

No trimming was needed: the largest included file is 3146 bytes (limit 1 MB per file). All included logs are complete and unmodified apart from the copy itself.
