# Evidence pack — readiness run 38933195241 (Directus, D98 contract)

- Run ID: 38933195241
- Workflow: readiness.yml (workflow_dispatch, attempt=1)
- Repo run: directus/directus — all 4 registered sides
- Head SHA: b7584c663a5ab74911e27540a20fb5fdbf731379 (PR #122, branch feat/directus-contract-d98; the D98 contract: build `npm_config_workspace_concurrency=2 pnpm run build`, migrate `cd directus && node cli.js bootstrap`, start `cd directus && node cli.js start`)
- Artifact: readiness-directus-directus-1 (id 12060981303)
- Artifact zip sha256 (as downloaded): 68670ff2defc858c8e834db05c9b53eedb028c1629d56c9871348caa782646b5
- Run conclusion: success; job window 16:35:49Z → ~16:57Z

## Results (attempt 1, rows-attempt1.json)

| unit / side | registered SHA | status | GET /server/ping |
|---|---|---|---|
| checkpoint-bug-directus-1 fix^ | 7e9b0350 | READY | 200 (4ms) |
| checkpoint-bug-directus-1 fix | 93a3f393 | READY | 200 (4ms) |
| C3 (clean, role C) | dc922b79 | READY | 200 (5ms) |
| C4 (clean, role C) | a5da59da | READY | 200 (3ms) |

READY 4/4 at attempt 1; no attempt-2 run (nothing non-READY to retry).
The build ran with workspace_concurrency=2 on every side; the migrate step
`cd directus && node cli.js bootstrap` initialized and applied its migrations
on every side (the D98 fix — the old contract died here with
`directus: command not found`).

## Contents

- rows-attempt1.json — the run's rows file, as downloaded.
- pd-logs/readiness-C3-clean-a1.log — READY-side reference log. No non-READY
  sides exist, so no failing-side logs are included.

## Redactions

Zero redactions applied by the packer: a credential-pattern scan and a
JWT-shape scan over the included files found no unredacted values. The
included log's bootstrap command line arrives pre-redacted by the harness
itself (ADMIN_PASSWORD and SECRET rendered as <redacted> / ***REDACTED***
markers). Every file is under 1 MB; no trimming was needed (the pd-log's own
elision markers are the harness's, left as-is).
