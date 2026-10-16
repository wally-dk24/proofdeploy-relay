# Evidence pack — readiness run 38919692114 (25-side re-run, D90; D61)

- **Run ID:** 38919692114 (GitHub Actions, repo `wally-dk24/proofdeploy`)
- **Workflow:** `.github/workflows/readiness.yml` (`repo=vendure-ecommerce/vendure`, `attempt=2` — attempt 1 for every side plus in-run re-provision of non-READY sides only; typed not-supported sides are never retried)
- **Head SHA dispatched from:** `a21b2897e400879d1ed4f81f7e9847f39742dbcd` (main; post-merge CI on this SHA was green before dispatch)
- **Run conclusion:** success (job 2026-10-16T14:55:23Z → 2026-10-16T15:30:28Z)
- **Artifact name:** `readiness-vendure-ecommerce-vendure-2` (artifact id 12055653245)
- **Artifact zip sha256 (as downloaded):** `0d0a1c41a4772ceddab0f2d164a29c5f6a1f46bf74682b8a1d721c62a64e0fc5`
- **Predictions:** committed before the first dispatch of this re-run — `evidence/rerun25-2026-10-16/predictions.md` (relay commit `c501a2da1a115f3bd9f989b36fa7966ae7fd0c71`, 2026-10-16T14:45:03Z; first dispatch 14:55:14Z).

## Contents

- `rows-attempt1.json`
- `rows-attempt2.json`
- `pd-logs/readiness-checkpoint-bug-vendure-1-fix-a1.log`

Every side was READY at attempt 1; `rows-attempt2.json` is present as downloaded and carries an empty rows list. One READY-side reference log is included per D61; the other five READY logs remain in the artifact named above.

## Outcome (final status per side, attempt 2 where it ran) — READY 6/6

- `C1` C: **READY**
- `C2` C: **READY**
- `checkpoint-bug-vendure-1` fix: **READY**
- `checkpoint-bug-vendure-1` fix^: **READY**
- `checkpoint-bug-vendure-2` fix: **READY**
- `checkpoint-bug-vendure-2` fix^: **READY**

## Redaction statement (D61)

All included files were scanned before committing for credential material (password/token/api-key assignments, `Authorization`/`Bearer` values, credential-bearing URLs, key formats). **Zero redactions were required.** The one password-adjacent line is Vendure's own startup warning naming the `SUPERADMIN_USERNAME`/`SUPERADMIN_PASSWORD` environment variables; it prints no values and is retained verbatim.

## Trimming

No trimming was needed: the largest included file is 25584 bytes (limit 1 MB per file). All included logs are complete and unmodified apart from the copy itself.
