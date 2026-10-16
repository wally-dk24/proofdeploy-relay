# D61 evidence pack — Vendure auth proof REHEARSAL (one side, fixed D137 driver)

- Run: `38976247158` — workflow `readiness.yml`, `mode=auth-proof`,
  `repo=vendure-ecommerce/vendure`, `attempt=1`,
  `only=checkpoint-bug-vendure-1:fix`; created 2026-10-16T22:31:19Z,
  completed **success**.
- Code head: combined branch `proof/d137-vendure-combined` at
  `6ce7e1cbc34d9c00b100bea25cccb5faa9e17d13` = PR #134 fixed head
  `733cbdb6cabf9acbbed2b042d8bc2a4bf298259e` + PR #133 fixture head
  `7a1cc7d3e301342046bfaf9f22d45b40ec8d6164` (both
  `git merge-base --is-ancestor` checks exit 0). The Vendure
  contract is NOT in main; the combined branch carries it from
  PR #133. Harness-recomputed fixture render:
  `ca9e5e07e310337aee0ff1822efdcfb2f68a02f68ad3237b5a83144cbd5618ae`.
  Loaded login query in that tree contains plain double quotes,
  no backslash.
- Purpose: rehearse ONE side before the full 6-side run, to set
  the Vendure bare-leg expectation FROM EVIDENCE (reviewer item 2).
- Predictions: `evidence/d137-auth-proofs/predictions-fixed-head.md`
  (relay commit `40985ec511aee10b3b6280554382144649a061f1`, committed
  2026-10-16T22:31:05Z, before this dispatch).
- Artifact: `readiness-vendure-ecommerce-vendure-1` (id
  `12079287782`); zip sha256 as downloaded:
  `fb165b5bd3e621f0a382535baa722daa71df73be0994941295fd1e669975835b`.

## Result — side PROVEN, matching the prediction

Side checkpoint-bug-vendure-1 / fix (`4393199c077e5cd9399696d25b69c620b36c5cc2`),
status READY:

- Leg 1: **200**, acquisitions **1, kinds `{"login": 1}`**,
  Authorization `Bearer <redacted:acquired_token>`, body
  `{"data": {"me": {"id": "1", "identifier": "superadmin"}}}`
  (`data.me.id` non-null).
- Leg 2: **200**, acquisitions **1, kinds `{"login": 1}`**,
  Bearer header, same body shape (its own fresh login).
- Leg 3: **status 200**, acquisitions **0**, kinds `{}`,
  **no Authorization header sent** (`authorization: null`), body
  `{"errors": [{"message": "You are not currently authorized to perform this action", "locations": [{"line": 1, "column": 9}], "path": ["me"], "extensions": {"code": "FORBIDDEN"}}], "data": {"me": null}}`.

Leg 3's actual status and body confirm the registered expectation
(HTTP 200 + `errors[0].extensions.code == "FORBIDDEN"`, per the
reviewer's source check at control C1 `4cbbc790`) from live
evidence; no spec change was needed before the full run.

## Contents

- `rows-authproof.json` — the artifact's rows file, as downloaded,
  unmodified.
- `pd-logs/` — the rehearsed side's log, in full (no failing
  sides; well under 1 MB, no trimming).

## Redaction

Per-row `redactions` counts are 0 in all categories; the acquired
session token appears only as `<redacted:acquired_token>` (its
header shape). Independent check over the rows file as
downloaded: grep for the JWT prefix `eyJ` returns 0 matches.
