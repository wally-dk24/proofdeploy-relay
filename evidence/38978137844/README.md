# D61 evidence pack — Vendure auth proof, full 6 sides (fixed D137 driver)

- Run: `38978137844` — workflow `readiness.yml`, `mode=auth-proof`,
  `repo=vendure-ecommerce/vendure`, `attempt=1` (all sides);
  created 2026-10-16T22:50:47Z, completed **success**.
- Code head: combined branch `proof/d137-vendure-combined` at
  `6ce7e1cbc34d9c00b100bea25cccb5faa9e17d13` = PR #134 FIXED head
  `733cbdb6cabf9acbbed2b042d8bc2a4bf298259e` + PR #133 fixture head
  `7a1cc7d3e301342046bfaf9f22d45b40ec8d6164` — the fixture head this
  proof ran with is **PR #133 at `7a1cc7d`** (both
  `git merge-base --is-ancestor` checks exit 0). The Vendure
  contract is not in main; the combined branch carries it from
  PR #133. Harness-recomputed fixture render on every side:
  `ca9e5e07e310337aee0ff1822efdcfb2f68a02f68ad3237b5a83144cbd5618ae`.
  Loaded login query in that tree: plain double quotes, no
  backslash. (The earlier run `38967407182`, built on the
  superseded fixture head `4e00385`, and run `38970581452`, built
  on the unfixed driver, were both cancelled unused; no result
  from either is used anywhere.)
- Predictions: `evidence/d137-auth-proofs/predictions-fixed-head.md`
  (relay commit `40985ec511aee10b3b6280554382144649a061f1`, committed
  2026-10-16T22:31:05Z, before the first dispatch of this
  configuration at 22:31:12Z). Preceded by the one-side rehearsal
  (run `38976247158`, pack `evidence/38976247158/`), which
  confirmed the bare-leg expectation from live evidence.
- Artifact: `readiness-vendure-ecommerce-vendure-1` (id
  `12081391445`); zip sha256 as downloaded:
  `437fedd86ef73c678ede3e16fee9bbb64c997ea81aba002c64d57e26dd5eb625`.

## Result — PROVEN 6/6, matching the predictions exactly

Every side: status READY, verdict PROVEN; leg 1 `POST /admin-api`
→ 200 with acquisitions `{"login": 1}`, a Bearer Authorization
header, and body `data.me.id` = `"1"` (non-null); leg 2 the same
with its own fresh login; leg 3 → **status 200, 0 acquisitions,
no Authorization header**, body
`errors[0].extensions.code == "FORBIDDEN"` with `data.me: null`.

| side | registered SHA |
|---|---|
| C1 (role C) | `4cbbc790118786a17721954c3a4d739810bd2632` |
| C2 (role C) | `b19ab4dc76e4f8521e02258b8afa26f615142595` |
| checkpoint-bug-vendure-1 fix^ | `859619c4c289f7773619704f9cd1e5c1256dcfce` |
| checkpoint-bug-vendure-1 fix | `4393199c077e5cd9399696d25b69c620b36c5cc2` |
| checkpoint-bug-vendure-2 fix^ | `8fb22a8d882bb8b8cb2828c19f91e7c7d58f8aff` |
| checkpoint-bug-vendure-2 fix | `02c9d81e83bebebe8b3384b2f789800b14ed0146` |

## Contents

- `rows-authproof.json` — the artifact's rows file, as downloaded,
  unmodified. Each request record carries the scrubbed response
  body the fixed driver's generic body checks evaluated.
- `pd-logs/` — all 6 side logs (no failing sides; included in
  full, each well under 1 MB, no trimming).

## Redaction

Per-row `redactions` counts are 0 in all categories on all 6
rows; acquired session tokens appear only as
`<redacted:acquired_token>` (header shape). Independent check
over the rows file as downloaded: grep for the JWT prefix `eyJ`
returns 0 matches.
