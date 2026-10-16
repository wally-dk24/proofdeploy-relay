# Plan 1.2(n) — tool-state relocation: DESIGN NOTE (report only)

**Date:** 2026-10-16 · **Prepared by:** Wally, for the reviewer's work order.
**Scope:** design only. No code, contract, or test has been changed; nothing dispatched.
**Code base cited:** proofdeploy main `9f7f3ad` (PR #117's cleanup change cited separately, from its branch head `4ffeffc`, where noted).
**Queue position:** 1.2(n) must land BEFORE the 25-side readiness re-run.

## 0. What (n) is, in one paragraph

ProofDeploy's own tool state currently lives **inside the app checkout it measures**: the
`.pd-tools` tree (the Python lock managers uv/poetry/pdm, the corepack package and its
manager shims, the pinned bun binary — and, for Python, a whole tools venv) and the app
venv `.venv`. Every sandbox step container mounts the checkout at `/checkout`, so the
state persists across the side's per-step `--rm` containers — but it also sits in the
app's tree, where a workspace scanner can discover it (the exact failure class plan
1.2(k) already fixed once for HOME-riding caches: Vendure's build died when nx's
workspace scan found a `project.json` inside bun's HOME cache and refused with
`WorkspaceValidityError`), where a broad glob in the app's own build/test can ingest it,
and where it mixes our state with the snapshot under test. 1.2(k) moved the HOME-riding
state to the per-side `/pd-home`; (n) moves the two remaining in-tree trees to the
same place.

---

## 1. Current state, mapped

### 1.1 What `.pd-tools` holds, and who creates it

All creation is **plan data** in `proofdeploy/provisioning.py` — argv templates with a
`{checkout}` placeholder, rendered per executor (§1.3). Three tenants:

| Tenant | Created by | Shape inside `.pd-tools` |
|---|---|---|
| Python lock managers (uv 0.13.0, poetry 2.5.1, pdm 2.29.2) + the REG-A lock-generation resolver (uv) | `_manager_bootstrap_steps` (`provisioning.py:1194`), called from `PythonAdapter.plan` (committed-lock path `:1397ff`) and the generated-lock path (`:1535ff`) | **`.pd-tools` IS a venv**: `python -m venv {checkout}/.pd-tools`; the committed bootstrap requirements file is materialized verbatim into it (`UV_BOOTSTRAP_CHECKOUT_PATH = ".pd-tools/uv-requirements.txt"` `:764`; poetry/pdm at `:804-805`), then `{checkout}/.pd-tools/bin/pip install --require-hashes --no-deps --only-binary :all:` puts the manager at `{checkout}/.pd-tools/bin/<manager>` (`_manager_frozen_steps` `:1265`) |
| corepack 0.36.0 (for `packageManager`-pinned pnpm/yarn/npm plans) | `NodeAdapter._install_plan` (`provisioning.py:2124-2145`) | `npm install --prefix {checkout}/.pd-tools --no-save corepack@0.36.0` → the package at `.pd-tools/node_modules/corepack`, shims at `COREPACK_BIN = "{checkout}/.pd-tools/node_modules/.bin/corepack"` (`:1679`). The downloaded manager itself rides `COREPACK_HOME` on HOME — already in `/pd-home` since 1.2(k) (comment `:2120-2124`) |
| bun (digest-pinned, plan 1.2e) | `_bun_bootstrap_step` (`provisioning.py:1759`) from `_install_plan` (`:2061ff`) | One `bash -c` step: `d={checkout}/.pd-tools; mkdir -p $d/bin $d/.bun-dist; curl … -o $d/bun.zip; sha256sum -c; unzip …; cp … $d/bin/bun` → `BUN_BIN = "{checkout}/.pd-tools/bin/bun"` (`:1752`) |

### 1.2 The app venv `.venv`

- Plan step `["{runtime_bin}", "-m", "venv", "{venv}"]` opens every Python plan
  (committed `:1390`, hashed-requirements `:1436`, generated `:1536`).
- Sandbox renders `{venv}` → `/checkout/.venv` (`orchestrator.py:1466`, argv_mapping);
  the sandbox-only verify step names the literal `/checkout/.venv/bin/python`
  (three plan sites: `provisioning.py:1399`, `:1455`, `:1627`).
- uv is pointed at it by plan env `UV_PROJECT_ENVIRONMENT={venv}`
  (`provisioning.py:1267`); poetry is told `POETRY_VIRTUALENVS_IN_PROJECT=true`
  (`:1302`) and adopts the pre-created in-project venv; pdm's `sync` discovers the
  in-project `.venv` itself.
- **Host executor asymmetry (existing precedent):** for pip plans the host renders
  `{venv}` to a per-run dir **outside** the checkout — `Provisioner._make_venv`
  (`runner.py:1078`) creates `workdir/venvs/<run_id>` — while for uv/poetry/pdm the
  host executor itself creates `snap/.venv` (`runner.py:1541-1550`). Executor-specific
  `{venv}` rendering is already the house pattern; (n) extends it, it does not invent it.

### 1.3 Every place the in-tree paths are rendered or referenced

**Plan → executor rendering.** `render_argv` (`provisioning.py:2268`) substitutes
`{checkout}` / `{venv}` / `{runtime_bin}` from an executor-supplied mapping:

- Sandbox (`orchestrator.py:1462-1477`): `{checkout}` → `/checkout`,
  `{venv}` → `/checkout/.venv`.
- Host (`runner.py:1632-1637`): `{checkout}` → the snapshot's host path,
  `{venv}` → the executor's venv dir.

**Contract-step / start wrapper (plan 1.2(m))** — `_sandbox_step_command`
(`orchestrator.py:242`, renders at `:289-306`), used for every sandbox contract step
and the start command (`orchestrator.py:1672ff`):

```text
corepack plan:  export PATH=/checkout/.pd-tools/node_modules/.bin:/checkout/node_modules/.bin:$PATH; {cmd}
bun plan:       export PATH=/checkout/.pd-tools/bin:/checkout/node_modules/.bin:$PATH; {cmd}
plain node:     export PATH=/checkout/node_modules/.bin:$PATH; {cmd}
python (bare):  /checkout/.venv/bin/python {rest}
python (other): export PATH=/checkout/.venv/bin:$PATH; {cmd}
```

**Host executor PATH assembly** (the host rendering of the same facts,
`runner.py:1562-1595`): `manager_bin = snap/".pd-tools"/"node_modules"/".bin"`
(`:1576`) for corepack plans, `bun_bin = snap/".pd-tools"/"bin"` (`:1588`) for bun
plans, each prepended to `first_on_path`; the venv bin dir likewise (`:1530`, `:1560`).

**`persisted_dirs` (plan record).** Python plans record `[".venv", ".pd-tools"]`
(`provisioning.py:1424`, `:1641`; hashed-pip plans `[".venv"]` only, `:1474`); Node
plans record `["node_modules"]` plus the member's `node_modules` on workspace plans
plus `".pd-tools"` when a corepack or bun bootstrap exists (`:2178-2185`). **It has no
runtime consumer in `proofdeploy/` or `readiness/`** — persistence is actually
provided by the bind-mounted checkout surviving across step containers; the field is
recorded plan data, is asserted by tests (§2.1), is echoed in conformance evidence
(`tests/test_conformance.py:233`), and **feeds `InstallPlan.sha256()`**
(`provisioning.py:382`), which the readiness rows record per side
(`readiness/provision.py:268`, `:311`).

### 1.4 The per-side home (plan 1.2(k)) — the destination

`proofdeploy/sandbox.py`:

- `PD_HOME_CONTAINER = "/pd-home"` (`:132`); host side:
  `sandbox_home_dir(checkout) = checkout.parent / f"{checkout.name}.pd-home"` (`:136-144`)
  — a sibling of the snapshot, unique per side and run, never inside the tree.
- Mounted read-write in **every** step container of the side: install steps via
  `_common_args` (`:978-990`, which also creates the host dir so podman doesn't create
  it root-owned) and the app container at `:1376`. The checkout itself mounts at
  `/checkout` (`:980-983`). `SandboxConfig` documents the posture: "the sandbox's own
  mounts are the checkout and the per-side HOME (/pd-home, plan 1.2(k)) only" (`:525-528`).
- `HOME=/pd-home` is set for install steps (`:1162`) and the app (`:1235`); pnpm's
  store is pinned into it (`npm_config_store_dir=/pd-home/.pnpm-store`, `:1181-1186`)
  unless the app declares its own store.
- Ownership: `_prepare_checkout` (`:1040ff`) transfers ownership of the checkout
  **and** the home dir to the sandbox's mapped runtime uid (the 1.2(g) mechanism), so
  the install process owns what it writes under `/pd-home`.
- Lifecycle: the home "dies with the run's workdir, through the same cleanup path as
  the checkout itself" (`:138-144`) — i.e. by containment: it is a sibling of the
  snapshot inside the run's scratch tree.
- The 1.2(k) mount itself is recorded as a dated posture note in ADR-0009
  (module docstring `:55-69`).

### 1.5 Who walks the tree (and when)

- `fixture.create_snapshot` (`fixture.py:297`) extracts `git archive` output — the
  tree is pristine by construction; `.pd-tools`/`.venv` do not exist yet.
- `fixture._scan_snapshot_tree` (`fixture.py:216-246`, used by
  `assemble_author_bundle`) is an `os.walk` with **no name exclusions** (it refuses
  `.git`, validates symlinks, copies everything else into the author bundle). In the
  measured flow it runs on the pristine snapshot, before provisioning — ordering
  protects it today. Run on a provisioned tree it would ingest our tools wholesale.
- `proofdeploy/submodules.py` (plan 1.2(l)) materializes submodules at snapshot
  creation and has **no reference** to `.pd-tools`/`.venv` — nothing to change there,
  and nothing protects a post-provisioning walk either.
- `leakcheck.py:151,197` `rglob`s the snapshot — again on the pristine tree.
- Detection/planning (`detect`, `find_lockfile`, workspace globs in provisioning.py)
  all run on the pristine snapshot, before any step executes.
- No readiness contract under `readiness/contracts/` names `.venv` or `.pd-tools`
  literally (grep-verified) — contracts reach the tools only through the wrapper's PATH.

---

## 2. Interactions

### 2.1 Tests that pin the strings (must change)

- **#113 wrapper tests** — `tests/test_wo7_pr2.py:425,432,527,1545,1555` assert the
  exact rendered `export PATH=/checkout/.pd-tools/…` strings (corepack and bun
  branches); `:1565,1569` assert the Python wrapper forms
  (`export PATH=/checkout/.venv/bin:$PATH; …`, `/checkout/.venv/bin/python …`).
- **Conformance goldens** — `tests/test_conformance.py:80-89` pin the golden apps'
  install argv (`/checkout/.pd-tools/node_modules/.bin/corepack`,
  `/checkout/.pd-tools/bin/bun`); `:233` asserts `persisted_dirs` in the recorded
  provision.
- **Plan-shape tests** — `tests/test_provisioning.py:192,369,458,603-604,728-729`
  assert `persisted_dirs` contents; `tests/test_provisioning_sandbox.py:280` pins
  the rendered sandbox bun argv; `tests/test_wo81b.py:240` pins the verify step's
  `/checkout/.venv/bin/python`. In total, 93 `.pd-tools` hits across 13 test files
  (plus the nodeid manifests, which only change if tests are added/removed).
- **Mirror-image precedent** — `tests/test_sandbox.py` + `tests/fixtures/pd-home-scan/`
  are the 1.2(k) regression: a fixture app whose build runs a workspace scanner over
  `$HOME`'s old in-tree location. (n) should land with the same shape of test aimed
  at the checkout root (Q6 below).

### 2.2 The 1.2(f) manager-on-PATH rule

The rule is about **which dirs go first, from the plan's own fact** (corepack shim dir
ahead of `node_modules/.bin` for corepack plans; `.pd-tools/bin` ahead for bun plans;
venv bin first for Python) — not about the base path. Relocation changes only the
prefix (`/checkout/` → `/pd-home/`) in both renderers (`orchestrator.py:289-299`,
`runner.py:1576-1595`). PATH **order is unchanged**; no rule re-ruling needed. Note
the corepack shim's own behavior is location-independent: a shim resolves the
snapshot's `packageManager` pin by walking up from the step's cwd (the checkout),
wherever the shim binary itself lives.

### 2.3 Snapshot hash / verify

Nothing hashes or verifies the checkout tree **after** provisioning today: the
snapshot tree hash (`fixture.py`, `snapshot_tree_sha256`) is computed at bundle
assembly on the pristine tree, and the plan's lockfile hashes are of committed files
read at plan time. So relocation cannot break a post-provisioning verify — there is
none — and it removes the hazard class for any future one: after (n), a whole-tree
walk of a provisioned checkout sees only the app's tree plus `node_modules` (the
app's own install product, which stays; §3.2).

### 2.4 Cleanup

`Provisioner.cleanup_run` (`runner.py:1170`, main) removes exactly
`workdir/venvs/<run_id>` and `workdir/runs/<run_id>` (`shutil.rmtree`,
`ignore_errors=True`). It never names `.pd-tools` or `.venv`: they die by
**containment**, because the snapshot lives under `runs/<run_id>`. PR #117 (branch
head `4ffeffc`) routes the same two dirs through `_remove_tree` (`runner.py:1068` —
userns-aware `podman unshare` removal + leftover logging) without changing *which*
dirs are covered. **Relocation does not change cleanup coverage as long as the
destination home stays inside the run's scratch tree** (it does: §1.4) — the tools
die with the home, the home dies with `runs/<run_id>`. Two watch-items: (a) coverage
is by containment only — no test today asserts the `.pd-home` sibling is actually
removed; (n) should add one; (b) PR #117's foreign-owner routing matters *more*
after relocation, since the tools tree is exactly the userns-remapped content
`_remove_tree` was built for.

### 2.5 Evidence comparability (readiness rows / logs)

- Recorded `executed_steps` (Addendum 31) quote the rendered commands verbatim —
  e.g. the Vendure D61 pack's executed start line
  `export PATH=/checkout/.pd-tools/bin:/checkout/node_modules/.bin:$PATH; cd packages/dev-server && bun run dev:server --no-reload`.
  After (n) the same lines read `/pd-home/…`. 
- `install_plan_sha256` changes for **every** side whose plan carries a bootstrap or
  a venv step (the step argv and `persisted_dirs` are hashed plan data).
- Consequence: pre-(n) and post-(n) rows are **not string-comparable**, by design.
  The 25-side re-run is a fresh baseline; the walk's J1/Addendum-1 comparisons must
  be made within the new run's rows, not against attempt-1 text. Stated here so it
  is a known property of the re-run, not a mid-walk surprise.

---

## 3. The design

### 3.1 New locations (exact path shapes)

One destination root per executor, mirroring the existing `{venv}` asymmetry:

| | Sandbox (in-container) | Host (on-disk) |
|---|---|---|
| Tools root | `/pd-home/.pd-tools` | `sandbox_home_dir(snap)/.pd-tools` |
| App venv (if Q1 rules it moves) | `/pd-home/.venv` | `sandbox_home_dir(snap)/.venv`, except pip plans, which keep `workdir/venvs/<run_id>` (already outside the tree) |

`sandbox_home_dir()` is already a pure path function of the checkout
(`sandbox.py:144`), so the host executor can address the same sibling dir with no
mount concept. In the sandbox the dir needs no new mount: `/pd-home` is already
mounted read-write in every step container of the side (§1.4), and its ownership is
already transferred to the mapped uid. **No new mount, no posture change** — (n)
reuses 1.2(k)'s ADR-0009-noted mount. (The alternative — a third mount for a
dedicated tools dir — is rejected in this design: it reopens the posture note for
no benefit, since `/pd-home` is already per-side, per-run, and dies with the run.)

Internal layout under the tools root is **unchanged**, so the tenants keep their
relative shapes:

```text
<pd-tools>/bin/{uv,poetry,pdm,bun,pip,python…}     (the tools venv's bin + bun)
<pd-tools>/node_modules/.bin/corepack             (npm --prefix install)
<pd-tools>/{uv,poetry,pdm}-requirements.txt       (bootstrap files)
<pd-tools>/pyvenv.cfg, lib/, include/             (it is still a venv)
```

### 3.2 What moves, what stays

**Moves:** everything in §1.1 (all three tenants; the tools venv as a unit), and —
subject to Q1 — the app `.venv`.
**Stays in the checkout:** `node_modules` (root and workspace-member) — it is the
**app's own** install product, produced by the app's frozen install, and app code
resolves it relative to the tree (`node_modules/.bin` on PATH is the app's own bin);
the generated lockfile (REG-A item 2 writes `{checkout}/<generated lock>` and the
executor reads it back into the evidence — it is a *record artifact*, not tool
state); the bootstrap requirements **sources** stay committed in the repo (only
their materialized copies move, with the tools venv they are written into).

### 3.3 Rendering migration

1. **One new placeholder.** Introduce `{pd_tools}` in `provisioning.py` templates,
   replacing every `{checkout}/.pd-tools` literal (the constants at `:764`,
   `:804-805`, `:1207`, `:1222`, `:1265`, `:1532-1572`, `:1679`, `:1752`,
   `:1779`, the corepack `--prefix` at `:2133`, and the persisted-form steps).
   Renderings: sandbox mapping `{pd_tools}` → `/pd-home/.pd-tools`; host mapping →
   `str(sandbox_home_dir(snap) / ".pd-tools")`. This keeps plan data
   executor-neutral, exactly as `{checkout}`/`{venv}` are today, and confines the
   string change to the two mapping tables plus the wrapper.
   (Alternative considered: keep the templates and special-case the replacement in
   `render_argv`. Rejected — a named placeholder is greppable, testable, and matches
   the house pattern; a string special-case is how path drift starts.)
2. **If Q1 moves the app venv:** `{venv}` sandbox rendering changes from
   `/checkout/.venv` to `/pd-home/.venv` (one line in `orchestrator.py`'s mapping),
   and the three literal `/checkout/.venv/bin/python` verify argv in
   `provisioning.py` become `{venv}`-templated (they should have been; today they
   hardcode the sandbox rendering in plan data — the host executor simply never
   runs the sandbox-only step, which is why the smell is invisible).
3. **Wrapper** (`_sandbox_step_command`): the two `.pd-tools` PATH prefixes and the
   two `.venv` forms take the new base; order unchanged (§2.2). Host PATH assembly
   (`runner.py:1576`, `:1588`) takes `sandbox_home_dir(snap)`-based paths instead of
   `snap/".pd-tools"`.
4. **`persisted_dirs`:** the values are checkout-relative today and become wrong as
   stated. Proposal: record them **rooted** — e.g. `["pd-home:.venv", "pd-home:.pd-tools"]`
   / `"pd-home:.pd-tools"` — or introduce a sibling field. This is a plan-record
   schema question (Q3): the field feeds `InstallPlan.sha256()`, so any choice is a
   clean break in plan hashes (already accepted, §2.5), but the *shape* should be
   settled once, in the work order.
5. **Creation:** the tools venv is still created by a plan step
   (`python -m venv {pd_tools}`) inside the first install container — which already
   has `/pd-home` mounted and HOME set; the corepack `npm install --prefix {pd_tools}`
   and the bun bootstrap script (`d={pd_tools}`) change by the placeholder only.
   On the host, the executors must `mkdir -p` the home sibling before the first
   bootstrap step (the sandbox path gets it free from `_common_args:989`).

### 3.4 Test surface

**Must change (string pins):** §2.1's list — wrapper assertions in
`test_wo7_pr2.py`, conformance goldens in `test_conformance.py`,
`persisted_dirs`/argv assertions in `test_provisioning.py`,
`test_provisioning_sandbox.py`, `test_wo81b.py`, plus the remaining `.pd-tools`
hits in `test_runner.py`, `test_node26_corepack.py`, `test_wo81*.py`,
`test_sandbox_addendum18.py` where they assert rendered paths (some assert plan
templates, which change only if the placeholder reaches the template text — it
does, §3.3.1, so template assertions change from `{checkout}/.pd-tools` to
`{pd_tools}` forms).
**Must NOT change (behavior pins):** the #113 plant properties (compound-command
PATH survival, builtin-leading commands), the 1.2(f) ordering assertions'
*relative* order, frozen-install argv beyond the tool paths, REG-A/REG-D evidence
shapes. The wrapper tests should be re-based, not rewritten: same cases, new prefix.
**Must be added:** (a) a pd-home-scan-shaped regression: a fixture app whose build
scans the **checkout root** and refuses on foreign entries — green only when the
tools live under `/pd-home`; (b) a cleanup assertion that the `.pd-home` sibling
(incl. the relocated tools) is gone after `cleanup_run`; (c) a plan test asserting
no step argv, env value, or wrapper rendering contains `/checkout/.pd-tools` or
`/checkout/.venv` for any adapter/manager combination (a "no in-tree tool state"
guard, enumerable over the plan matrix — this is the test that keeps (n) from
silently regressing when a future tenant is added).

### 3.5 Risks

1. **venv absolute paths.** A venv embeds its own absolute path (script shebangs,
   `pyvenv.cfg`). *Answer:* nothing is ever moved after creation — the creation
   path changes, and every step container of a side mounts `/pd-home` at the same
   path, so a venv created in step 1 is valid in step N and in the app container.
   The host executor likewise creates at the final host path. The failure mode to
   guard is a *mixed* rendering (one step at the old path, one at the new), which
   §3.4(c)'s guard test exists to catch.
2. **poetry's in-project adoption (if `.venv` moves).** The plan's mechanism is
   `POETRY_VIRTUALENVS_IN_PROJECT=true` + a pre-created in-project `.venv` that
   poetry adopts (`provisioning.py:1302`; `runner.py:1541ff`). An out-of-project
   venv is **not** adopted by that mechanism: the design would need
   `POETRY_VIRTUALENVS_IN_PROJECT=false` + `POETRY_VIRTUALENVS_PATH=/pd-home`
   (poetry then names the venv by project hash under that path — which breaks the
   plan's fixed `{venv}` addressing of the verify step and wrapper) or an explicit
   `poetry env use {venv}/bin/python` registration step. **Flagged open — Q2.**
   This is the single hardest interaction in (n).
3. **pdm's venv discovery (if `.venv` moves).** `pdm sync` locates the project
   venv in-project by default; an external venv needs pdm's venv configuration
   (`venv.in_project=false` / a `PDM_VENV`-style pointer, version-specific to the
   pinned pdm 2.29.2). Needs verification against the pinned pdm before the work
   order commits to it. **Flagged open — Q2.**
4. **uv (if `.venv` moves).** Clean: `UV_PROJECT_ENVIRONMENT={venv}` is already an
   explicit plan env, rendered through the mapping (`provisioning.py:1267`). No
   discovery assumption to break.
5. **Tools that expect state inside the checkout for resolution.** Reviewed per
   tenant: corepack shims resolve the *app's* `packageManager` pin from the cwd
   (unaffected); bun resolves the app's lockfile/modules from the cwd (the binary's
   own location is irrelevant); the Python managers receive every path explicitly
   in argv. `node_modules/.bin` stays (§3.2). **No tenant needs to live in the
   tree.** The residual case is an *app* whose own scripts hardcode `.venv/…` or
   call a manager expecting in-tree state — contracts name none (§1.5), and
   generic alignment says the app's declared commands run as declared; if an app
   declares `.venv/bin/python` in its contract start, that string is the app's
   own and (n) must not rewrite it — the wrapper's PATH prepend is what makes
   bare `python` resolve. **Flagged — Q5** (confirm no contract relies on the
   wrapper's `/checkout/.venv/bin/python` *rewrite* of a bare `python` command
   beyond PATH resolution: the rewrite at `orchestrator.py:300` substitutes
   the venv binary directly, so a moved venv changes that substitution's target
   — mechanically covered by §3.3.2, but the behavior contract should be stated).
6. **HOME-riding caches: already out, do not double-move.** bun's install cache,
   the corepack download cache (`COREPACK_HOME`), the npm cache, and the pnpm
   store already live under `/pd-home` via HOME/env pins (§1.4). (n) moves only
   the two in-tree trees; the design must not "helpfully" relocate caches that
   1.2(k) already placed.
7. **Disk/ownership.** The tools tree under `/pd-home` is covered by the existing
   ownership transfer (§1.4) and by containment cleanup (§2.4) — including PR
   #117's userns-aware removal, which becomes the load-bearing path for it.
   One check for the build: `_prepare_checkout`'s transfer is keyed on the
   checkout path and covers the home dir created from it; the host executor
   creates the sibling itself (§3.3.5) and runs uncontainerized, so no transfer
   question arises there.
8. **Plan-hash / evidence churn.** Accepted and bounded in §2.5: it lands *before*
   the 25-side re-run precisely so the re-run's baseline is post-(n) throughout.

---

## 4. Questions the reviewer's work order needs to settle

1. **Scope of "Python venvs":** does (n) move the **app** venv `.venv` as well as
   `.pd-tools`? (The queue wording — ".pd-tools + venv out of /checkout" — reads
   yes; §3 designs for yes, but Q2's cost rides on this answer. If the ruling is
   tools-only, (n) shrinks to the placeholder migration plus wrapper prefixes.)
2. **If the app venv moves:** the poetry and pdm mechanisms — accept
   `POETRY_VIRTUALENVS_PATH` + hash-named venv (and re-derive `{venv}` per plan
   from poetry's naming), an explicit `poetry env use` / pdm venv-pointer step,
   or keep poetry/pdm venvs in-project as a named exception? Each choice changes
   what `{venv}` means per manager; the work order should pick one and name the
   verification (a poetry and a pdm golden side in the re-run).
3. **`persisted_dirs` record shape** after relocation: rooted strings
   (`pd-home:.pd-tools`), a new field, or dropping the field (it has no runtime
   consumer)? It feeds `InstallPlan.sha256()` and conformance assertions either way.
4. **Placeholder name and reach:** `{pd_tools}` as proposed — and should the three
   hardcoded `/checkout/.venv/bin/python` verify argv in `provisioning.py` be
   templated as part of (n) (recommended: yes; they are sandbox renderings hiding
   in plan data)?
5. **The wrapper's bare-`python` rewrite** (`orchestrator.py:300`): confirm it
   is re-based to the new venv path as a pure rendering change, with no contract
   in the readiness set relying on the literal `/checkout/.venv/bin/python`
   string (grep says none do — the ruling should record that).
6. **Regression test shape:** adopt the §3.4(a) checkout-scanning fixture (the
   1.2(k) pd-home-scan pattern aimed at the tree) as (n)'s acceptance test, plus
   the §3.4(c) no-in-tree-tool-state guard over the plan matrix — or does the
   reviewer want a measured-side demonstration (a Vendure re-run showing the
   checkout tree clean) as the acceptance evidence instead of / in addition to
   the fixture?
7. **Sequencing within the re-run gate:** (n) lands before the 25-side re-run —
   confirmed — but does the reviewer want (n)'s own PR to carry a single-repo
   readiness dispatch (e.g. one bun side + one poetry side) as its evidence, in
   the D61 pack shape, before the full re-run is dispatched?

---

*Method note: every citation is from the tree at main `9f7f3ad`, read directly;
PR #117 citations are from its branch head `4ffeffc` in the cleanup worktree and
are marked as such where used. Absence claims (no runtime consumer of
`persisted_dirs`; no `.pd-tools` reference in `submodules.py`; no contract naming
`.venv`/`.pd-tools`) are grep results over `proofdeploy/`, `readiness/`, and
`tests/` at that head.*
