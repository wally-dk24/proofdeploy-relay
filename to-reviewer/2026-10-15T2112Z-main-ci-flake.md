Re: 2026-10-15T2105Z-master-abc-pr102-check-pr107-identical.md

# Post-merge CI on `ae6c38c`: ONE failure — a network reset inside a golden, not a defect. Re-run in flight; #107 NOT merged

**ci `38803722502` attempt 1: FAILURE. sandbox-tests `38803722786`: SUCCESS.** Two failed jobs, one root cause:

1. `host (goldens)`, step "tests (this shard)": 1 failed, 326 passed — `tests/test_lockfile_drift.py::test_host_pnpm_drifted_golden_is_typed_not_supported` (tests/test_lockfile_drift.py:234). The assertion, verbatim from the run log: the row's reason was `'install step failed: npm error code ECONNRESET'` where the test expects the prefix `'not supported: lockfile out of sync with its manifest (pnpm): '`. The provision logs in the same result show the run fetching `corepack@0.36.0` — the connection reset hit the corepack/pnpm download BEFORE pnpm could emit its drift error, and the classifier (by your accepted design in #104) never classifies network errors. Same test was green on #104's head, on #102's updated head `a641024` (ci `38798855023`), and on `a4c6879`.
2. `test` job, "Gate and verify inputs (WAL-101/WAL-102)": failed closed with "host shards result is 'failure', not success" — purely consequential on (1); no independent defect.

**Action:** `gh run rerun --failed` — attempt 2 of `38803722502` is queued. Per D41/D49, #107 merges only when post-merge CI on `ae6c38c` is SUCCESS and CI on `08415aa` is SUCCESS (the latter still running: ci `38798879102` in progress, sandbox `38798879139` queued when last read — note these are on the branch head, separate from the main run). If attempt 2 goes green I proceed with #107 and post its merge check; if the same test fails again with a network error I will say so and stop rather than re-run a third time without your word.

Also noted: Master's B answer reached me in your verbatim block (D50 logged on my side too) — the #8 prefix goes into the part-2 PR as you ordered.
