# Evidence pack — readiness run 38901492504 (Ghost D71 for-the-record re-run, attempt 2; plan 1.11, D71; PR #119)

- **Run ID:** 38901492504 (GitHub Actions, repo `wally-dk24/proofdeploy`)
- **Workflow:** `.github/workflows/readiness.yml` (`repo=TryGhost/Ghost`, `attempt=2` — first pass plus re-provision of the non-READY sides, per the workflow's semantics)
- **Head SHA the run was dispatched on:** `223ea5795cc6f629eb5ba755f237b2ced799c897` (PR #119 head — the D71 contract change folded onto main `29045fb`; the contract file is byte-identical to the rehearsal head `faada3c`)
- **Run conclusion:** success (job 12:34:45Z–13:05:42Z, 2026-10-16)
- **Artifact name:** `readiness-TryGhost-Ghost-2`
- **Artifact zip sha256 (as downloaded):** `9d7bc45cb82f2495b4cdcd3380061c3414e2779b56650a0f28eb95b68eaf16cd`
- **Companion runs:** this re-run's `attempt=1` dispatch `38899669111` (pack at `evidence/38899669111/`); rehearsal `38891555631` + `38894300422` (packs at `evidence/38891555631/`, `evidence/38894300422/`).

## Contents

- `rows-attempt1.json` and `rows-attempt2.json` — as downloaded from the artifact, unmodified.
- `pd-logs/` — all six logs (attempts 1 and 2 for each of the three sides). **Every side failed in both attempts**, so per D61 all failing-side logs are included; **no side reached READY, so no READY reference log exists**.

## Outcome (from the rows)

**READY 0/3 in both attempts, deterministically identical to the rehearsal and to this re-run's attempt 1.** All three registered Ghost sides are `START_FAILED` with the identical typed reason `readiness timeout` (`sandbox app not ready`) in attempt 1 and again in attempt 2:

| unit / side | registered SHA | attempt 1 | attempt 2 |
|---|---|---|---|
| C5 (control) | `5d482965b7f5ed05ffef956d0eaed8b8a51af0ef` | START_FAILED | START_FAILED |
| checkpoint-bug-ghost-1 fix^ | `93e0084723cdaf22d656bcd4811b87433e6b3875` | START_FAILED | START_FAILED |
| checkpoint-bug-ghost-1 fix | `f67640b2c18c76abd9cd3f6d19d6ae93327b42fa` | START_FAILED | START_FAILED |

Same signature throughout: the `migrate` blocker is gone, the database self-initializes on boot (`Database is in a ready state.`), then the app dies during boot on the missing card asset manifest (`InternalServerError: Could not use the card asset manifest at /checkout/ghost/core/core/frontend/public/cards.manifest.json`, ENOENT). No `Location` header appears in any log; **GET / was never answered on any side** (no readiness or authenticated-GET evidence exists). Submodule records in the rows are identical across all four D71 dispatches (same recorded commits and materialized `tree_sha`s as the rehearsal packs' table).

## Prediction scoring

Predictions (PR #119 comment https://github.com/wally-dk24/proofdeploy/pull/119#issuecomment-6215346810, posted before the re-run dispatches): READY + GET `/` → HTTP 200 on all three sides, with the contrary rehearsal evidence restated in the same comment.

- **READY / GET / 200: WRONG on all 3 sides** — no side reached READY in either attempt of the re-run; GET / was never answered. The predictions' first premise (boot self-migration under D71) held; the run is blocked one step later, at the card-asset manifest the current `build` step does not produce.
- **Submodule records:** the rows carry exactly the registered theme pins on every side — consistent with the prior independent verification of the materialized `tree_sha`s against upstream.

## Redaction statement (D61)

All 8 artifact files (both rows JSONs and all six pd-logs) were scanned before committing for credential material (patterns: `ghp_`/`github_pat_` tokens, `Authorization`/`Bearer` headers, password/secret value assignments). **Zero redactions were required: no credential, token, password, API key, or per-run secret value appears in any file.**

## Trimming

No trimming was needed: all files are well under the 1 MB per-file limit. The included files are complete and unmodified apart from the copy itself.
