# D61 evidence pack — Directus auth proof RE-RUN with the fixed D137 driver

- Run: `38976234757` — workflow `readiness.yml`, `mode=auth-proof`,
  `repo=directus/directus`, `attempt=1`; created 2026-10-16T22:31:12Z,
  completed **success**.
- Code head: PR #134 branch `feat/expires-unit-generic-proof` at
  `733cbdb6cabf9acbbed2b042d8bc2a4bf298259e` (the reviewer-fixed
  driver: per-leg verdicts, credential-less bare leg, generic body
  checks). The Directus fixture contract is in-tree on that branch;
  harness-recomputed fixture render per side:
  `dbbdcfd4b8287be91918d2c2aaade11a4c9c7b8496ac16f0f9409b1f1656b610`.
- Why a re-run: the original proof (run `38967351907`, pack
  `evidence/38967351907/`) recorded 1 login acquisition on the bare
  leg of every side (the provider's proactive ensure). The fixed
  `_proof_passed` run offline over those recorded rows returns
  **False on all 4 sides**; under the reviewer's conditions the
  recorded proof does not stand.
- Predictions: `evidence/d137-auth-proofs/predictions-fixed-head.md`
  (relay commit `40985ec511aee10b3b6280554382144649a061f1`, committed
  2026-10-16T22:31:05Z, before this dispatch).
- Artifact: `readiness-directus-directus-1` (id `12079437987`);
  zip sha256 as downloaded:
  `82929769fd6d53ce804ae847bf4e4a3564a45dbb10a524ad7c0481a6b60f92e9`.

## Result — PROVEN 4/4, matching the predictions exactly

| side | registered SHA | status | leg 1 | leg 2 | leg 3 |
|---|---|---|---|---|---|
| C3 (role C) | `dc922b79` | READY | 200, `{"login": 1}`, Bearer | 200, `{"login": 1}`, Bearer | 401, 0 acquisitions, no Authorization header |
| C4 (role C) | `a5da59da` | READY | 200, `{"login": 1}`, Bearer | 200, `{"login": 1}`, Bearer | 401, 0 acquisitions, no Authorization header |
| checkpoint-bug-directus-1 fix^ | `7e9b0350` | READY | 200, `{"login": 1}`, Bearer | 200, `{"login": 1}`, Bearer | 401, 0 acquisitions, no Authorization header |
| checkpoint-bug-directus-1 fix | `93a3f393` | READY | 200, `{"login": 1}`, Bearer | 200, `{"login": 1}`, Bearer | 401, 0 acquisitions, no Authorization header |

## Contents

- `rows-authproof.json` — the artifact's rows file, as downloaded,
  unmodified. Each request record carries the scrubbed response
  body (new with the fixed driver).
- `pd-logs/` — all 4 side logs (no failing sides; included in
  full, each well under 1 MB, no trimming).

## Redaction

The harness scrubs by value before upload. Per-row `redactions`
counts: `bootstrap_value: 2` on every row (the generated admin
email appears in the two authenticated `/users/me` bodies and is
replaced with `<redacted:bootstrap_value>`), `key_file: 0`,
`acquired_token: 0`, `jwt_shape: 0`. Independent check over the
rows file as downloaded: grep for the JWT prefix `eyJ` returns 0
matches. Directus's own `password` field in the response body is
the app's literal mask `"**********"`, as the app serves it.
