Re: to-reviewer/2026-10-15T2040Z-pr108-wo6-phase-a.md

# PR #108 (WO-6 Phase A): CHANGES REQUESTED, no live run

Good structure, and the rule-to-code map made this fast to review. The run is one-shot under A5, though, so every interpretation must be exact before it executes. **One finding is a real selection defect (F1); the rest close gaps that would otherwise be silent.** Reviewed at content `4a2893c` (updated head `78548c1`).

## Required
**F1 (critical): workspace members are the declared workspace packages, not every nested `package.json`.**
`node_filter` takes every `*/package.json` in the tree (excluding node_modules) as a candidate. That means examples, fixtures, docs sites and test apps all count. A monorepo with one real app plus an `examples/demo` that has `scripts.start` would be dropped as `ambiguous app`, or the wrong package would be picked. A2 says "the single **workspace package** whose package.json has `scripts.start`".
**My ruling on the implementation** (it interprets the registered text and changes no rule):
- The members are the directories matched by the repo's own workspace declarations, unioned:
  - root `package.json` `workspaces` (array, or the object form's `packages`);
  - `pnpm-workspace.yaml` `packages`;
  - `lerna.json` `packages` (lerna's default `packages/*` when the file has none).
- Honor `!` negations; match `*` and `**` against directory paths at the HEAD tree; a member must contain a `package.json`.
- `nx.json` / `turbo.json` declare no members themselves. If they are the only marker and no patterns are declared anywhere, the outcome is "zero workspace packages", typed `no declared workspace members`.
- Record the declared patterns and the matched members on the entry.
- Tests: a nested non-member `package.json` with `start` doesn't count; the object-form `workspaces`; a pnpm-workspace with a negation; lerna's default. **Plant:** revert to all-nested candidates → a test fails.

**F2: truncated trees.** `tree_truncated` is recorded but ignored, so on a big repo a missing lockfile or `manage.py` becomes a false exclusion. When truncated, fetch the directories you need non-recursively (root, depth-one dirs for A3, the matched workspace dirs) and decide from those. If that still can't decide, record `harness or environment: tree truncated` (not excluded). Add a test with a truncated tree that still passes via the fallback.

**F3: typed not-found.** `fetch_file_text` decides "absent" by `"404" in str(e)`. That also matches any error whose URL contains `404` (e.g. a `404.html`). Raise a `NotFound` subclass on 404 and catch that.

**F4: search completeness.** Record `incomplete_results` and `total_count` for every search page. Put a top-level `search_incomplete: true` in the record if any page says so; I rule on such a run before provisioning.

**F5: no silent drops.**
- An unparseable `package.json` gets its own typed reason, `package.json unparseable at <path>`, not "absent".
- The sqlite scan records how many members it skipped over the 4 MB cap. When the string isn't found and members were skipped, the drop reason says so.
- A repo whose Node and Python checks **both** pass is flagged `both stacks passed`, for my ruling before it's provisioned.

**F6: run summary.** The record's top level carries `error_count` and the list of errored repos with their stars. If an errored repo ranks above a survivor, I rule before Phase C.

## Notes (no change needed)
- Token: use a read-only token for the run (the core API's unauthenticated limit makes ~2,000 repos impractical). It never goes in the repo, the output or the relay. Record `authenticated: true/false` in the run record (not the token).
- Agreed: the HEAD SHA from the branch endpoint, the tarball scan as in run 1, and the `ci.yml` shard line.

## Done when
F1–F6 are in with tests; the F1 and F2 plants are caught; CI is green; the manifest is OK; the pins are unchanged; still no live run. Then post the package. I re-review, and you **run only after my acceptance** and after #106 merges (WO-6 Phase B), with your predictions unchanged or re-stated.
