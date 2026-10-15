New:

# PR #104 — plan 1.2(j): typed "not supported: lockfile out of sync with its manifest" (D33)

**PR:** https://github.com/wally-dk24/proofdeploy/pull/104 — head `c49ff917f446197abed792f1ec7c47f14a9dd3ca` (2 commits: `10fdc14` implementation + `c49ff91` CI shard fix), branch `feat/lockfile-drift-typed`, base main `4aaa086`. Built now per your correction; the rebase onto the post-#102 main comes after #102 merges. Not merged; no readiness workflows dispatched.

**Files (24):** `proofdeploy/runner.py` (classifier `_lockfile_drift_reason` + both host install bail sites), `proofdeploy/orchestrator.py` (sandbox wiring, install-phase only), `readiness/provision.py` (`_rerun_targets` excludes not-supported rows), `tests/test_lockfile_drift.py` (21 tests), `tests/fixtures/drift/` (10 captured outputs + npm/pnpm golden apps), `tests/expected_host_nodeids.txt`, `.github/workflows/ci.yml` (shard assignment).

## Per-manager evidence (all from REAL runs of the pinned version on a drifted project, 2026-10-15; captures committed as fixtures)

| Manager | Pinned | Exact refusal line |
|---|---|---|
| npm | 10.9.4 | `npm error \`npm ci\` can only install packages when your package.json and package-lock.json or npm-shrinkwrap.json are in sync. Please update your lock file with \`npm install\` before continuing.` (`npm ci`, lodash 4.17.20 lock / 4.17.21 manifest, exit 1 EUSAGE; `does not satisfy` / `Missing: … from lock file` patterns also matched) |
| pnpm | 9.15.0 | `ERR_PNPM_OUTDATED_LOCKFILE` — `Cannot install with "frozen-lockfile" because pnpm-lock.yaml is not up to date with <ROOT>/package.json` (`pnpm install --frozen-lockfile`, exit 1) |
| yarn classic | 1.22.22 | `error Your lockfile needs to be updated, but yarn was run with \`--frozen-lockfile\`.` (exit 1) |
| yarn berry | 4.18.1 | `YN0028: The lockfile would have been modified by this install, which is explicitly forbidden.` (`yarn install --immutable`, exit 1) |
| bun | 1.4.3 (digest-verified) | `error: lockfile had changes, but lockfile is frozen` (`bun install --frozen-lockfile`, exit 1) |
| uv | 0.13.0 | ``error: The lockfile at `uv.lock` needs to be updated, but `--check` was provided.`` (`uv lock --check`, exit 1) |
| pdm | 2.29.2 | `WARNING: Lockfile doesn't satisfy the project requirements, packages may be outdated` (`pdm lock --check`, exit 1) |
| poetry | 2.5.1 | ``pyproject.toml changed significantly since poetry.lock was last generated. Run `poetry lock` to fix the lock file.`` (`poetry install --sync`, exit 1) |
| pip | — | **Untyped — no drift class exists:** pip has no manifest/lock pair; the requirements file IS the lock |

No manager was left untyped for ambiguity.

## Tests, plants, CI

- New file: **21 passed** (incl. REAL host installs on the drifted npm + pnpm goldens), locally and in CI's goldens shard.
- **Plant 1** (classifier returns `None`): 11 tests FAIL (8 classifier cases, sandbox drift, both goldens) — caught, reverted.
- **Plant 2** (npm patterns widened to any `npm error` line): all negatives FAIL (7: classifier network/ERESOLVE/generic + host + sandbox network) — caught, reverted. Negatives use real captures: npm ECONNREFUSED and ERESOLVE classify as `None` and keep generic reasons at all three levels.
- **CI on `c49ff91` (direct API reads):** ci `38775142864` SUCCESS; sandbox-tests `38775143121` SUCCESS. First head `10fdc14` FAILED ci at lint only — the manifest shard-coverage check caught the new test file assigned to no shard; host shards all passed. Fixed in `c49ff91` (file → goldens shard) and disclosed on the PR.
- **Manifest:** full=920 (host 843, sandbox 77) — regenerated, checked clean from the script's own foreground output.
- ruff clean; mypy clean (CI scope). **Pins byte-identical** on the head.

## Disclosures

1. **A local regression figure in my builder's first report was unreliable and is corrected here:** two deliveries of the same background session disagreed (240 passed/22 skipped vs 149 passed/1 warning); both agree on zero failures across test_wo7_pr2 + test_readiness_harness + test_runner + test_sandbox_addendum18. The load-bearing verification is CI on `c49ff91`, which ran the entire host suite across its shards, all SUCCESS. Treat the local counts as unreconciled; the CI conclusion is unaffected.
2. **"Never retried" interpretation:** implemented in the readiness harness's only retry mechanism — attempt-2 `_rerun_targets` now excludes rows whose typed reason starts with the registered `not supported:` prefix. D18's own refusal strings don't carry that prefix and keep today's attempt-2 behaviour — flagged as your call.
3. **Classification boundary:** classification is install-phase only. A contract build step that itself runs the manager (nest-style `npm ci && build`) and hits drift stays a generic, retried BUILD_FAILED — deliberate, with a guard test; the typed class is the plan's frozen install refusing.
4. **Rebase surface vs #102:** zero overlap in `provisioning.py` (the classifier lives in `runner.py`); the only shared file is `readiness/provision.py`, confined to `_rerun_targets` + one helper.
5. **Prediction (also in the PR body):** in the 25-side re-run, nest H1 (both sides) + the 4 held-out cleans show the typed npm reason; H2–H4 do not.
6. One local wo81b run was lost to a VM disconnect and re-run clean; scratch capture projects remain at `~/workspace/drift-scratch/` if you want them.
