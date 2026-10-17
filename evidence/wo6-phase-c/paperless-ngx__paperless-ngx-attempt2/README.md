# D61 evidence pack — WO-6 Phase C, candidate #1: paperless-ngx/paperless-ngx (attempt 2 — D151 round 2)

- Run: `39033362054` — workflow `readiness.yml`, `repo=paperless-ngx/paperless-ngx`,
  `attempt=2` (the pass semantics: attempt 1 provisions the side once,
  attempt 2 re-provisions any non-READY side; one artifact carries both),
  dispatched 2026-10-17T12:39:03Z from branch `feat/wo6-regc-add2-script`
  at head `66d7556be1447c10b7db2eb5bd40f2896eaa9dde`, completed
  **success** 2026-10-17T12:45:13Z.
- Fix round: D152 fix budget **round 2 of 3** for this candidate (round 1
  was run `39031527915`, pack `paperless-ngx__paperless-ngx-attempt1/`).
  The fix is the reviewer's D151 ruling: the contract's build step
  creates the app's runtime dirs before migrate.
- Contract fix commit: `66d7556be1447c10b7db2eb5bd40f2896eaa9dde`
  (contract only), pushed 2026-10-17T12:38:54Z, before the dispatch.
  Contract: `readiness/contracts/paperless-ngx__paperless-ngx.yml`,
  sha256 `e189ea6ed2cf67ccd883d822dfd7d3112cc9365adb9b84df4d1ed278ad89f49e`.
  The change: `build: "true"` → `build: "mkdir -p data media consume"`
  (data = the declared `PAPERLESS_DATA_DIR`, holding `db.sqlite3`;
  media and consume are the exact paths the app's own round-1 check
  hints named, `/checkout/media` and `/checkout/consume`).
- Candidate: survivor #1 — 46,745 stars, app_path `src` (survivor
  record), registered HEAD `0cb2cdbfdd91a2078236f8851cd310261284591c`.
  Harness commit (row's `harness_sha`):
  `53ea74be483ee1b5c2b56d21de821afbf34f8306`.
- Prediction: `evidence/wo6-phase-c/predictions.md` (relay commit
  `efee47859a0dc0a67a8b32947171d709a6ee83b8`, pushed
  2026-10-17T12:38:45Z, before the contract-fix commit and the
  dispatch): READY, on the ground that round 1's only stopper was the
  two missing runtime dirs. **Scored RIGHT.**
- Artifact: `readiness-paperless-ngx-paperless-ngx-2` (id `12101651354`);
  zip sha256 as downloaded:
  `c1d89f260781c8d5a8899dad146fba31a21357f6ebd2b83f30fca0f2c4a12350`.

## Result — READY on attempt 1

Attempt-1 row: unit `WO6-C1`, role `C`, status **`READY`**,
`typed_reason` null, `last_error` null; runtime python 3.14.8
(declared `>=3.11`), package manager `uv`, install plan sha256
`92caaedf5ffd268df3e5eca95e1573844aaea420444d24abbad17789b3e84c37`,
network mode `none+socket`. The attempt-2 rows file is empty — no
non-READY side existed to re-provision.

From the pd-log, in order: the build step ran
`mkdir -p data media consume` (install step 7); migrate (install
step 8) then ran to completion (`Applying … OK` through the full
migration set) — the round-1 `SystemCheckError` ERRORS for
`PAPERLESS_CONSUMPTION_DIR` and `PAPERLESS_MEDIA_ROOT` are gone; the
system checks at app start report WARNINGS only (the missing `gs`
and `tesseract` consumption binaries, non-blocking, as in round 1).
The app booted (`Paperless-ngx version: v3.3.1`), the readiness poll
saw `GET /` → 302 and `GET /accounts/login/?next=/` → 200, and the
harness recorded `sandbox app READY at http://127.0.0.1:36023`.

READY log: `pd-logs/readiness-WO6-C1-clean-a1.log` (this pack).

## Contents

- `rows-attempt1.json`, `rows-attempt2.json` — the artifact's rows
  files, as downloaded, unmodified.
- `pd-logs/readiness-WO6-C1-clean-a1.log` — the side's log in full
  (20,240 bytes as downloaded; no trimming), scrubbed as stated below.

## Redaction

`PAPERLESS_SECRET_KEY`'s contract value (a throwaway committed for
this ephemeral provision) appears in the sandbox command lines in
the log: 10 occurrences, each replaced with
`<redacted:PAPERLESS_SECRET_KEY>`. No other credentials, tokens, or
per-run secrets appear in the pack (grep over the pack for
GitHub-token and JWT/key shapes: 0 matches). The rows files needed
no redaction.
