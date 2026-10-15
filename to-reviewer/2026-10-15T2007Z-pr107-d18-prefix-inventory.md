Re: 2026-10-15T1805Z-pr104-pr105-accept-ghost-hold.md

# PR #107 — plan 1.2(j) follow-up: D18 `not supported:` prefix + refusal inventory

**PR:** https://github.com/wally-dk24/proofdeploy/pull/107 — head `656019a17116f5b28e602811d643eacf082be6f1`, single commit on current main `a4c6879`. 8 files (3 source, 3 test, host manifest, the inventory doc). CI green; not merged; no readiness runs dispatched.

## (a) D18 strings — prefix only, remainder byte-identical (5 emission sites)

| Site | After |
|---|---|
| `proofdeploy/provisioning.py:1371` (Python adapter) | `not supported: no frozen lockfile found in snapshot for runtime 'python'` |
| `proofdeploy/provisioning.py:1857` (Node adapter) | `not supported: no frozen lockfile found in snapshot for runtime 'node'` |
| `proofdeploy/orchestrator.py:1343` (sandbox bail; log twin :1337) | `not supported: no frozen lockfile found in snapshot for runtime {runtime_name!r}` |
| `proofdeploy/runner.py:1415` (host bail) | `not supported: no frozen lockfile found in {snap} for runtime {runtime!r}` |
| `proofdeploy/runner.py:672` (`verify_lockfile` ValueError) | `not supported: no frozen lockfile found in {snapshot_dir} for runtime {runtime!r}` |

Tests asserting the old strings updated (test_provisioning.py:791, test_runner.py:153, :370). A final grep confirms no unprefixed emission remains in `proofdeploy/` or `readiness/` (one prose comment at runner.py:183 names the mechanism — not an emission, left deliberately).

## (b) The inventory — `docs/typed-refusals-inventory.md` (in the PR, for your ruling)

**58 entries**, each verbatim with file:line and a proposed class (*not supported* / *harness or environment* / *our gap*). The ones that matter most for H5/J1, because they are proposed **not supported** but do NOT carry the prefix today (so the mechanical non-retry/skip test misses them):

- `app.path {declared!r} is not a workspace package in this snapshot (…)` — provisioning.py:616
- `ambiguous app: {n} candidates` — provisioning.py:621
- `packageManager {pm}@{v} disagrees with the lockfile present in the snapshot (…)` — provisioning.py:1843
- `bun.lockb … is not supported: bun installs are planned from bun.lock only …` — provisioning.py:1872 ("is not supported:" is mid-string; `startswith` misses it)
- `runtime not supported` (harness mapping of "cannot detect runtime in {dir}: no package.json or pyproject.toml") — readiness/provision.py:283; the side is re-provisioned on attempt 2 today

Everything else: the D33 drift string + the five D18 sites (not supported, prefixed); all SandboxError strings and install/start/readiness failures (harness or environment); the unpinned-bun / workspace-not-yet / runtime-range strings (our gap). Scope notes in the doc: the frozen container-spike harness (1.12) is excluded (its rows don't feed H5/J1); PR #102's `REG-D: …` strings are not on this main yet and need inventory rows when #102 lands. Your ruling on the classes is the input; no string beyond D18's was changed in this PR.

## (c) Test + plant

New test `test_d18_no_lockfile_refusal_is_prefixed_and_never_retried`: exact prefixed refusal from the Node adapter; the readiness row carries it; `_is_not_supported_row` true; `_rerun_targets` excludes the side while still returning a generic failure row. **Plant:** prefix dropped at the Node site → test FAILED (AssertionError showing the unprefixed string). Reverted; test passes.

## Gates

- **CI on `656019a`: ci `38795076214` SUCCESS; sandbox-tests `38795076196` SUCCESS** (direct API reads; CI's lint re-ran ruff + mypy + manifest check, all green).
- **Manifest:** full=928 (host 850, sandbox 78) = base 927 + the new test; check-verified from the script's own foreground output.
- Ruff "All checks passed!"; mypy "Success: no issues found in 20 source files" (foreground, CI scope). Local: test_lockfile_drift 22 passed; test_provisioning + test_readiness_harness 65 passed; test_runner 80 passed.
- **Pins byte-identical** (all four).

## Disclosure

Plant-revert slip, recovered and re-verified: reverting the plant via `git checkout -- proofdeploy/provisioning.py` also reverted that file's two legitimate prefix edits. Both were immediately re-applied; the final tree was re-verified three ways (grep shows all sites prefixed, diff shape identical to the pre-plant diff, touched suites green, CI green on the pushed head). The shipped head is correct; the slip is on the record.
