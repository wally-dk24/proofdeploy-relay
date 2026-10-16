# Evidence pack — readiness run 38826754792 (plan 1.2(m), PR #113)

- **Run ID:** 38826754792 (GitHub Actions, repo `wally-dk24/proofdeploy`)
- **Workflow:** `.github/workflows/readiness.yml` (`repo=vendure-ecommerce/vendure`, `attempt=2` — attempt 1 plus re-provision of non-READY sides in the same run)
- **Head SHA the run was dispatched on:** `8f2f8faf005882dd503a2f6d50e6dba0829902ee` (branch `feat/wrapper-path-export`, PR #113 evidence head)
- **Run conclusion:** success (job 00:01:53Z–~01:23Z, 2026-10-16)
- **Artifact name:** `readiness-vendure-ecommerce-vendure-2` (artifact id 12017834306)
- **Artifact zip sha256 (as downloaded):** `9ddc227fbbdb8c1808e46dd01c5a5b42dec1a095130bdd28481c2485f2416039`

## Contents

- `rows-attempt1.json`, `rows-attempt2.json` — as downloaded from the artifact, unmodified.
- `pd-logs/` — the provision log for **every** side (all 6 units × 2 attempts = 12 logs). **No side reached READY in this run**, so there is no READY-side reference log; every side progressed to the same point (install → build → seed → start), and `pd-logs/readiness-checkpoint-bug-vendure-1-fix-a1.log` is the representative furthest-progressed log.

## Outcome (from the rows)

All 6 units (C1, C2, checkpoint-bug-vendure-1 fix/fix^, checkpoint-bug-vendure-2 fix/fix^), both attempts: **START_FAILED**, typed_reason `readiness timeout`, last_error `sandbox app not ready`. **READY 0/6.**

Per the logs (12/12): frozen bun install passed; build passed (Lerna, 16 projects); the seed `cd packages/dev-server && bun run populate` executed end-to-end and exited 0 (`[Populate] Done!`) — the plan 1.2(m) wrapper fix working; zero `env: 'cd'` errors, zero exit-127 step failures, zero `WorkspaceValidityError`. At start the app booted (server + worker) and then exited: worker `ENOENT … scandir '/checkout/packages/dev-server/email-plugin/templates/partials'`; server `SqliteError: database is locked` during concurrent schema sync; `script "start" exited with code 1`. Prediction scoring is posted on PR #113 (comment 6205895635).

## Redaction statement (D61)

All 14 files were scanned before committing for credential material (patterns: password/passwd/secret/token/api-key values, `Authorization`/`Bearer` headers, proxy URLs, `password=`/`password: ` assignments). **Zero redactions were required: no credential, token, password, API key, or per-run secret value appears in any file.** The only password-adjacent text is Vendure's own startup warning ("Default superadmin password is configured…"), which names environment variables (`SUPERADMIN_USERNAME`/`SUPERADMIN_PASSWORD`) but prints no values; it is retained verbatim. Command lines in the logs show only non-secret env (`DB=sqlite`, `HOME=/pd-home`, `PORT=<ephemeral>`).

## Trimming

No trimming was needed: the largest file is 25,591 bytes (limit 1 MB per file). All logs are complete and unmodified apart from the copy itself.
