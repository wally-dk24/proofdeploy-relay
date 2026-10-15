New: PR #110 package — #102 follow-up: `regd_unit_summaries` never drops a unit silently

# PR #110 — review package

**PR:** https://github.com/wally-dk24/proofdeploy/pull/110 · **Head:** `d479be997b49e6c5eb1d31576dbce80bc4e380cb` (base `b677f56` → fix+tests → manifest regen). 3 files, +116/−17. MERGEABLE.
**CI (direct API reads):** ci `38810633599` **SUCCESS**; sandbox-tests `38810633574` **SUCCESS**. **Manifest:** full=**955** (host 877, sandbox 78) — exactly +1 nodeid vs base, the new test. Ruff + mypy clean (foreground, CI scope). Pins byte-identical (`55b7d932`, `fa72a0e6`, `0994d1e3`, `6cb8edaf`).

**The change** (`readiness/provision.py`, `regd_unit_summaries` :479): the unit loop selects the first two rows (row order) where REG-D applied; fewer than 2 applied → no summary (unchanged — REG-D-not-on-both-sides stays omitted). Per side, the tree is obtained or an error recorded: the row's capture `tree_error` if present (or no artifact recorded), else the artifact is read back and an `OSError`/`JSONDecodeError` recorded as `"<Type>: <message>"` — the same format `_capture_regd_tree` uses. If either side errored, the unit is **emitted** as `{"unit", "regd_trees_identical": null, "tree_errors": {<role>: <error>, …}}` with only the errored sides in the map — never dropped silently. The both-trees-readable path is byte-for-byte the old behavior. **Docstring** now states the comparison is over the SET of `name@version` entries, ignores where an entry resolved, and that this is fine for D4 — your exact point — plus the null-emission contract.

**Tests + plant:** new `test_regd_unit_summaries_unreadable_trees_emit_null_summary` covers all three unreadable shapes (capture error on the row, exact `tree_errors` dict asserted; artifact missing at read-back → `FileNotFoundError…`; artifact unparseable → `JSONDecodeError…`), plus a one-sided-applied unit still omitted. Existing identical/differing tests unchanged and passing (full harness file 33 passed locally). **Plant** (emit block replaced with omit-on-unreadable) → the new test FAILED; reverted, zero PLANT markers, tests green again.

**One design choice flagged for your confirmation:** `tree_errors` is keyed by the row's `role` (`fix^`/`fix`/`C`), and read-back failures are synthesized into the map even when the row's own `tree_error` was null (capture had succeeded). This matches "the per-side `tree_error`s" while covering the missing/unparseable-artifact cases the rows don't otherwise record.

**Disclosure:** local subset runs trip the 80% coverage gate (22% on the regd subset) — an artifact of running subsets; CI's full-union run, which enforces the floor, is green on this head.

For your review, in your group-3 order (after #106/#109/#108/CI-SPEED).
