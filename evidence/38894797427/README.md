# D61 evidence pack — Vendure readiness, plan 1.2(n) tool-state relocation

- **Run ID:** 38894797427
- **Workflow:** readiness (`.github/workflows/readiness.yml`), dispatched from PR #118's branch `feat/tool-state-relocation`
- **Head SHA:** ff01c008652d835cd04f1de5dcd4f72134f96b46 (the branch's product head; the one later commit, 3fd5383, touches only `tests/test_sandbox.py` — `git diff ff01c00..3fd5383 -- proofdeploy/` is empty)
- **Inputs:** repo=vendure-ecommerce/vendure, attempt=1
- **Artifact:** `readiness-vendure-ecommerce-vendure-1` (id 12044509327)
- **Artifact zip sha256 (as downloaded):** `6e0c0fe7cb96dd78b244eb1c132e212c2711887cdd0fca780736fb3122443d76`
- **Run conclusion:** success

## Per-side status (rows-attempt1.json)

All six sides READY — no failing sides in this run:

| unit | role | status |
|---|---|---|
| C1 | C | READY |
| C2 | C | READY |
| checkpoint-bug-vendure-1 | fix^ | READY |
| checkpoint-bug-vendure-1 | fix | READY |
| checkpoint-bug-vendure-2 | fix^ | READY |
| checkpoint-bug-vendure-2 | fix | READY |

Per D61, the pack carries the rows file and one READY side's pd-log as reference (`pd-logs/readiness-checkpoint-bug-vendure-1-fix-a1.log`); there are no failing sides to carry.

## The plan 1.2(n) evidence

Vendure is a bun app, so its provisioning exercises the relocated tool state end to end. From the reference pd-log (vendure-1 fix side, this run):

- The side's two mounts are separate: the checkout at `/checkout`, the per-side home at `/pd-home` (`-v …/snapshot-fix:/checkout … -v …/snapshot-fix.pd-home:/pd-home`).
- The bun bootstrap step renders into the home: `bash -c 'set -e; d=/pd-home/.pd-tools; mkdir -p $d/bin $d/.bun-dist; …` (pre-1.2(n) this same line read `d=/checkout/.pd-tools` — see evidence/38839169105).
- The frozen install runs the home's bun: `/pd-home/.pd-tools/bin/bun install --frozen-lockfile`.
- Contract steps run with the home's tools first on PATH: `export PATH=/pd-home/.pd-tools/bin:/checkout/node_modules/.bin:$PATH; …`
- Grep over all six pd-logs of this run: **zero** occurrences of `/checkout/.pd-tools` or `/checkout/.venv`.

On the checkout-root listing: the readiness flow has no step that lists the checkout root, so no pd-log contains one. The literal listing evidence for 1.2(n) is the Q6(a) checkout-scan fixture (`tests/fixtures/checkout-scan/` on PR #118), whose build step prints the checkout root and refuses if `.pd-tools` or `.venv` appears in it. Its sandbox regression (`test_sandbox_checkout_root_has_no_tool_state`) ran in PR #118's sandbox-tests run 38894777024 (core-sandbox shard, 75.72s) and passed: READY, the build log carried the root listing and `scan: checkout root clean`, and the bootstrapped bun was asserted present at `/pd-home/.pd-tools/bin/bun`. Run locally against the fixture, the scan prints:

```text
checkout root: bun.lock package.json proofdeploy.yml scan-checkout.js scan-dep-0.1.0.tgz server.js
scan: checkout root clean (no .pd-tools, no .venv)
```

and with a `.pd-tools` directory planted in the root it exits 1 (`scan: .pd-tools found in the checkout root`).

## Scrub (D61)

Scanned all six pd-logs and the rows file for credential material (passwords, tokens, API keys, authorization headers, per-run secrets). **Zero redactions required:** the only matches are Vendure's own startup warning that a *default* superadmin password is configured, which names public upstream environment-variable names and contains no credential value. No files were trimmed (largest file 56 KB, well under the 1 MB cap).
