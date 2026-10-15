Re: to-reviewer/2026-10-15T1955Z-pr102-changes.md, 2026-10-15T2007Z-pr107-d18-prefix-inventory.md, 2026-10-15T1953Z-hono-rerun-result.md, 2026-10-15T2005Z-branch-freshness.md

# #102 ACCEPTED · #107 ACCEPTED · typed-refusal ruling · hono cause found

**Verdicts:**
- **#102 (1.2(h)+(i)) ACCEPTED** at content `7cc5ab75eba645ed585881941ac68859c3d2021b`, verified identical at updated head `a6410245c04d1ab3beb62eefaf04144e1db62357`.
- **#107 (1.2(j) follow-up, part 1) ACCEPTED** at `656019a17116f5b28e602811d643eacf082be6f1`.
- Both go to Master as one batch: **#102, then #107**. **Don't merge before `MASTER AUTHORIZATION (verbatim)` lands here.**

## 1. #102: what I verified
1. All five requested changes (a)–(e) are in the code as described.
2. Identity: patch-id `f04d963d…` equal for `main...7cc5ab7` and `main...a641024`; D29-filtered sha256 `abc56876…` equal.
3. Touched suites run here (test_provisioning, test_yml, test_readiness_harness): 110 passed. Manifest check on `a641024`: OK. mypy clean. Pins unchanged.
4. **My plants, both caught:**
   - scope check disabled → `test_regd_sample_lockfile_names_are_all_refused` + 1 more FAILED;
   - UTC guard widened to accept `+hh:mm` → `test_regd_offset_cutoff_is_a_typed_refusal` FAILED.
5. CI on `a641024`: owed in your next message (I have `7cc5ab7` SUCCESS from your report).
6. **Non-blocking follow-up (before the 25-side re-run):** `regd_unit_summaries` omits a unit whose trees can't be read back. Emit it instead with `regd_trees_identical: null` and the per-side `tree_error`s, so the two-side report never drops a unit silently. Also note that set comparison of `name@version` ignores location; that is fine for D4 (resolved versions), so keep it, but say so in the docstring.

## 2. #107: what I verified
1. The diff is the five D18 sites plus tests: the prefix is added and the remainder is byte-identical. No other emission changed.
2. Suites run here: 165 passed, 2 failed. The 2 are the known proxy-TLS goldens in my VM (`SELF_SIGNED_CERT_IN_CHAIN`), not defects.
3. **My plant** (prefix dropped at the Node site, provisioning.py:1857) → `test_d18_no_lockfile_refusal_is_prefixed_and_never_retried` and `test_workspace_without_lockfile_refuses_at_root` FAILED.
4. Manifest OK; pins unchanged. Trial merge of #102 then #107 onto `a4c6879`: clean, manifest OK, touched suites 110 passed.

## 3. Typed-refusal ruling (inventory in #107, 58 rows + #102's REG-D strings)

**Governing rules:**
- **R1.** The mechanical test stays `typed_reason.startswith("not supported:")`. The prefix goes **only** on strings in a not-supported class Master has approved: D18, D33, and anything he approves now.
- **R2.** Any string not ruled here, including future passthroughs (#57), defaults to **harness or environment**: retried, never skipped. Nothing becomes not-supported by sounding like it.
- **R3.** An **our gap** row never removes a unit. Under H5 the unit stays pending until we fix the gap, then it's re-provisioned.

**Agreed as proposed:** 1, 4–7, 9, 11–26, 28–55, 58.
- On #1, an impossible range (no release could ever satisfy it) is the only exception. If one occurs, I reclassify that row with evidence.
- On #21 and #24 to #26, agreed: these are retried.

**Changed by my ruling:**
- **#2 `app.path … is not a workspace package` → our gap.** The contract is ours. If the app is truly absent at an older SHA, show it, and I rule that row individually.
- **#3 `ambiguous app` → our gap.** We declare `app.path` in our contracts. (REG-C selection's own "ambiguous app" exclusion is a separate, registered mechanism and is unaffected.)
- **#10 `bun.lockb` → our gap.** Bun itself installs frozen from `bun.lockb`, so this is our planner's limit, not the repo's. **Reword it** so the text no longer contains "is not supported", e.g. `bun.lockb (legacy binary lockfile): ProofDeploy plans bun installs from bun.lock only (our gap)`.
- **#27 `contract missing 'start' command` → our gap** (agreed).
- **#56 `runtime not supported` (cannot detect runtime) → our gap.** Detection is root-only (package.json → pyproject → requirements.txt), so an app under `app.path` or a non-Node/Python repo lands here; that's our coverage. Also fix the message: it omits requirements.txt.
- **#57** → R2 (the named `proofdeploy.yml missing required keys` instance: our gap).

**Brought to Master as a new not-supported class (do not prefix until his answer):**
- **#8 `packageManager … disagrees with the lockfile`.** My recommendation is to approve it as not supported: the repo's own toolchain (corepack with the declared manager) refuses, the same principle as D33. If he approves, it gets the `not supported: ` prefix with the remainder unchanged.

**#102's REG-D strings:**
- `REG-D: sample <path> has a lockfile (…)` → **our gap.** The correct behavior there is to install the sample frozen. Not urgent: no registered nest unit's sample has a lockfile.
- `REG-D: … no ISO 8601 UTC lock cutoff` → **harness or environment** (the analogue of #5).

**Order (part 2 of the 1.2(j) follow-up, one PR after #102 and #107 merge):**
1. Add a "Ruling (reviewer, 2026-10-15)" section to `docs/typed-refusals-inventory.md` with the classes above. Add rows for the REG-D strings.
2. Apply the #10 rewording and the #56 message fix. Add the #8 prefix only if Master approves.
3. A test asserting that every inventory row ruled "not supported" starts with the prefix, and that no other row does. Plant: prefix one our-gap string → fails.

## 4. hono: cause found (our gap, our contract)
`readiness/contracts/honojs__hono.yml` on main reads `build: "pnpm run build"` (and `start: "npm create hono@latest"`). Hono installs with bun, so under 1.2(f) the pinned manager on the PATH is bun and `pnpm` is correctly absent. The "sandbox install failed: env: 'pnpm'" error is our contract's build step, not hono's tree. **Your prediction on the pin was right; the miss was our contract.**
**Order (low priority; hono is train-set):** propose `build` and `start` from hono's own `package.json` scripts and CI at the registered SHAs, citing the files. Report only; I rule before any contract change.

## 5. Branch freshness (your 2005Z note)
Fine with the gate as you describe it: acceptance names the content head, and every merge still gets the D26/D29 identity package and my verdict first. I'm asking Master to confirm the ruling in his own words so it can be logged.

## 6. Seen, not yet reviewed
- **#108 (WO-6 Phase A)** at `4a2893c`: I review it when your `to-reviewer/` package arrives (with your predictions).
- **#106**'s head `cc1dcf5` is a PLANT commit. I review only after your package names the final, plant-free head.
- **Owed:** post-merge CI on `a4c6879` (ci `38793477761`, sandbox-tests `38793477736`).
