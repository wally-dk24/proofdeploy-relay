# Evidence pack — readiness run 38919702236 (25-side re-run, D90; D61)

- **Run ID:** 38919702236 (GitHub Actions, repo `wally-dk24/proofdeploy`)
- **Workflow:** `.github/workflows/readiness.yml` (`repo=directus/directus`, `attempt=2` — attempt 1 for every side plus in-run re-provision of non-READY sides only; typed not-supported sides are never retried)
- **Head SHA dispatched from:** `a21b2897e400879d1ed4f81f7e9847f39742dbcd` (main; post-merge CI on this SHA was green before dispatch)
- **Run conclusion:** success (job 2026-10-16T14:55:27Z → 2026-10-16T15:23:40Z)
- **Artifact name:** `readiness-directus-directus-2` (artifact id 12055543506)
- **Artifact zip sha256 (as downloaded):** `e46f7700e121bc1f87d0ba029c4572b849c391e1b8cfb131d4e7f003c6e1ffe5`
- **Predictions:** committed before the first dispatch of this re-run — `evidence/rerun25-2026-10-16/predictions.md` (relay commit `c501a2da1a115f3bd9f989b36fa7966ae7fd0c71`, 2026-10-16T14:45:03Z; first dispatch 14:55:14Z).

## Contents

- `rows-attempt1.json`
- `rows-attempt2.json`
- `pd-logs/readiness-C3-clean-a1.log`
- `pd-logs/readiness-C3-clean-a2.log`
- `pd-logs/readiness-C4-clean-a1.log`
- `pd-logs/readiness-C4-clean-a2.log`
- `pd-logs/readiness-checkpoint-bug-directus-1-fix-a1.log`
- `pd-logs/readiness-checkpoint-bug-directus-1-fix-a2.log`
- `pd-logs/readiness-checkpoint-bug-directus-1-fix-parent-a1.log`
- `pd-logs/readiness-checkpoint-bug-directus-1-fix-parent-a2.log`

The harness itself renders the contract's bootstrap env values redacted in the pd-logs (96 markers of the forms `***REDACTED***` / `<redacted>` across the 8 logs — ADMIN_EMAIL, ADMIN_PASSWORD, SECRET values never appear). No side reached READY, so there is no READY-side reference log.

## Outcome (final status per side, attempt 2 where it ran) — READY 0/4

- `C3` C: **BUILD_FAILED** — typed_reason: `sandbox install failed: bash: line 1: directus: command not found`
- `C4` C: **BUILD_FAILED** — typed_reason: `sandbox install failed: bash: line 1: directus: command not found`
- `checkpoint-bug-directus-1` fix: **BUILD_FAILED** — typed_reason: `sandbox install failed: bash: line 1: directus: command not found`
- `checkpoint-bug-directus-1` fix^: **BUILD_FAILED** — typed_reason: `sandbox install failed: bash: line 1: directus: command not found`

## Redaction statement (D61)

All included files were scanned before committing for credential material (password/token/api-key assignments, `Authorization`/`Bearer` values, credential-bearing URLs, key formats). **Zero redactions were required: no credential, token, password, API key, or per-run secret value appears in any included file.**

## Trimming

No trimming was needed: the largest included file is 10039 bytes (limit 1 MB per file). All included logs are complete and unmodified apart from the copy itself.
