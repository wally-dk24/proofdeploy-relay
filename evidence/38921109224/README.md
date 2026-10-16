# D61 evidence pack — Ghost auth proof, D91 round (endpoint `/ghost/api/admin/tags/`)

- **Run ID:** 38921109224 (preceded by the fix-side rehearsal, run 38920266792, same configuration, also PROVEN)
- **Workflow:** `readiness.yml`, dispatched with `mode=auth-proof`, `repo=TryGhost/Ghost`, `attempt=1`, `--ref feat/ghost-fixture-auth`
- **PR:** wally-dk24/proofdeploy#121, head `69b63ccdff8e6d9301dc1fedd8d49af67d610ea5`
- **Artifact:** `readiness-TryGhost-Ghost-1` (id 12056000485), zip sha256 as downloaded: `021083699e586fb2edaca019122f065c3e096fb9f21de59efc47d68b67e4ea2b`
- **Contents:** `rows-authproof.json` (the run's rows, labelled `auth-proof` — evidence, never measured records) + `pd-logs/` for all three sides.
- **Predictions:** posted before the first dispatch of this configuration — https://github.com/wally-dk24/proofdeploy/pull/121#issuecomment-6218277423

## Result: PROVEN on all 3 sides

Endpoint per side: `GET /ghost/api/admin/tags/` (D91 — the D87 round's `/site/` read is `permissions: false`, public by design; `tags` browse is `permissions: true`).

| Side | Registered SHA | Status | Seed | Req 1 (auth) | Req 2 (auth) | Req 3 (no Authorization) | Mint count after two authenticated |
|---|---|---|---|---|---|---|---|
| `checkpoint-bug-ghost-1` fix | `f67640b2c18c76abd9cd3f6d19d6ae93327b42fa` | READY | key file present; `ghost/core/content/data/ghost-dev.db` (1,519,616 bytes) | 200 | 200 | 403 | 2 |
| `checkpoint-bug-ghost-1` fix^ | `93e0084723cdaf22d656bcd4811b87433e6b3875` | READY | key file present; `ghost/core/content/data/ghost-dev.db` (1,519,616 bytes) | 200 | 200 | 403 | 2 |
| `C5` C | `5d482965b7f5ed05ffef956d0eaed8b8a51af0ef` | READY | key file present; `ghost/core/content/data/ghost-dev.db` (1,499,136 bytes) | 200 | 200 | 403 | 2 |

Per side: the seed step ran inside the install container (its log names `ghost/core/content/data/ghost-dev.db`), wrote the Admin API key to `.pd-auth/admin_key` (presence recorded, never content), and the booted app serves from that same SQLite file — the snapshot holds exactly that one `.db` after boot. Each authenticated request minted one fresh `ghost_jwt` (the provider's own detail lines, counted host-side); the unauthenticated request carries no Authorization header and is refused with 403.

## Redactions

The harness scrubs every emitted string BY VALUE with the side's secret set (the seed-written Admin API key, the per-side bootstrap values, every minted JWT) plus a JWT-shape sweep, and records counts per category on each row. In this run every row's counts are **0 in all categories** (`admin_key` 0, `bootstrap_value` 0, `minted_jwt` 0, `jwt_shape` 0) — truthfully: no secret value appeared in any emitted string (executor detail lines carry no token values; Authorization headers are recorded by shape only, `Ghost <redacted:minted_jwt>`). Independent check on this pack: a JWT-shape grep (`eyJ…`) over the rows and all three logs returns 0 matches.
