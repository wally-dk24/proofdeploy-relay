# Evidence pack — readiness run 38919757723 (25-side re-run, D90; D61)

- **Run ID:** 38919757723 (GitHub Actions, repo `wally-dk24/proofdeploy`)
- **Workflow:** `.github/workflows/readiness.yml` (`repo=honojs/hono`, `attempt=2` — attempt 1 for every side plus in-run re-provision of non-READY sides only; typed not-supported sides are never retried)
- **Head SHA dispatched from:** `a21b2897e400879d1ed4f81f7e9847f39742dbcd` (main; post-merge CI on this SHA was green before dispatch)
- **Run conclusion:** success (job 2026-10-16T14:55:51Z → 2026-10-16T15:06:29Z)
- **Artifact name:** `readiness-honojs-hono-2` (artifact id 12054576565)
- **Artifact zip sha256 (as downloaded):** `23fcf4a7c7f4951f163c39e5c35d02d35620ab3969c4568e6abd83876b269ca5`
- **Predictions:** committed before the first dispatch of this re-run — `evidence/rerun25-2026-10-16/predictions.md` (relay commit `c501a2da1a115f3bd9f989b36fa7966ae7fd0c71`, 2026-10-16T14:45:03Z; first dispatch 14:55:14Z).

## Contents

- `rows-attempt1.json`
- `rows-attempt2.json`
- `pd-logs/readiness-T7-fix-a1.log`
- `pd-logs/readiness-T7-fix-a2.log`
- `pd-logs/readiness-T7-fix-parent-a1.log`
- `pd-logs/readiness-T7-fix-parent-a2.log`
- `pd-logs/readiness-train-C2-clean-a1.log`
- `pd-logs/readiness-train-C2-clean-a2.log`

No side reached READY, so there is no READY-side reference log.

## Outcome (final status per side, attempt 2 where it ran) — READY 0/3

- `T7` fix: **BUILD_FAILED** — typed_reason: `sandbox install failed: bash: line 1: pnpm: command not found`
- `T7` fix^: **BUILD_FAILED** — typed_reason: `sandbox install failed: bash: line 1: pnpm: command not found`
- `train-C2` C: **BUILD_FAILED** — typed_reason: `sandbox install failed: bash: line 1: pnpm: command not found`

## Redaction statement (D61)

All included files were scanned before committing for credential material (password/token/api-key assignments, `Authorization`/`Bearer` values, credential-bearing URLs, key formats). **Zero redactions were required: no credential, token, password, API key, or per-run secret value appears in any included file.**

## Trimming

No trimming was needed: the largest included file is 4415 bytes (limit 1 MB per file). All included logs are complete and unmodified apart from the copy itself.
