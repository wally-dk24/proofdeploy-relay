# Evidence pack — readiness run 38894300422 (Ghost D71 contract rehearsal, attempt 2; plan 1.11, D71)

- **Run ID:** 38894300422 (GitHub Actions, repo `wally-dk24/proofdeploy`)
- **Workflow:** `.github/workflows/readiness.yml` (`repo=TryGhost/Ghost`, `attempt=2` — first pass plus re-provision of the non-READY sides, per the workflow's semantics)
- **Head SHA the run was dispatched on:** `faada3c` (branch `fix/ghost-contract-d71` — the D71 contract change on main `e6c7bbd`; see PR #119)
- **Run conclusion:** success (job 11:31:14Z–12:02:35Z, 2026-10-16)
- **Artifact name:** `readiness-TryGhost-Ghost-2`
- **Artifact zip sha256 (as downloaded):** `72afb52532bf4dcdf0664c9ebbfcf3cebd38c81d057f787e2826484364c7c9f1`
- **Companion run:** `38891555631` (the `attempt=1` rehearsal dispatch), evidence pack at `evidence/38891555631/`, which carries the full outcome analysis. This pack is its deterministic twin.

## Contents

- `rows-attempt1.json` and `rows-attempt2.json` — as downloaded from the artifact, unmodified.
- `pd-logs/` — all six logs (attempts 1 and 2 for each of the three sides). **Every side failed in both attempts**, so per D61 all failing-side logs are included; **no side reached READY, so no READY reference log exists**.

## Outcome (from the rows)

**READY 0/3 in both attempts, deterministically identical to the attempt-1 rehearsal dispatch.** All three registered Ghost sides are `START_FAILED` with the identical typed reason `readiness timeout` (`sandbox app not ready`) in attempt 1 and again in attempt 2:

| unit / side | registered SHA | attempt 1 | attempt 2 |
|---|---|---|---|
| C5 (control) | `5d482965b7f5ed05ffef956d0eaed8b8a51af0ef` | START_FAILED | START_FAILED |
| checkpoint-bug-ghost-1 fix^ | `93e0084723cdaf22d656bcd4811b87433e6b3875` | START_FAILED | START_FAILED |
| checkpoint-bug-ghost-1 fix | `f67640b2c18c76abd9cd3f6d19d6ae93327b42fa` | START_FAILED | START_FAILED |

Cause, identical in every log: the `migrate` blocker is gone and the database self-initializes on boot (`Database is in a ready state.`), then the app dies during boot on the missing card asset manifest (`InternalServerError: Could not use the card asset manifest at /checkout/ghost/core/core/frontend/public/cards.manifest.json`, ENOENT). No `Location` header appears in any log; **GET / was never answered on any side** (no readiness or authenticated-GET evidence exists). Full analysis, submodule records, and the verbatim error block are in the companion pack (`evidence/38891555631/README.md`); the submodule records in these rows are byte-identical to that pack's table.

## Redaction statement (D61)

All 8 artifact files (both rows JSONs and all six pd-logs) were scanned before committing for credential material (patterns: `ghp_`/`github_pat_` tokens, `Authorization`/`Bearer` headers, password/secret/api-key value assignments). **Zero redactions were required: no credential, token, password, API key, or per-run secret value appears in any file.**

## Trimming

No trimming was needed: the largest artifact file is 25,716 bytes (limit 1 MB per file). The included files are complete and unmodified apart from the copy itself.
