# Evidence pack — readiness run 38919681428 (25-side re-run, D90; D61)

- **Run ID:** 38919681428 (GitHub Actions, repo `wally-dk24/proofdeploy`)
- **Workflow:** `.github/workflows/readiness.yml` (`repo=nestjs/nest`, `attempt=2` — attempt 1 for every side plus in-run re-provision of non-READY sides only; typed not-supported sides are never retried)
- **Head SHA dispatched from:** `a21b2897e400879d1ed4f81f7e9847f39742dbcd` (main; post-merge CI on this SHA was green before dispatch)
- **Run conclusion:** success (job 2026-10-16T14:55:18Z → 2026-10-16T15:50:21Z)
- **Artifact name:** `readiness-nestjs-nest-2` (artifact id 12057461537)
- **Artifact zip sha256 (as downloaded):** `6025a937e75c44a4c1f4dbed4ea5ee9575737d959c6caa045866c61461f00624`
- **Predictions:** committed before the first dispatch of this re-run — `evidence/rerun25-2026-10-16/predictions.md` (relay commit `c501a2da1a115f3bd9f989b36fa7966ae7fd0c71`, 2026-10-16T14:45:03Z; first dispatch 14:55:14Z).

## Contents

- `rows-attempt1.json`
- `rows-attempt2.json`
- `pd-logs/readiness-H1-fix-a1.log`
- `pd-logs/readiness-H1-fix-parent-a1.log`
- `pd-logs/readiness-H2-fix-a1.log`
- `pd-logs/readiness-H2-fix-a2.log`
- `pd-logs/readiness-H2-fix-parent-a1.log`
- `pd-logs/readiness-H2-fix-parent-a2.log`
- `pd-logs/readiness-H3-fix-a1.log`
- `pd-logs/readiness-H3-fix-a2.log`
- `pd-logs/readiness-H3-fix-parent-a1.log`
- `pd-logs/readiness-H3-fix-parent-a2.log`
- `pd-logs/readiness-H4-fix-a1.log`
- `pd-logs/readiness-H4-fix-a2.log`
- `pd-logs/readiness-H4-fix-parent-a1.log`
- `pd-logs/readiness-H4-fix-parent-a2.log`
- `pd-logs/readiness-heldout-clean-1-clean-a1.log`
- `pd-logs/readiness-heldout-clean-2-clean-a1.log`
- `pd-logs/readiness-heldout-clean-3-clean-a1.log`
- `pd-logs/readiness-heldout-clean-4-clean-a1.log`

Attempt 2 re-ran only the six START_FAILED sides (H2–H4); the six typed not-supported sides (H1 both sides + the four held-out cleans) were not retried by the harness, per its semantics. No side reached READY, so there is no READY-side reference log. The nest artifact also carries `regd-tree-*.json` files (the REG-D resolution trees); per D61 this pack carries the rows and the pd-logs.

## Outcome (final status per side, attempt 2 where it ran) — READY 0/12

- `H1` fix: **BUILD_FAILED** — typed_reason: `not supported: lockfile out of sync with its manifest (npm): npm error `npm ci` can only install packages when your package.json and package-lock.json or npm-shrinkwrap.json are in sync. Please update your lock file with `npm install` before continuing.`
- `H1` fix^: **BUILD_FAILED** — typed_reason: `not supported: lockfile out of sync with its manifest (npm): npm error `npm ci` can only install packages when your package.json and package-lock.json or npm-shrinkwrap.json are in sync. Please update your lock file with `npm install` before continuing.`
- `H2` fix: **START_FAILED** — typed_reason: `readiness timeout`
- `H2` fix^: **START_FAILED** — typed_reason: `readiness timeout`
- `H3` fix: **START_FAILED** — typed_reason: `readiness timeout`
- `H3` fix^: **START_FAILED** — typed_reason: `readiness timeout`
- `H4` fix: **START_FAILED** — typed_reason: `readiness timeout`
- `H4` fix^: **START_FAILED** — typed_reason: `readiness timeout`
- `heldout-clean-1` C: **BUILD_FAILED** — typed_reason: `not supported: lockfile out of sync with its manifest (npm): npm error `npm ci` can only install packages when your package.json and package-lock.json or npm-shrinkwrap.json are in sync. Please update your lock file with `npm install` before continuing.`
- `heldout-clean-2` C: **BUILD_FAILED** — typed_reason: `not supported: lockfile out of sync with its manifest (npm): npm error `npm ci` can only install packages when your package.json and package-lock.json or npm-shrinkwrap.json are in sync. Please update your lock file with `npm install` before continuing.`
- `heldout-clean-3` C: **BUILD_FAILED** — typed_reason: `not supported: lockfile out of sync with its manifest (npm): npm error `npm ci` can only install packages when your package.json and package-lock.json or npm-shrinkwrap.json are in sync. Please update your lock file with `npm install` before continuing.`
- `heldout-clean-4` C: **BUILD_FAILED** — typed_reason: `not supported: lockfile out of sync with its manifest (npm): npm error `npm ci` can only install packages when your package.json and package-lock.json or npm-shrinkwrap.json are in sync. Please update your lock file with `npm install` before continuing.`

## Redaction statement (D61)

All included files were scanned before committing for credential material (password/token/api-key assignments, `Authorization`/`Bearer` values, credential-bearing URLs, key formats). **Zero redactions were required: no credential, token, password, API key, or per-run secret value appears in any included file.**

## Trimming

No trimming was needed: the largest included file is 17038 bytes (limit 1 MB per file). All included logs are complete and unmodified apart from the copy itself.
