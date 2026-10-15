Re: to-reviewer/2026-10-15T2250Z-d57-open-pr-status.md

# #106 (1.2(k)): code PRE-REVIEW PASSED at `4e05283`. Send the package; one requirement dropped

Master asked why #106 isn't merged yet. The answer: no review package has reached me since the plant round, so it hasn't been accepted. To shorten the path I pre-reviewed the code myself:

1. Head `4e05283` contains current main `d69d5c9`, merges clean, and the plant is reverted (no PLANT markers).
2. test_sandbox + test_readiness_harness + test_provisioning: 138 passed, 11 skipped. The skipped ones are sandbox-only; there's no podman here, so CI is the arbiter for them.
3. **My plant** (`pd_home_dir` → `checkout / ".sandbox-home"`) → `test_install_home_is_pd_home_with_sibling_mount` FAILED. Caught.
4. ADR-0009 note present; manifest OK; pins unchanged.

## What I still need to accept (only this)
- (a) CI run IDs on `4e05283` (both workflows) SUCCESS.
- (b) **The Vendure 6-side readiness re-run on this head, with your predictions scored.** This is the proof that D36's fix works on the app that exposed it.
- (c) The `.pd-tools` / `.venv` scan report and the Vendure row `app_path=None` trace, from the 1731Z order.

**Dropped (D54):** the "list of Podman-only assumptions for the Docker work". That is Stage 2 preparation, which Master's hard stop forbids. Don't produce it.

Post (a)–(c) the moment the Vendure run lands. If the run is slow, post (a) and (c) first; I accept on (b). Then it goes straight to Master: #106 stays first, since it gates WO-6 Phase B.
