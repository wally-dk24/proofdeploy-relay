# Evidence pack — readiness run 38850295028 (Ghost re-run, plan 1.11; D46 Option A live after PR #109)

- **Run ID:** 38850295028 (GitHub Actions, repo `wally-dk24/proofdeploy`)
- **Workflow:** `.github/workflows/readiness.yml` (`repo=TryGhost/Ghost`, `attempt=1`)
- **Head SHA the run was dispatched on:** `9f7f3ad950bbb9a0f506a0225b7aa196a004094f` (main, PR #115 merge; contains PR #109, plan 1.2(l) submodules at pinned commits). The run record's own `head_sha` confirms this.
- **Run conclusion:** success (job 04:22:41Z–04:31:42Z, 2026-10-16)
- **Artifact name:** `readiness-TryGhost-Ghost-1` (artifact id 12025750556)
- **Artifact zip sha256 (as downloaded):** `b099f8def655579540be35157381ec004b344e7485ca080fc46561f0c6d3d48a`

## Contents

- `rows-attempt1.json` — as downloaded from the artifact, unmodified. **No `rows-attempt2.json` exists in this run:** the dispatch was `attempt=1`. Because every side was non-READY, a second dispatch with `attempt=2` was run separately — run `38851152056`, evidence pack at `evidence/38851152056/` (its artifact carries both attempts' rows and logs).
- `pd-logs/` — all three sides' logs. **Every side failed**, so per D61 all failing-side logs are included; **no side reached READY, so no READY reference log exists**. All three sides failed at the identical step, so no side progressed further than another.

## Outcome (from the rows)

**READY 0/3.** All three registered Ghost sides are `BUILD_FAILED` with the identical typed reason `sandbox install failed: [ELIFECYCLE] Command failed with exit code 127.`:

| unit / side | registered SHA | status |
|---|---|---|
| C5 (control) | `5d482965b7f5ed05ffef956d0eaed8b8a51af0ef` | BUILD_FAILED |
| checkpoint-bug-ghost-1 fix^ | `93e0084723cdaf22d656bcd4811b87433e6b3875` | BUILD_FAILED |
| checkpoint-bug-ghost-1 fix | `f67640b2c18c76abd9cd3f6d19d6ae93327b42fa` | BUILD_FAILED |

Decisive evidence (each side's log, tail): the frozen install (step 1) and `pnpm run build` (step 2) completed; **install step 3, the contract's `migrate` command (`pnpm run migrate:db`), failed** — Ghost's script shells out to docker:

```
$ docker exec --workdir /home/ghost/ghost/core ghost-dev pnpm knex-migrator migrate
sh: 1: docker: not found
[ELIFECYCLE] Command failed with exit code 127.
install step 3 (migrate) failed: exit 127
```

There is no `docker` binary inside the sandbox image, so the migrate step cannot run; provisioning stopped before start, and **GET / was never probed on any side** (no readiness or authenticated-GET evidence exists in this run — the rows record no probe result).

**What DID work — the PR #109 submodule materialization (the point of plan 1.2(l)):** every row's `submodules` list records both theme submodules at exactly the commits recorded in the superproject, and the `tree_sha` in each row was independently verified against the upstream repos' own commit objects (GitHub git API): the tree of the recorded commit equals the `tree_sha` the harness materialized, for all four distinct theme commits.

| side | submodule | recorded commit | materialized `tree_sha` (verified = upstream tree) |
|---|---|---|---|
| C5 | `ghost/core/content/themes/casper` | `7f4adb690c40850382fed4312d90e21ff0313fdc` | `d645bc4aec20b1ee739a6221583dcd15c2f8d4d8` |
| C5 | `ghost/core/content/themes/source` | `219dc7dbe62bb6dfa8db9fd651528571eae6cfd3` | `805457d7f7e89099691e37e9e2ac1a37c672bb48` |
| fix^ / fix | `ghost/core/content/themes/casper` | `a3f491463f08d3be5315c516097831b6e9a4724d` | `0f245ecc389bb3b07f9c461c0bed83645762166a` |
| fix^ / fix | `ghost/core/content/themes/source` | `9d3414ec8c2b7f35a8e2e4b5bd09014620f21333` | `d0c55d8b8e1f29b3dbbab83c90cfd281f3dec6ca` |

Predictions were recorded before dispatch on PR #109 (comment 6208020493): GET / → HTTP 200 and READY on all three sides. **Scored: WRONG on all three sides** (BUILD_FAILED at the migrate step; GET / never reached). The submodule-record sub-predictions (recorded commits per side) were CORRECT. The predictions' premise — that prior Ghost passes reached the readiness probe — does not hold for the sandboxed path with the current contract: the `migrate:db` docker dependency blocks provisioning earlier than the theme defect the 1.11 report measured. Classifying that gap (contract shape vs app assumption) is the reviewer's call; no classification is asserted here beyond the rows' typed reason.

## Redaction statement (D61)

All 4 artifact files (the rows JSON and all three pd-logs) were scanned before committing for credential material (patterns: password/passwd/secret/token/api-key values, `Authorization`/`Bearer` headers, credential-bearing proxy URLs, `password=<value>` assignments, long opaque tokens). **Zero redactions were required: no credential, token, password, API key, or per-run secret value appears in any file.** The only "secret"-adjacent hits are pnpm install lines for Ghost's own `secretlint` packages (a lint tool dependency; names only, no values). Command lines show only non-secret env (`HOME=/pd-home`, `npm_config_store_dir=/pd-home/.pnpm-store`, ephemeral sandbox ports).

## Trimming

No trimming was needed: the largest artifact file is 18,668 bytes (limit 1 MB per file). The included files are complete and unmodified apart from the copy itself.
