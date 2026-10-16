# Evidence pack — readiness run 38919799050 (25-side re-run, D90; D61)

- **Run ID:** 38919799050 (GitHub Actions, repo `wally-dk24/proofdeploy`)
- **Workflow:** `.github/workflows/readiness.yml` (`repo=fastify/fastify`, `attempt=2` — attempt 1 for every side plus in-run re-provision of non-READY sides only; typed not-supported sides are never retried)
- **Head SHA dispatched from:** `a21b2897e400879d1ed4f81f7e9847f39742dbcd` (main; post-merge CI on this SHA was green before dispatch)
- **Run conclusion:** success (job 2026-10-16T14:56:09Z → 2026-10-16T15:02:55Z)
- **Artifact name:** `readiness-fastify-fastify-2` (artifact id 12054940106)
- **Artifact zip sha256 (as downloaded):** `d18289ea66bf7c1244e9bf98c684cb3a0c41d0223c5104c35738f678bf71cf01`
- **Predictions:** committed before the first dispatch of this re-run — `evidence/rerun25-2026-10-16/predictions.md` (relay commit `c501a2da1a115f3bd9f989b36fa7966ae7fd0c71`, 2026-10-16T14:45:03Z; first dispatch 14:55:14Z).

## Contents

- `rows-attempt1.json`
- `rows-attempt2.json`
- `pd-logs/readiness-T2-fix-a1.log`
- `pd-logs/readiness-T2-fix-parent-a1.log`
- `pd-logs/readiness-T3-fix-a1.log`
- `pd-logs/readiness-T3-fix-parent-a1.log`
- `pd-logs/readiness-train-C3-clean-a1.log`

All five sides were refused at plan time with a typed not-supported reason, so the harness did not re-provision them: `rows-attempt2.json` carries an empty rows list and there are no attempt-2 logs. No side reached READY, so there is no READY-side reference log.

## Outcome (final status per side, attempt 2 where it ran) — READY 0/5

- `T2` fix: **BUILD_FAILED** — typed_reason: `not supported: no frozen lockfile found in snapshot for runtime 'node'`
- `T2` fix^: **BUILD_FAILED** — typed_reason: `not supported: no frozen lockfile found in snapshot for runtime 'node'`
- `T3` fix: **BUILD_FAILED** — typed_reason: `not supported: no frozen lockfile found in snapshot for runtime 'node'`
- `T3` fix^: **BUILD_FAILED** — typed_reason: `not supported: no frozen lockfile found in snapshot for runtime 'node'`
- `train-C3` C: **BUILD_FAILED** — typed_reason: `not supported: no frozen lockfile found in snapshot for runtime 'node'`

## Redaction statement (D61)

All included files were scanned before committing for credential material (password/token/api-key assignments, `Authorization`/`Bearer` values, credential-bearing URLs, key formats). **Zero redactions were required: no credential, token, password, API key, or per-run secret value appears in any included file.**

## Trimming

No trimming was needed: the largest included file is 5163 bytes (limit 1 MB per file). All included logs are complete and unmodified apart from the copy itself.
