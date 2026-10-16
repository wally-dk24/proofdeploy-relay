# Directus D98 contract fix — readiness predictions (recorded BEFORE first dispatch)

- Date: 2026-10-16
- Work order: D98 item 3 (CHECKPOINT BLOCKER, PRIORITY 1) — plan: blind checkpoint units C3/C4 + checkpoint-bug-directus-1
- PR: https://github.com/wally-dk24/proofdeploy/pull/122 — head `b7584c663a5ab74911e27540a20fb5fdbf731379` (branch `feat/directus-contract-d98` on main `3eab21ac139307e37f5025eb7938743eb3b1fa02`)
- Contract change (readiness/contracts/directus__directus.yml, one file):
  - build: "npm_config_workspace_concurrency=2 pnpm run build"
  - migrate: "cd directus && node cli.js bootstrap"
  - start: "cd directus && node cli.js start"
  - readiness `/server/ping`, env, auth unchanged; one contract serves all 4 sides.
- Prior state: all 4 sides BUILD_FAILED in re-run 38919702236 at the migrate step — `directus: command not found` (old build was `"true"`; the workspace was never built).

## Predictions (per side)

| unit | role | registered SHA | prediction |
|---|---|---|---|
| checkpoint-bug-directus-1 | fix^ | 7e9b03504c6094bac8dace713c11a6f909016057 | READY; GET /server/ping → 200 after start |
| checkpoint-bug-directus-1 | fix | 93a3f39342e171d3ae3017c2aa9a744b5e1c3f8a | READY; GET /server/ping → 200 after start |
| C3 | C | dc922b790a15949e8abfce9c1f4523d4e5f89492 | READY; GET /server/ping → 200 after start |
| C4 | C | a5da59da94bb33c8d644df93a21403eca4c1a4e7 | READY; GET /server/ping → 200 after start |

Expected total: READY 4/4.

## Mechanism

The frozen pnpm install (unchanged) lays down the workspace; the new build step actually compiles it (`pnpm run build` = upstream's `pnpm --recursive run build`, workspace concurrency 2 as in upstream's Dockerfile), so the directus package is built. `migrate` runs the package's own entry directly — `cd directus && node cli.js bootstrap` — which needs no `directus` bin shim on PATH (the old failure mode); bootstrap initializes/migrates the SQLite DB at /checkout/data.db and seeds the admin from the contract's bootstrap_env. `start` runs `node cli.js start` from the same package; the readiness probe GET /server/ping answers 200. This mirrors upstream's own image flow at all 4 SHAs (Dockerfile b900f41b, directus/cli.js 50b5ead7, pnpm@10.27.0).

## Stop rule (per D98)

Rehearse ONE side first; if the rehearsal shows a new blocker, stop and report it — no improvised fix.
