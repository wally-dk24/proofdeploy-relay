# D61 evidence pack — Ghost auth RE-PROOF with the generic per-leg driver

- Run: `38987053721` — workflow `readiness.yml`, `mode=auth-proof`,
  `repo=TryGhost/Ghost`, `attempt=1`; created 2026-10-17T00:35:13Z,
  completed **success** (00:45:46Z).
- Code head: `main` at
  `98e9c21dad1dcddeebf23d5351d0ec471f7f631b` (train 3 merge: PR
  #133 Vendure fixture + PR #134 generic per-leg auth-proof
  driver). The proof ran from main itself — no branch, no content
  changes. Driver spec in-tree (`readiness/auth_proof.py`
  PROOF_SPECS): `GET /ghost/api/admin/tags/`, unauthenticated
  status 403, acquisition kind `mint`, no body checks.
- Why a re-proof: Ghost's original proof (D96 era) ran under the
  old driver, whose bare leg still acquired via the provider's
  proactive ensure (the logged mint). Under the D137 per-leg
  rules — each authenticated leg exactly 1 acquisition of the
  registered kind; the bare leg 0 acquisitions AND no
  Authorization header, through a credential-less executor copy —
  Directus (run `38976234757`) and Vendure (run `38978137844`)
  were already proven; this run shows Ghost under the same rules.
- Predictions: `evidence/d137-auth-proofs/predictions-ghost-reproof.md`
  (relay commit `00ae596767c402fe9d20e9190f325ea1a6203c74`,
  committed 2026-10-17T00:35:01Z, before this dispatch at
  00:35:13Z).
- Artifact: `readiness-TryGhost-Ghost-1` (id `12084040957`);
  zip sha256 as downloaded:
  `8facea6737da7efa0b5707a78a78743bbda2bfabb3129b285d3f72c92b8f8ad8`.

## Result — PROVEN 3/3, matching the predictions

| side | registered SHA | status | leg 1 | leg 2 | leg 3 |
|---|---|---|---|---|---|
| C5 (role C) | `5d482965` | READY | 200, `{"mint": 1}`, header | 200, `{"mint": 1}`, header | 403, 0 acquisitions, no Authorization header |
| checkpoint-bug-ghost-1 fix^ | `93e00847` | READY | 200, `{"mint": 1}`, header | 200, `{"mint": 1}`, header | 403, 0 acquisitions, no Authorization header |
| checkpoint-bug-ghost-1 fix | `f67640b2` | READY | 200, `{"mint": 1}`, header | 200, `{"mint": 1}`, header | 403, 0 acquisitions, no Authorization header |

Fixture render on every row:
`ab955a3081fc9d293d1b84fd257299069c0340c59037a5a8aa09f6ef11afe7f0`
(= the Master-signed render, D117). Leg-3 body on every side is
Ghost's refusal: `{"errors": [{"message": "Authorization failed",
…}]}`. Network mode `none+socket` on all rows.

The fixed `_proof_passed` from the same main checkout, run
offline over the recorded rows (seed conjunct
`key_file_present = true` on all sides), returns **True on all
3 sides**, agreeing with the harness's recorded verdicts.

One wording note on the predictions: they said "Bearer header";
the recorded Authorization shape is `Ghost
<redacted:acquired_token>` — Ghost's Admin API scheme, dictated
by the committed contract's token presentation (the generic
driver takes presentation from the contract). The header was
sent with the acquired token on legs 1–2 and absent on leg 3,
exactly as predicted in substance.

## Contents

- `rows-authproof.json` — the artifact's rows file, as
  downloaded, unmodified (sha256
  `731f7aae00e5c37844c5b379e250a7db14fcae629574f507b825262d808edbfd`).
- `pd-logs/` — no side failed, so per D61 this pack carries one
  READY side's log as reference:
  `authproof-C5-clean-a1.log` (28,822 bytes, in full, no
  trimming). All files well under 1 MB.

## Redaction

The harness scrubs by value before upload; Authorization values
are recorded as shape only (`Ghost <redacted:acquired_token>`).
Per-row `redactions` counts in the rows file: `key_file: 0`,
`bootstrap_value: 0`, `acquired_token: 0`, `jwt_shape: 0` on all
3 rows. Nothing was redacted by hand in this pack. Grep for the
JWT prefix `eyJ` over the rows file: **0 matches**.
