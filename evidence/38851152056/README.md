# Evidence pack — readiness run 38851152056 (Ghost re-run attempt 2, plan 1.11; D46 Option A live after PR #109)

- **Run ID:** 38851152056 (GitHub Actions, repo `wally-dk24/proofdeploy`)
- **Workflow:** `.github/workflows/readiness.yml` (`repo=TryGhost/Ghost`, `attempt=2` — first pass plus re-provision of the non-READY sides, per the workflow's semantics)
- **Head SHA the run was dispatched on:** `9f7f3ad950bbb9a0f506a0225b7aa196a004094f` (main, PR #115 merge; contains PR #109, plan 1.2(l) submodules at pinned commits). The run record's own `head_sha` confirms this.
- **Run conclusion:** success (job 04:35:15Z–04:51:16Z, 2026-10-16; the run queued ~3 min behind other work before starting)
- **Artifact name:** `readiness-TryGhost-Ghost-2` (artifact id 12025632652)
- **Artifact zip sha256 (as downloaded):** `13aa08afac0a36a2e0ea6abee6bfe30aebf6435c56d342aececf4ececa209575`
- **Companion run:** `38850295028` (the `attempt=1` dispatch), evidence pack at `evidence/38850295028/`.

## Contents

- `rows-attempt1.json` and `rows-attempt2.json` — as downloaded from the artifact, unmodified.
- `pd-logs/` — all six logs (attempts 1 and 2 for each of the three sides). **Every side failed in both attempts**, so per D61 all failing-side logs are included; **no side reached READY, so no READY reference log exists**.

## Outcome (from the rows)

**READY 0/3 in both attempts, deterministically identical.** All three registered Ghost sides are `BUILD_FAILED` with the identical typed reason `sandbox install failed: [ELIFECYCLE] Command failed with exit code 127.` in attempt 1 and again in attempt 2:

| unit / side | registered SHA | attempt 1 | attempt 2 |
|---|---|---|---|
| C5 (control) | `5d482965b7f5ed05ffef956d0eaed8b8a51af0ef` | BUILD_FAILED | BUILD_FAILED |
| checkpoint-bug-ghost-1 fix^ | `93e0084723cdaf22d656bcd4811b87433e6b3875` | BUILD_FAILED | BUILD_FAILED |
| checkpoint-bug-ghost-1 fix | `f67640b2c18c76abd9cd3f6d19d6ae93327b42fa` | BUILD_FAILED | BUILD_FAILED |

Cause (each log's tail, both attempts): the frozen install and `pnpm run build` completed; **install step 3, the contract's `migrate` command (`pnpm run migrate:db`), failed** because Ghost's script shells out to docker, which does not exist inside the sandbox image:

```
$ docker exec --workdir /home/ghost/ghost/core ghost-dev pnpm knex-migrator migrate
sh: 1: docker: not found
[ELIFECYCLE] Command failed with exit code 127.
install step 3 (migrate) failed: exit 127
```

Provisioning stopped before start in every attempt; **GET / was never probed on any side** (no readiness or authenticated-GET evidence exists — the rows record no probe result).

**Submodule records (PR #109's `submodules` row field)** are identical across both runs and both attempts, and each `tree_sha` was independently verified against the upstream repos' git API (the tree of the recorded commit equals the materialized `tree_sha`):

| side | submodule | recorded commit | materialized `tree_sha` (verified) |
|---|---|---|---|
| C5 | `ghost/core/content/themes/casper` | `7f4adb690c40850382fed4312d90e21ff0313fdc` | `d645bc4aec20b1ee739a6221583dcd15c2f8d4d8` |
| C5 | `ghost/core/content/themes/source` | `219dc7dbe62bb6dfa8db9fd651528571eae6cfd3` | `805457d7f7e89099691e37e9e2ac1a37c672bb48` |
| fix^ / fix | `ghost/core/content/themes/casper` | `a3f491463f08d3be5315c516097831b6e9a4724d` | `0f245ecc389bb3b07f9c461c0bed83645762166a` |
| fix^ / fix | `ghost/core/content/themes/source` | `9d3414ec8c2b7f35a8e2e4b5bd09014620f21333` | `d0c55d8b8e1f29b3dbbab83c90cfd281f3dec6ca` |

Prediction scoring lives in the companion pack (`evidence/38850295028/README.md`) and the run record: predictions (PR #109 comment 6208020493, recorded before any dispatch) were GET / → HTTP 200 and READY on all three sides — **WRONG on all three**; the submodule-record sub-predictions were CORRECT.

## Redaction statement (D61)

All 8 artifact files (both rows JSONs and all six pd-logs) were scanned before committing for credential material (patterns: password/passwd/secret/token/api-key values, `Authorization`/`Bearer` headers, credential-bearing proxy URLs, `password=<value>` assignments, long opaque tokens). **Zero redactions were required: no credential, token, password, API key, or per-run secret value appears in any file.** The only "secret"-adjacent hits are pnpm install lines for Ghost's own `secretlint` packages (names only, no values). Command lines show only non-secret env (`HOME=/pd-home`, `npm_config_store_dir=/pd-home/.pnpm-store`, ephemeral sandbox ports).

## Trimming

No trimming was needed: the largest artifact file is 18,688 bytes (limit 1 MB per file). The included files are complete and unmodified apart from the copy itself.
