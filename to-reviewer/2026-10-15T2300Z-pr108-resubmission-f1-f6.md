Re: 2026-10-15T2115Z-pr108-changes-requested.md

# PR #108 RE-SUBMISSION — F1–F6 changes round (WO-6 Phase A)

**PR:** https://github.com/wally-dk24/proofdeploy/pull/108 · **Content commit:** `1a07123` (parent `78548c1`) · **Current head:** `65af10f00b986644b14325857fdda03a06b36c5b` (content + two main-only freshness merges, `8d45697` then `65af10f`). The wo6 files are byte-identical across all three heads (verified by diff); only main-side files and the manifest union changed.
**CI (direct API reads):** on `65af10f`: ci `38814809977` **SUCCESS**, sandbox-tests `38814809989` **SUCCESS**. On `8d45697`: ci `38809884005` SUCCESS; its sandbox run `38809883994` was cancelled mid-run by the freshness update (superseded by the green pair; disclosed in the PR body). **Manifest:** at the current head full=**1000** (host 919, sandbox 81) — an exact set union of main `d69d5c9`'s manifest + the 39 wo6 nodeids, verified arithmetically at each update; check OK. **Pins byte-identical.** Ruff + mypy clean (foreground, CI scope). **Predictions re-stated as UNCHANGED** (in the PR body, with the reasoning: F1 rescues wrongly-ambiguous monorepos and removes wrong-app picks in offsetting directions; F2–F6 change recording/typing, not survivor criteria). No live run.

**Per finding** (anchors on `scripts/wo6/regc_add2_run.py`):
- **F1** — `_declared_workspace_patterns` (:521): union of root `package.json` `workspaces` (array, or object form's `packages`), `pnpm-workspace.yaml` via `_parse_pnpm_workspace` (:484; block + flow forms, quoted/comment-safe), `lerna.json` `packages` with the `packages/*` default when none declared; nx/turbo contribute nothing. `TreeIndex._expand_pattern`: segment-wise matching, `*` one segment, `**` any depth, `!` subtracts. `node_filter` (:561): no patterns in workspace mode → typed `no declared workspace members`; verdict records `workspace_patterns` + `workspace_members` (a member must contain a `package.json`).
- **F2** — `TreeIndex` (:269): presence in the partial recursive set is trusted; anything else is decided from cached non-recursive contents-API listings (root / depth-one / matched dirs on demand). An unfetchable listing raises `HarnessError("tree truncated")` → recorded `harness or environment: tree truncated`, never excluded.
- **F3** — `NotFound(HarnessError)` (:88); `Fetcher.get_bytes` raises it on 404; `fetch_file_text` catches `NotFound` only — no substring matching anywhere.
- **F4** — `collect_pool` returns per-language `search_pages` metadata (every page: `returned`, `total_count`, `incomplete_results`); top-level `search_incomplete: true` when any page is incomplete.
- **F5** — (a) unparseable `package.json` → `package.json unparseable at <path>` (root and members); (b) the sqlite scan records `sqlite_scan_skipped_members`, and the not-found reason states the skip count; (c) `both_stacks_passed: true` when both verdicts pass.
- **F6 + your note** — top level carries `error_count`, `errored_repos` (`full_name` + `stars`, item-4 order), and `authenticated` (token never recorded).

**Tests:** 39 total (21 kept, 18 new), all offline, all passing locally (foreground). New coverage: F1 ×5 (nested non-member doesn't count; object-form workspaces; pnpm negation; lerna default; nx-only → `no declared workspace members`), F2 ×4 (Node root app, Python depth-one, workspace member each pass via fallback on a truncated tree; listing failure → `harness or environment: tree truncated`, `excluded: None`), F3 ×2, F4 ×1, F5 ×5, F6 ×1.

**Plants (real mutations on the committed tree; both caught, both reverted, tree clean):** F1 plant (member selection reverted to all-nested) → `test_node_nested_non_member_package_does_not_count` + `test_node_pnpm_workspace_negation` FAILED. F2 plant (fallback disabled) → all 4 F2 tests FAILED. Suite green again after reverts (39 passed); zero PLANT markers remain.

**Disclosure (F5(b) scan semantics):** the tarball scan keeps run 1's early exit on first find (the mechanism you agreed to), so `sqlite_scan_skipped_members` counts members skipped *before* the find; on a not-found verdict — the case the drop reason covers — the scan always runs to completion, so the count there is exact.

For your review. The live run (Phase B) still waits for your acceptance AND #106's merge.
