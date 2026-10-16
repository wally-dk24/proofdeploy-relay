# 25-side readiness re-run — PREDICTIONS (posted BEFORE the first dispatch)

- Run: plan 1.2 done-when + 1.6 readiness re-run (D90 work order; Addendum 1 H5, Addendum 2 J1–J6; D33, D48, D50; D61).
- Dispatched from: proofdeploy main `a21b2897e400879d1ed4f81f7e9847f39742dbcd` (after the Step-0 gate: post-merge CI on that SHA green).
- Scope: EVERY unit/side in `readiness/manifest.json` at that SHA — verified count: **27 units, 42 sides, 10 repos** (nestjs/nest 12, vendure-ecommerce/vendure 6, fastify/fastify 5, directus/directus 4, TryGhost/Ghost 3, django/django 3, honojs/hono 3, bottlepy/bottle 2, codeigniter4/CodeIgniter4 2, LMCache/LMCache 2). The "25-side" name is the Addendum-1 subset: nest 12 + directus 4 + vendure 6 + Ghost 3 = 25.
- Method: `.github/workflows/readiness.yml`, one dispatch per repo, attempt=2 (attempt 1 for every side; attempt 2 re-runs only non-READY sides; typed not-supported sides are never retried by the harness). No model, no probes, no scoring.
- Predictions written: 2026-10-16, before dispatch 1 of this re-run.

## Per-side predictions

### nestjs/nest (contract: frozen npm ci --legacy-peer-deps + REG-D sample build)
Registration evidence: H1 both sides + all 4 held-out cleans have committed lockfiles out of sync with their manifests (reviewer finding 2026-10-15, Addendum 2 J2); H2/H3/H4 in sync.
- H1 fix^ `1c4d9c42` — NOT READY: `not supported: lockfile out of sync with its manifest` (D33/J2 class)
- H1 fix `02ee6d44` — NOT READY: same typed drift class
- H2 fix^ `a0b14b58` — READY
- H2 fix `1d1dbf7b` — READY
- H3 fix^ `40d07dc6` — READY
- H3 fix `21648746` — READY
- H4 fix^ `697ff89e` — READY
- H4 fix `1685a435` — READY
- heldout-clean-1 `a9bf20b6` — NOT READY: typed drift class
- heldout-clean-2 `abce26ec` — NOT READY: typed drift class
- heldout-clean-3 `45485b54` — NOT READY: typed drift class
- heldout-clean-4 `c921a60e` — NOT READY: typed drift class

### vendure-ecommerce/vendure (D64 contract)
Evidence: READY 6/6 at attempt 1 under D64 (run 38839169105) and again 6/6 on the post-1.2(n) code (run 38894797427, PR #118).
- C1 `4cbbc790` — READY
- C2 `b19ab4dc` — READY
- checkpoint-bug-vendure-1 fix^ `859619c4` — READY
- checkpoint-bug-vendure-1 fix `4393199c` — READY
- checkpoint-bug-vendure-2 fix^ `8fb22a8d` — READY
- checkpoint-bug-vendure-2 fix `02c9d81e` — READY

### TryGhost/Ghost (D71+D83 contract)
Evidence: READY 3/3 at attempt 1 (run 38907953857).
- checkpoint-bug-ghost-1 fix^ `93e00847` — READY
- checkpoint-bug-ghost-1 fix `f67640b2` — READY
- C5 `5d482965` — READY

### directus/directus
Evidence: the linkBin EPERM class was fixed by plan 1.2(g) checkout ownership (#100); on-record expectation is Directus passes linkBin. No READY proof exists yet in the D61 packs; the bootstrap/start path is the residual risk (a failure would be START_FAILED, our-gap class, not not-supported).
- C3 `dc922b79` — READY
- C4 `a5da59da` — READY
- checkpoint-bug-directus-1 fix^ `7e9b0350` — READY
- checkpoint-bug-directus-1 fix `93a3f393` — READY

### honojs/hono (current contract, pre-D76 fix)
Evidence: every prior measurement under this contract: frozen bun install succeeds, build dies `env: 'pnpm': No such file or directory` (exit 127) — the adapter installs with bun; pnpm is absent; start is also wrong (library, no start). Class: our gap (contract shape); the D76 fix is approved but not yet built.
- T7 fix^ `f5a53463` — NOT READY: BUILD_FAILED (pnpm exit 127 class)
- T7 fix `00ee8757` — NOT READY: BUILD_FAILED (pnpm exit 127 class)
- train-C2 `1f707c56` — NOT READY: BUILD_FAILED (pnpm exit 127 class)

### bottlepy/bottle
Evidence: READY proven repeatedly (runs 38624583663, 38850692704).
- T6 fix^ `2a743a30` — READY
- T6 fix `b73bd1db` — READY

### django/django
No prior readiness rows in the D61 packs; prediction from contract shape (pip install of the framework + startproject + runserver), stated as such.
- T4 fix^ `7ff7fcc0` — READY (contract-shape prediction)
- T4 fix `ebcb13b3` — READY (contract-shape prediction)
- train-C1 `da6567da` — READY (contract-shape prediction)

### fastify/fastify
Train units measured in the dev-set era; no readiness rows in the D61 packs. Prediction from contract shape (npm ci + build:validation + examples/simple.js), with the named risk that the example's port handling decides start.
- T2 fix^ `dd2ba36f` — READY (contract-shape prediction)
- T2 fix `179619c8` — READY (contract-shape prediction)
- T3 fix^ `7de6e816` — READY (contract-shape prediction)
- T3 fix `93c239a3` — READY (contract-shape prediction)
- train-C3 `f376f608` — READY (contract-shape prediction)

### codeigniter4/CodeIgniter4
No READY proof for any PHP side exists in the recorded evidence; the framework's composer platform requirements are the known risk. Prediction from contract shape only, low confidence.
- T1 fix^ `ecbf0446` — NOT READY: BUILD_FAILED (expected class harness-or-environment or our gap; the decisive log line will tell)
- T1 fix `f5e463b9` — NOT READY: BUILD_FAILED (same caveat)

### LMCache/LMCache
Evidence: plan 1.2(d) LMCache support is an unbuilt gap line; no READY proof exists; the contract's `pip install -e .` pulls the full torch-class dependency set.
- T5 fix^ `3884c7f1` — NOT READY (expected class: our gap, plan 1.2(d))
- T5 fix `449b94c1` — NOT READY (expected class: our gap, plan 1.2(d))

## Predicted totals
READY 29/42; NOT READY 13/42 (nest drift 6, hono 3, CodeIgniter4 2, LMCache 2).
