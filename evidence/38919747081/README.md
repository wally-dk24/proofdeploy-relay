# Evidence pack — readiness run 38919747081 (25-side re-run, D90; D61)

- **Run ID:** 38919747081 (GitHub Actions, repo `wally-dk24/proofdeploy`)
- **Workflow:** `.github/workflows/readiness.yml` (`repo=TryGhost/Ghost`, `attempt=2` — attempt 1 for every side plus in-run re-provision of non-READY sides only; typed not-supported sides are never retried)
- **Head SHA dispatched from:** `a21b2897e400879d1ed4f81f7e9847f39742dbcd` (main; post-merge CI on this SHA was green before dispatch)
- **Run conclusion:** success (job 2026-10-16T14:55:48Z → 2026-10-16T15:07:30Z)
- **Artifact name:** `readiness-TryGhost-Ghost-2` (artifact id 12054323595)
- **Artifact zip sha256 (as downloaded):** `aec9b63c2aa3ba242f63c727f6b372827c6f1a23248f9c51eb326d528e8d20e7`
- **Predictions:** committed before the first dispatch of this re-run — `evidence/rerun25-2026-10-16/predictions.md` (relay commit `c501a2da1a115f3bd9f989b36fa7966ae7fd0c71`, 2026-10-16T14:45:03Z; first dispatch 14:55:14Z).

## Contents

- `rows-attempt1.json`
- `rows-attempt2.json`
- `pd-logs/readiness-checkpoint-bug-ghost-1-fix-a1.log`

Every side was READY at attempt 1; `rows-attempt2.json` is present as downloaded and carries an empty rows list. One READY-side reference log is included per D61; the other two READY logs remain in the artifact named above.

## Outcome (final status per side, attempt 2 where it ran) — READY 3/3

- `C5` C: **READY**
- `checkpoint-bug-ghost-1` fix: **READY**
- `checkpoint-bug-ghost-1` fix^: **READY**

## Redaction statement (D61)

All included files were scanned before committing for credential material (password/token/api-key assignments, `Authorization`/`Bearer` values, credential-bearing URLs, key formats). **Zero redactions were required: no credential, token, password, API key, or per-run secret value appears in any included file.**

## Trimming

No trimming was needed: the largest included file is 23262 bytes (limit 1 MB per file). All included logs are complete and unmodified apart from the copy itself.
