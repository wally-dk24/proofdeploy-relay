# Evidence pack — readiness run 38891555631 (Ghost D71 contract rehearsal, attempt 1; plan 1.11, D71)

- **Run ID:** 38891555631 (GitHub Actions, repo `wally-dk24/proofdeploy`)
- **Workflow:** `.github/workflows/readiness.yml` (`repo=TryGhost/Ghost`, `attempt=1`)
- **Head SHA the run was dispatched on:** `faada3c` (branch `fix/ghost-contract-d71` — the D71 contract change on main `e6c7bbd`). The contract change only: `readiness/contracts/TryGhost__Ghost.yml` — `migrate` removed, `start` replaced with upstream's nodemon exec (see PR #119).
- **Run conclusion:** success (job 11:13:13Z–11:28:15Z, 2026-10-16)
- **Artifact name:** `readiness-TryGhost-Ghost-1`
- **Artifact zip sha256 (as downloaded):** `5583cdf3d4a517ef58655fbbf4f66ffb13d8ba033498904dc96f59ba968a7eec`
- **Companion run:** `38894300422` (the `attempt=2` rehearsal dispatch), evidence pack at `evidence/38894300422/`. The for-the-record re-run from PR #119's folded head `223ea57` is packed separately.

## Contents

- `rows-attempt1.json` — as downloaded from the artifact, unmodified.
- `pd-logs/` — all three sides' logs. **Every side failed**, so per D61 all failing-side logs are included; **no side reached READY, so no READY reference log exists**.

## Outcome (from the rows)

**READY 0/3.** All three registered Ghost sides are `START_FAILED` with the identical typed reason `readiness timeout` (`sandbox app not ready`):

| unit / side | registered SHA | attempt 1 |
|---|---|---|
| C5 (control) | `5d482965b7f5ed05ffef956d0eaed8b8a51af0ef` | START_FAILED |
| checkpoint-bug-ghost-1 fix^ | `93e0084723cdaf22d656bcd4811b87433e6b3875` | START_FAILED |
| checkpoint-bug-ghost-1 fix | `f67640b2c18c76abd9cd3f6d19d6ae93327b42fa` | START_FAILED |

**What changed vs the pre-D71 runs (`38850295028`/`38851152056`):** the `migrate` blocker is gone. Install and build complete, and the app boots far enough to initialize its own database — D71's premise verified in the logs (fix side: `Database is in a ready state.` and `Ghost database ready in 5.965s`). There is **no redirect**: no `Location` header appears anywhere in any side's log, and the readiness probe never received an HTTP response — **GET / was never answered on any side** (no readiness or authenticated-GET evidence exists).

**The new blocker (from each log's tail, all sides):** the app dies during boot with

```
InternalServerError: Could not use the card asset manifest at /checkout/ghost/core/core/frontend/public/cards.manifest.json
  context: "ENOENT: no such file or directory, open '/checkout/ghost/core/core/frontend/public/cards.manifest.json'",
  help: 'Card assets ship with Ghost — reinstall, or run `pnpm build:assets` when running from source'
```

The manifest is a build product the contract's current `build` step (`pnpm run build`) does not produce. No classification is asserted here; the row's typed reason is `readiness timeout`.

**Submodule records (the rows' `submodules` field)** — recorded commit → materialized `tree_sha`, identical to the values independently verified against upstream in the pre-D71 packs:

| side | submodule | recorded commit | materialized `tree_sha` |
|---|---|---|---|
| C5 | `ghost/core/content/themes/casper` | `7f4adb690c40850382fed4312d90e21ff0313fdc` | `d645bc4aec20b1ee739a6221583dcd15c2f8d4d8` |
| C5 | `ghost/core/content/themes/source` | `219dc7dbe62bb6dfa8db9fd651528571eae6cfd3` | `805457d7f7e89099691e37e9e2ac1a37c672bb48` |
| fix^ / fix | `ghost/core/content/themes/casper` | `a3f491463f08d3be5315c516097831b6e9a4724d` | `0f245ecc389bb3b07f9c461c0bed83645762166a` |
| fix^ / fix | `ghost/core/content/themes/source` | `9d3414ec8c2b7f35a8e2e4b5bd09014620f21333` | `d0c55d8b8e1f29b3dbbab83c90cfd281f3dec6ca` |

## Redaction statement (D61)

All 4 artifact files (the rows JSON and all three pd-logs) were scanned before committing for credential material (patterns: `ghp_`/`github_pat_` tokens, `Authorization`/`Bearer` headers, password/secret/api-key value assignments). **Zero redactions were required: no credential, token, password, API key, or per-run secret value appears in any file.** Command lines show only non-secret env (ephemeral sandbox ports, `/pd-home` paths).

## Trimming

No trimming was needed: the largest artifact file is 25,733 bytes (limit 1 MB per file). The included files are complete and unmodified apart from the copy itself.
