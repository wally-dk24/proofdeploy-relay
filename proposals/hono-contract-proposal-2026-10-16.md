# Hono registered contract — build/start proposal (REPORT ONLY)

**Date:** 2026-10-16 · **Prepared by:** Wally, for the reviewer's ruling
**Scope:** proposal only. No contract file, code file, or run has been changed or dispatched.
**Question it answers:** hono's registered contract cannot provision any of its sides — its `build` names a package manager the app does not declare, and its `start` is not a server command at all. Per the Vendure precedent (D64), the contract's commands must be the app's own, proven from the app's files at the registered SHAs.

All upstream citations below are from files fetched at the registered SHAs themselves (raw.githubusercontent / GitHub contents API, `ref=<sha>`), byte-compared across SHAs (sha256). Scratch copies: `~/workspace/readiness-scratch/` (prior run), `/tmp/hono/<sha8>/` (fetched files), `~/workspace/hono-proposal-scratch/fix/` (dress-rehearsal checkout).

---

## 1. The registered contract today (proofdeploy main `9f7f3ad`)

`readiness/contracts/honojs__hono.yml`, in full:

```yaml
build: "pnpm run build"
start: "npm create hono@latest"
readiness: "/"
```

Three fields, no `app:` block, no `install:` block (install is the adapter's, keyed off the lockfile — see §3).

## 2. Registered SHAs — verification

Checked against `readiness/manifest.json` on main `9f7f3ad`. **Three registered sides**, not one — the C2 clean on record (`1f707c56`) plus both sides of train-bug unit T7:

| Unit | Role | SHA |
|---|---|---|
| T7 (train-bug) | fix^ | `f5a53463ad2ff791fc02418b645750ce75a260ad` |
| T7 (train-bug) | fix | `00ee875728e2369b98a9ab7dda0690b77afedcff` |
| train-C2 (train-clean) | C | `1f707c56378a4497bc0a6a44fef5f5a8517fdf6b` |

## 3. The defects

### 3a. `build` — the 1.2(f) mechanism

Under plan 1.2(f) (manager-on-PATH, DONE #101), the sandbox puts on PATH only the tooling the app itself declares: the adapter reads hono's lockfile, selects bun, downloads the pinned bun 1.2.20 into `/checkout/.pd-tools/bin`, and renders the frozen install from it. Hono declares bun in every way a repo can: root lockfile is `bun.lock` — the **only** lockfile at the root at all three SHAs (no `pnpm-lock.yaml`, no `package-lock.json`, no `yarn.lock`; contents API at each SHA) — and `package.json:702` carries `"packageManager": "bun@1.2.20"` at all three SHAs. There is no pnpm anywhere in the app's own declarations, so pnpm is correctly absent from the sandbox PATH, and the contract's `pnpm run build` dies before any of the app's code runs. This is the contract naming the wrong manager, not an app defect and not a 1.2(f) defect: 1.2(f) is doing exactly what it was built to do.

The failure is already measured, not hypothetical. In the post-pin readiness run (artifact `readiness-honojs-hono-2`, rows + pd-logs at `~/workspace/readiness-scratch/hono-after-pin/`), all three registered sides came back `BUILD_FAILED` with the identical typed reason. The T7-fix log shows the mechanism end to end: the sandbox installs bun 1.2.20, runs `/checkout/.pd-tools/bin/bun install --frozen-lockfile` **successfully**, then renders the contract's build step (log line 48) —

```
$ podman … bash -c env PATH=/checkout/.pd-tools/bin:/checkout/node_modules/.bin:$PATH pnpm run build
env: 'pnpm': No such file or directory
install step 2 (build) failed: exit 127
```

— and the row records `package_manager: "bun"`, `status: "BUILD_FAILED"`, `typed_reason: "sandbox install failed: env: 'pnpm': No such file or directory"` for fix^, fix, and C alike.

### 3b. `start` — a scaffolding command where a server belongs

`npm create hono@latest` is the create-hono project scaffolder — the command a developer runs once, interactively, to generate a *new* project. It is wrong as a readiness `start` in four independent ways: it prompts (project name, template) on a non-interactive harness; it fetches templates and packages from the network, which is cut after install; it never binds `$PORT` and never serves anything, so the `readiness: "/"` probe has nothing to probe; and it is not a command this repo ever runs for itself — hono's `package.json` scripts block contains **no `start`, `dev`, or `serve` entry at all** at any registered SHA (the block runs from `test` at `package.json:14` through `remove-dist`; the only `"dev"` string in the file is an unrelated config array at `:635`). The repo has no server entrypoint because hono is a library, which is what §5 addresses.

## 4. What upstream itself does — install and build

Upstream's own CI (`.github/workflows/ci.yml`, byte-identical at all three SHAs, sha256 `760b7873…`) is bun end to end. The Main job: setup-bun (`:44–46`, version from `.tool-versions`), then —

- **install:** `bun install --frozen-lockfile` (`ci.yml:48`) — exactly what the adapter already renders (§3a's log);
- **build:** `bun run build` (`ci.yml:52`);
- **test:** `bun run test` (`ci.yml:53`).

The same install+build pair repeats in the fastly, node, workerd, lambda, and lambda-edge jobs. `bun run build` resolves to the app's own script, `package.json:30`, identical at all three SHAs:

```json
"build": "bun run --shell bun remove-dist && bun ./build/build.ts && bun run copy:package.cjs.json"
```

with `"postbuild": "publint"` at `package.json:31` (bun runs it as part of `bun run build` — confirmed in the §7 rehearsal). One wrinkle for the record: upstream's two bun-version sources disagree by one patch — `.tool-versions` says bun **1.2.19**, `packageManager` says bun@**1.2.20**. The sandbox pin (plan 1.2(e), #105) is 1.2.20, i.e. it follows `packageManager`; nothing in this proposal turns on the difference (see open question 4).

## 5. What `start` means for a library — the house shapes, and upstream's own template

Hono is a web framework library: there is no app in the repo to start. The registered contracts already have two shapes for this, and hono's own repo supplies the model for a third element:

- **Bottle** (library, Python): `build: "pip install ."`, and `start` synthesizes a minimal app inline — `python -c "…from bottle import route, run…run(host='localhost', port=int(os.environ['PORT']))"` — probing a route the inline app defines (`readiness: "/hello/world"`).
- **Fastify** (framework, Node): `start: "node examples/simple.js"` — an in-repo example file. Hono has no `examples/` directory at any registered SHA (root listing: `.github .vscode benchmarks build docs perf-measures runtime-tests src`), so this shape is unavailable as-is.
- **Django** (framework): scaffolds a project in `build` (`django-admin startproject …`) and starts the scaffold. Heavier than hono needs.

The decisive find: **upstream's own benchmark harness already runs a hono app from this repo, from source, under bun.** `benchmarks/http-server/benchmark.ts` (byte-identical at all three SHAs, sha256 `c7ad6b58…`) embeds its test-app template at `:37–52` — `import { Hono } from './src/index.ts'` (`:37`), `app.get('/', (c) => c.text('Hi'))` (`:43`), `export default app` (`:52`) — and serves it by spawning `bun <appPath>` (`:130`, `:195`). Its own endpoint validation asserts `GET /` returns exactly `Hi` (`:137–140`). The proposed `start` is that template, adapted only where the harness differs from upstream's laptop: the port comes from the sandbox-assigned `$PORT` env (read via `process.env.PORT`, the bottle shape) instead of Bun's default 3000, and it is delivered inline (the bottle shape) instead of as a file, because the contract has no file-writing step.

## 6. Proposed contract (exact strings)

```yaml
build: "bun run build"
start: "bun -e \"import { Hono } from './src/index.ts'; const app = new Hono(); app.get('/', (c) => c.text('Hi')); Bun.serve({ port: Number(process.env.PORT), fetch: app.fetch });\""
readiness: "/"
```

Provenance, element by element:

| Element | Source at the registered SHAs |
|---|---|
| `bun run build` | `package.json:30` (`scripts.build`), invoked identically by upstream CI at `ci.yml:52` (and in five sibling jobs) |
| `bun` as the runtime/manager | `bun.lock` (sole root lockfile, all 3 SHAs); `package.json:702` `"packageManager": "bun@1.2.20"`; sandbox pin 1.2.20 already in place (#105) |
| `import { Hono } from './src/index.ts'` | upstream's own app template, `benchmarks/http-server/benchmark.ts:37`; `src/index.ts` exists at all 3 SHAs (1,104 bytes each) |
| `app.get('/', (c) => c.text('Hi'))` | `benchmark.ts:43`, verbatim; upstream asserts this exact response at `benchmark.ts:137–140` |
| serving under `bun` | upstream's harness spawns `bun <app file>` (`benchmark.ts:130`, `:195`); `Bun.serve({ port, fetch: app.fetch })` is the explicit-port form of the same mechanism |
| port from `process.env.PORT` | the sandbox assigns `PORT` to the app container (every app contract relies on it — Ghost `$PORT`, Vendure `PORT` env); bottle's start reads `os.environ['PORT']` the same way |
| `readiness: "/"` | unchanged; it is the route the probe app — and upstream's own template — answers |

Install needs no contract entry, as today: the adapter's frozen `bun install --frozen-lockfile` (upstream `ci.yml:48`) already succeeds on all three sides (§3a).

## 7. Dress rehearsal (local, pinned bun 1.2.20, T7 fix SHA)

At a clean checkout of `00ee875728e2369b98a9ab7dda0690b77afedcff` (`~/workspace/hono-proposal-scratch/fix/`), with the exact pinned binary (bun 1.2.20, the release asset the sandbox itself downloads):

- `bun install --frozen-lockfile` → 781 packages installed, success;
- `bun run build` → **exit 0**, including the `postbuild` publint step (warnings only — one `pkg.exports` types-conditioning warning);
- the proposed `start` verbatim → `GET /` returned **HTTP 200, body `Hi`**, on the `$PORT` given in env; `GET /nope` returned 404, i.e. the probe app answers only its registered route.

The `bun -e` + top-level TS import + `Bun.serve` mechanics were additionally verified against a stub app under both the pinned 1.2.20 and this VM's bun 1.4.2 (200/`Hi` at `/` in both). Caveat, stated plainly: this rehearsal ran on this VM with network access and no sandbox; it proves the commands against the app's real files, not the sandboxed run itself — that proof is the re-run's, after the ruling.

## 8. Per-side uniformity — one contract genuinely fits all three sides

- **T7 fix^ vs fix: `package.json` is byte-identical** (sha256 `cafef790…` both), and their `bun.lock` files are identical (harness-recorded sha256 `c83c3031…` for both). The fix commit does not touch any file this proposal cites.
- **C2 (`1f707c56`, hono 4.12.34) vs T7 (4.13.8):** `package.json` differs in exactly two lines — `version` (`:3`) and one devDependency pin (`editorconfig-checker` 6.1.1 vs 6.2.0, `:685`) — neither bears on install, build, or the probe. `bun.lock` differs (harness sha256 `910eec46…`), as expected across release lines; it is still `bun.lock`, still frozen-installable by the same adapter path (the after-pin run installed it successfully).
- **Identical at all three SHAs:** `.github/workflows/ci.yml`, `.github/workflows/release.yml`, `benchmarks/http-server/benchmark.ts`, `.tool-versions`, `src/index.ts` size, the sole-lockfile layout, `packageManager`, and every script this proposal cites (`build` at `package.json:30` on all three, since the C2 diff touches only lines 3 and 685).

## 9. Predictions (to be scored against the re-run, if adopted)

**READY — all three sides.** Each side currently fails only at the contract's build step, on a command naming a manager the app never declared; install already passes everywhere, and the proposed build and start are upstream's own commands and upstream's own template app.

| Side | Prediction | Basis |
|---|---|---|
| T7 fix `00ee8757` | **READY** | §7 rehearsal ran on this exact SHA: frozen install ✓, `bun run build` exit 0 incl. publint, probe 200/`Hi` at `/` |
| T7 fix^ `f5a53463` | **READY** | `package.json` + `bun.lock` byte-identical to fix (§8); the rehearsal's result carries over mechanically |
| train-C2 `1f707c56` | **READY** | scripts and CI byte-identical to T7 (§8); differences are a version string and an editorconfig-checker pin; its frozen install already succeeded in the after-pin run |

Residual risks, named: (i) **quoting** — the `start` string nests a double-quoted YAML scalar around a `bun -e` argument; bottle's contract already carries exactly this nesting (`python -c \"…\"`) and renders correctly through the 1.2(m) wrapper, but the implementing change should be verified against the *rendered* command, not the YAML text. (ii) **`postbuild` publint** runs inside `bun run build`; at the fix SHA it warns and passes. If a future registered side ever makes publint fail, the side goes `BUILD_FAILED` — that is upstream's own gate surfacing, correctly typed, not a contract problem. (iii) The probe imports `src/index.ts`; if a future side moves the source entry, start fails loudly (module not found) rather than silently serving the wrong thing.

## 10. Open questions for the reviewer's ruling

1. **Probe import: source or built package?** Primary proposal imports `./src/index.ts` (upstream's template, §5). The alternative imports the built package — `import { Hono } from 'hono'` (self-reference via `package.json` exports → `dist/`) — the consumer shape: it exercises the published entry map and couples `start` to `build`'s output, at the cost of diverging from upstream's own in-repo template. Reviewer's call.
2. **`app.path`.** Ghost/Vendure/Nest now declare `app: path: "."` under the 2026-10-15 app-identification ruling; bottle/fastify (and hono today) don't. The proposed commands are root-relative either way. Declare `"."` for hono too, or leave the contract minimal?
3. **`readiness: "/"` unchanged?** The probe app answers only `/` (404 elsewhere — rehearsal-verified). Keeping `/` matches the current contract and upstream's own endpoint assertion; no change proposed, confirmation requested.
4. **Bun version source.** `.tool-versions` (1.2.19, upstream CI's input) vs `packageManager` (1.2.20, the sandbox's #105 pin). No contract text turns on it; noted so the record shows the discrepancy was seen, not missed.

---

*Method note: every upstream file cited was fetched at the registered SHA on 2026-10-16 (raw.githubusercontent / contents API) and sha256-compared across SHAs; lockfile presence/absence claims are root-listing results at those SHAs; the failure evidence is quoted from the post-pin run's own rows and pd-logs (`~/workspace/readiness-scratch/hono-after-pin/`); the rehearsal used the pinned bun-v1.2.20 release binary against a clean checkout of the T7 fix SHA. Nothing was edited, dispatched, or opened as a PR.*
