# D61 evidence pack — WO-6 Phase C, candidate #1: paperless-ngx/paperless-ngx (attempt 1)

- Run: `39031527915` — workflow `readiness.yml`, `repo=paperless-ngx/paperless-ngx`,
  `attempt=2` (the pass semantics: attempt 1 provisions the side once,
  attempt 2 re-provisions the non-READY side; one artifact carries both),
  dispatched 2026-10-17T12:11:09Z from branch `feat/wo6-regc-add2-script`,
  completed **success** (the harness ran and wrote its rows; a non-READY
  side is data, not a harness failure).
- Registration commit: `596077a9cecd8c856b98037275dc916faa8adc7b`
  (contract + manifest unit WO6-C1 + workflow choice), pushed
  2026-10-17T12:10:59Z, before the dispatch.
- Contract: `readiness/contracts/paperless-ngx__paperless-ngx.yml`,
  sha256 `702739a11873aeb534b585272e3ca79938658e2bf7a1fea0aa988169055d4f75`.
- Candidate: survivor #1 — 46,745 stars, app_path `src` (survivor record),
  registered HEAD `0cb2cdbfdd91a2078236f8851cd310261284591c`.
  Harness commits: attempt 1 `ece451b24ac78ded07f85a01e0bd15484696c6a4`,
  attempt 2 `aa933ccac4a83a1dbd9b4c9445744e004ebc393f`.
- Prediction: `evidence/wo6-phase-c/predictions.md` (relay commit
  `2d8092526b8cfc13b4112d17476ddac083edc1bc`, pushed 2026-10-17T12:02:04Z,
  before the registration commit and the dispatch). **Scored WRONG on the
  stopper:** the prediction named the frozen install (cp314 wheels) or
  Django startup checks as the likely failure; the install in fact
  completed in full (uv 0.13.0, `uv lock --check` clean, all 274 locked
  packages installed on Python 3.14.8, incl. torch 2.14.0+cpu), and the
  side stopped at the contract's **migrate** step on the app's own
  system-check ERRORS (below).
- Artifact: `readiness-paperless-ngx-paperless-ngx-2` (id `12100816642`);
  zip sha256 as downloaded:
  `0e3a925b1712f7131cc85edb1ca6c018a16959834a9a0ebfc32348f9ff281c5d`.

## Result — NOT READY on both attempts (identical)

Both rows: status `BUILD_FAILED`, runtime python 3.14.8 (declared
`>=3.11`), package manager `uv`, install plan sha256
`d86406bbe7697035c89c5dfe41b10ba9402379fb0a55bdab05fa52c94f1f74b2`,
network mode `none+socket`. Typed reason (the harness's own field):
`sandbox install failed: SystemCheckError: System check identified some issues:`.

The failing step is the contract's migrate step
(`/checkout/.venv/bin/python src/manage.py migrate --noinput`, install
step 8), which Django's system-check framework stops with two ERRORS
(the app's own text, from the pd-logs):

```
?: PAPERLESS_CONSUMPTION_DIR is set but doesn't exist.
	HINT: Create a directory at /checkout/consume
?: PAPERLESS_MEDIA_ROOT is set but doesn't exist.
	HINT: Create a directory at /checkout/media
```

plus WARNINGS only for the missing `gs` and `tesseract` binaries
(consumption tools; they do not block the checks).

The two directories are the app's runtime data directories: its
compose deployment mounts them as volumes and its own deployment
creates them before migrate — a bare checkout contains neither
(empty directories are not tracked), and the generic contract as
committed creates neither. The walk is stopped here per the Phase C
rules and reported; the candidate is NOT skipped.

## Contents

- `rows-attempt1.json`, `rows-attempt2.json` — the artifact's rows
  files, as downloaded, unmodified.
- `pd-logs/` — both side logs in full (14,335 and 14,244 bytes as
  downloaded; no trimming), scrubbed as stated below.

## Redaction

`PAPERLESS_SECRET_KEY`'s contract value (a throwaway committed for
this ephemeral provision) appears in the sandbox command lines in
the logs: 9 occurrences per log, 18 total, each replaced with
`<redacted:PAPERLESS_SECRET_KEY>`. No other credentials, tokens, or
per-run secrets appear in the pack (grep over the pack for token/JWT
shapes: 0 matches). The rows files needed no redaction.
