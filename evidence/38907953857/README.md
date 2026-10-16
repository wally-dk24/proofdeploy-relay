# Evidence pack — Ghost D83 re-run, attempt 1

- Run ID: 38907953857
- Workflow: readiness (.github/workflows/readiness.yml), dispatched from PR #119 branch fix/ghost-contract-d71
- Head SHA: ae6e808d73a469110aaf5a596f8a05e655f35902
- Artifact: readiness-TryGhost-Ghost-1 (id 12049178354)
- Artifact zip sha256 (as downloaded): 8f59f7e74bcb8a8a38211832c814777f3dead8e70ddce5146635dd608f971b76
- Configuration: D71 contract (migrate removed; start = upstream nodemon exec minus the watcher) + D83 build: `pnpm run build && pnpm --filter ghost run build:assets`
- Predictions were posted on PR #119 before this dispatch: https://github.com/wally-dk24/proofdeploy/pull/119#issuecomment-6216525242

## Contents

- rows-attempt1.json — the run's rows as uploaded.
- readiness-checkpoint-bug-ghost-1-fix-a1.log — the fix side's pd-log, included as the READY reference. There are no failing sides in this run, so no failing-side logs exist to include.

## Outcome

READY 3/3 at attempt 1 (no attempt 2 needed — attempt 2 re-runs only non-READY sides):

- fix f67640b2: READY; build:assets js/cards/css all "Done"; GET / 200 in the app log; `sandbox app READY`.
- fix^ 93e00847: READY; same asset-build and GET / 200 evidence.
- C5 5d482965: READY; same asset-build and GET / 200 evidence.

Submodule records carry exactly the registered pins on every side (casper/source commits and materialized tree_shas identical to the pre-D83 packs).

Note: the readiness path for this contract has no separate authenticated-GET probe; its proof is provisioning + the GET / readiness probe. The predictions' authenticated-GET element is therefore not exercised by this run (scored as such on the PR).

## Scrub

All artifact files were scanned for credential patterns before commit. Zero redactions required — the only matches were Ghost's own `secretlint` package names in pnpm install output. No trimming was needed (largest file 23,514 bytes).
