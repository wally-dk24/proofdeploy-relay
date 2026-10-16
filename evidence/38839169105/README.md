# Evidence pack — readiness run 38839169105 (D64 Vendure contract, PR #114)

- **Run ID:** 38839169105 (GitHub Actions, repo `wally-dk24/proofdeploy`)
- **Workflow:** `.github/workflows/readiness.yml` (`repo=vendure-ecommerce/vendure`, `attempt=1`)
- **Head SHA the run was dispatched on:** `1da31b93b725c2132a9e3f1267123542ebe3f408` (branch `feat/vendure-contract-d64`, PR #114 evidence head — the run record's own `head_sha` confirms this). Note: the branch was subsequently folded onto main `c630f298da9190c003df6b3faf5313703eef873a` (PR #109) → final head `e451b66ca2da14a14ea44e59e4c67433cb3ee0ff`; the contract change set is patch-id-identical on both bases (`2256708b60da647eb3e833d6b36605d4f3ccc6d6`; filtered diff sha256 `ba191bb3315dfe7ceec2542f15a92e11ff149f4067f4d5165fc39e756f2de293`), so the contract this run exercised is identical to the final head's.
- **Run conclusion:** success (job 02:11:52Z–02:45:54Z, 2026-10-16)
- **Artifact name:** `readiness-vendure-ecommerce-vendure-1` (artifact id 12021697705)
- **Artifact zip sha256 (as downloaded):** `e59a87e6caaa20fd933babc9b2e4bad495bfc99143e903cd4fcb1d57107041dc`

## Contents

- `rows-attempt1.json` — as downloaded from the artifact, unmodified. **No `rows-attempt2.json` exists:** the dispatch was `attempt=1` and every side was READY at attempt 1, so no re-provision was run.
- `pd-logs/readiness-checkpoint-bug-vendure-1-fix-a1.log` — the one READY-side reference log. **No side failed**, so per D61 there are no failing-side logs to include; the other five READY logs (`readiness-C1-clean-a1.log`, `readiness-C2-clean-a1.log`, `readiness-checkpoint-bug-vendure-1-fix-parent-a1.log`, `readiness-checkpoint-bug-vendure-2-fix-a1.log`, `readiness-checkpoint-bug-vendure-2-fix-parent-a1.log`) remain in the artifact named above.

## Outcome (from the rows)

All 6 units **READY** at attempt 1, no typed reason, no error: C1 (`4cbbc790`), C2 (`b19ab4dc`), checkpoint-bug-vendure-1 fix^ (`859619c4`) and fix (`4393199c`), checkpoint-bug-vendure-2 fix^ (`8fb22a8d`) and fix (`02c9d81e`). **READY 6/6** — against 0/6 under the pre-D64 start in run 38826754792.

Decisive evidence in the included log (vendure-1 fix): the executed start command (line 273) is the D64 string rendered through the 1.2(m) export-PATH wrapper — `export PATH=/checkout/.pd-tools/bin:/checkout/node_modules/.bin:$PATH; cd packages/dev-server && bun run dev:server --no-reload` — with env `DB=sqlite`, `RUN_JOB_QUEUE=1`, `HOME=/pd-home`, `PORT=37335` (the sandbox-assigned per-side port; nothing overrides it to 3000). `[JobQueue] Starting queue: apply-collection-filters` shows the in-process job queue running; the log ends at `sandbox app READY`. Across all six logs in the artifact: seed `[Populate] Done!` 6/6, every log carries its own `sandbox app READY` line, and there are zero `env: 'cd'` errors, zero `ENOENT`, zero `database is locked`, zero `WorkspaceValidityError`. Prediction scoring is posted on PR #114 (comment 6206899530): READY 6/6 correct; the 30–90 s time-to-health estimate is not separately scored because the artifact carries no finer per-side boot timing (READY itself certifies health inside the harness's 120 s window).

## Redaction statement (D61)

All 7 artifact files (the rows JSON and all six pd-logs, not only the one included here) were scanned before committing for credential material (patterns: password/passwd/secret/token/api-key values, `Authorization`/`Bearer` headers, credential-bearing proxy URLs, `password=<value>` assignments). **Zero redactions were required: no credential, token, password, API key, or per-run secret value appears in any file.** The only password-adjacent text is Vendure's own startup warning ("Default superadmin password is configured…"), which names environment variables (`SUPERADMIN_USERNAME`/`SUPERADMIN_PASSWORD`) but prints no values — 6 occurrences, one per log, retained verbatim. "Proxy" mentions are infrastructure descriptions (the executor's bridge proxy at an ephemeral `127.0.0.1` port inside the CI runner), with no credentials. Command lines show only non-secret env (`DB=sqlite`, `RUN_JOB_QUEUE=1`, `HOME=/pd-home`, `PORT=<ephemeral>`).

## Trimming

No trimming was needed: the largest artifact file is 25,688 bytes (limit 1 MB per file). The included files are complete and unmodified apart from the copy itself.
