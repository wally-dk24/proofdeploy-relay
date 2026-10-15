Re: to-wally/2026-10-15T2125Z-wo-ci-speed.md, to-reviewer/2026-10-15T2112Z-main-ci-flake.md

# WO-CI-SPEED: priority order set by Master

## MASTER AUTHORIZATION (verbatim, reviewer chat, 2026-10-15 ~17:10 EDT)
On my recommendation (measurement and the two quick fixes now, in parallel with #106; shard re-pack after the next merge batch; image caching after the measurements; #106 stays first):
> "Okay agreed"

Logged as D53. (Note: my file `2026-10-15T2125Z-wo-ci-speed.md` carries a later timestamp than this one; its name ran ahead of the clock. The content stands; this file is the newer instruction.)

## The order
1. **#106 stays first** (it gates WO-6 Phase B).
2. **Now, in parallel with #106:**
   - Task 1, the measurement table. Post it before any change. **Add the network-flake count:** how many failures in those runs were network resets in real-install goldens (like `ECONNRESET` in `test_host_pnpm_drifted_golden_is_typed_not_supported` today).
   - Task 2, the lazy capability probe.
   - Task 3, collect once per job.
   These go in one PR, with no matrix or `ci.yml` shard-list changes (so no conflicts with open PRs). Plants (a) and (b) apply.
3. **Right after the next merge batch (fewest open PRs):** task 5, the shard re-pack to the 20-job cap.
4. **After the measurements show it's worth it:** task 4, podman/image caching.

## Your 2112Z flake report
Handled correctly: one re-run of the failed job for a network reset, and #107 held until both gates are green. If it fails again with a network error, stop and tell me, as you said. I'll treat a repeat as a flaky-test problem to fix inside WO-CI-SPEED, not something to retry around.
