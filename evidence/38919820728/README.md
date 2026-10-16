# Evidence pack — readiness run 38919820728 (25-side re-run, D90; D61)

- **Run ID:** 38919820728 (GitHub Actions, repo `wally-dk24/proofdeploy`)
- **Workflow:** `.github/workflows/readiness.yml` (`repo=LMCache/LMCache`, `attempt=2` — attempt 1 for every side plus in-run re-provision of non-READY sides only; typed not-supported sides are never retried)
- **Head SHA dispatched from:** `a21b2897e400879d1ed4f81f7e9847f39742dbcd` (main; post-merge CI on this SHA was green before dispatch)
- **Run conclusion:** success (job 2026-10-16T14:56:20Z → 2026-10-16T15:06:14Z)
- **Artifact name:** `readiness-LMCache-LMCache-2` (artifact id 12053789572)
- **Artifact zip sha256 (as downloaded):** `457a6fd4851e9a0a7921616e18d25f2429e2bd49a0b646802732815fc6d204a7`
- **Predictions:** committed before the first dispatch of this re-run — `evidence/rerun25-2026-10-16/predictions.md` (relay commit `c501a2da1a115f3bd9f989b36fa7966ae7fd0c71`, 2026-10-16T14:45:03Z; first dispatch 14:55:14Z).

## Contents

- `rows-attempt1.json`
- `rows-attempt2.json`
- `pd-logs/readiness-T5-fix-a1.log`
- `pd-logs/readiness-T5-fix-a2.log`
- `pd-logs/readiness-T5-fix-parent-a1.log`
- `pd-logs/readiness-T5-fix-parent-a2.log`

No side reached READY, so there is no READY-side reference log.

## Outcome (final status per side, attempt 2 where it ran) — READY 0/2

- `T5` fix: **BUILD_FAILED** — typed_reason: `sandbox install failed: error: The build backend returned an error`
- `T5` fix^: **BUILD_FAILED** — typed_reason: `sandbox install failed: error: The build backend returned an error`

## Redaction statement (D61)

All included files were scanned before committing for credential material (password/token/api-key assignments, `Authorization`/`Bearer` values, credential-bearing URLs, key formats). **Zero redactions were required: no credential, token, password, API key, or per-run secret value appears in any included file.**

## Trimming

No trimming was needed: the largest included file is 10692 bytes (limit 1 MB per file). All included logs are complete and unmodified apart from the copy itself.
