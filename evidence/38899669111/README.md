# Evidence pack — readiness run 38899669111 (Ghost D71 for-the-record re-run, attempt 1; plan 1.11, D71; PR #119)

- **Run ID:** 38899669111 (GitHub Actions, repo `wally-dk24/proofdeploy`)
- **Workflow:** `.github/workflows/readiness.yml` (`repo=TryGhost/Ghost`, `attempt=1`)
- **Head SHA the run was dispatched on:** `223ea5795cc6f629eb5ba755f237b2ced799c897` (PR #119 head — the D71 contract change folded onto main `29045fb`; the contract file is byte-identical to the rehearsal head `faada3c`)
- **Run conclusion:** success (job 12:19:11Z–12:33:53Z, 2026-10-16)
- **Artifact name:** `readiness-TryGhost-Ghost-1`
- **Artifact zip sha256 (as downloaded):** `6c998ad5e71400ac2d441e71e7c2d7725de8df1fd11387bb97d02476987c1ac3`
- **Companion runs:** rehearsal `38891555631` + `38894300422` (packs at `evidence/38891555631/`, `evidence/38894300422/`); this re-run's `attempt=2` dispatch `38901492504` is packed at `evidence/38901492504/` and carries the prediction scoring.
- **Predictions:** posted before this dispatch as a comment on PR #119 (https://github.com/wally-dk24/proofdeploy/pull/119#issuecomment-6215346810) — READY + GET `/` → HTTP 200 on all three sides, with the contrary rehearsal evidence restated in the same comment.

## Contents

- `rows-attempt1.json` — as downloaded from the artifact, unmodified.
- `pd-logs/` — all three sides' logs. **Every side failed**, so per D61 all failing-side logs are included; **no side reached READY, so no READY reference log exists**.

## Outcome (from the rows)

**READY 0/3 — identical to the rehearsal.** All three registered Ghost sides are `START_FAILED` with the identical typed reason `readiness timeout` (`sandbox app not ready`):

| unit / side | registered SHA | attempt 1 |
|---|---|---|
| C5 (control) | `5d482965b7f5ed05ffef956d0eaed8b8a51af0ef` | START_FAILED |
| checkpoint-bug-ghost-1 fix^ | `93e0084723cdaf22d656bcd4811b87433e6b3875` | START_FAILED |
| checkpoint-bug-ghost-1 fix | `f67640b2c18c76abd9cd3f6d19d6ae93327b42fa` | START_FAILED |

Same signature as the rehearsal packs: the `migrate` blocker is gone, the database self-initializes on boot (`Database is in a ready state.`), then the app dies during boot on the missing card asset manifest (`InternalServerError: Could not use the card asset manifest at /checkout/ghost/core/core/frontend/public/cards.manifest.json`, ENOENT). No `Location` header appears in any log; **GET / was never answered on any side** (no readiness or authenticated-GET evidence exists). Submodule records in the rows are identical to the rehearsal packs' table (same recorded commits and materialized `tree_sha`s).

## Redaction statement (D61)

All 4 artifact files (the rows JSON and all three pd-logs) were scanned before committing for credential material (patterns: `ghp_`/`github_pat_` tokens, `Authorization`/`Bearer` headers, password/secret value assignments). **Zero redactions were required: no credential, token, password, API key, or per-run secret value appears in any file.**

## Trimming

No trimming was needed: all files are well under the 1 MB per-file limit. The included files are complete and unmodified apart from the copy itself.
