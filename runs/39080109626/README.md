# Failed blind run 39080109626 — WO-6 plan 1.5(d), first attempt

Committed per D203 item 4 / D68 (the failed run's pack, append-only).

## Run

- **Run ID:** 39080109626 — https://github.com/wally-dk24/proofdeploy/actions/runs/39080109626
- **Workflow:** `measure.yml`, dispatched from **main `63fdb628015395ce224ad38030c734c59a1b8f3a`** (merge-base with main: `63fdb62` — the dispatch ref was main itself).
- **Target repo:** https://github.com/paperless-ngx/paperless-ngx
- **Dispatch inputs (as dispatched, D201/D202):**
  - `unit_repo`: **empty** (D202 ruling — the CLI resolves the contract from the cloned repo's origin)
  - `arm`: skill (v3, author skill sha256 `e04cb811…`)
  - `bug_id`: `wo6-paperless-371c4f57`
  - `base_ref` (B's first parent): `3a34bdc7b078486bc85a97ce309a1c8180cdfc88`
  - `bug_ref` (B): `7dca0bfa6af103b76185aada3ce736b0ac1abc01`
  - `fix_ref`: `371c4f57d3ab208fc261e159fa86ca1dc80cb334`
  - `clean_ref`: empty

## Outcome

**Failed pre-measurement at the bundle leak check.** The run died in the
"Run measured unit" step before any model call, any probe, or any scoring.
Verbatim error line:

```
proofdeploy: error: secret leak in bundle file pyproject.toml: 2 secret value(s) present; refusing to send to the model
```

(step exit code 2). No evidence or score record was created
(`records/run-39080109626` was never pushed); there is no verdict.

## Classification (D203): our gap

The two flagged values are values of the registered contract's own env
keys — `PAPERLESS_CHANNELS_BACKEND` (`channels.layers.InMemoryChannelLayer`)
and `PAPERLESS_CACHE_BACKEND` (`django.core.cache.backends.locmem.LocMemCache`).
Both are public Django backend class paths, copied into the contract from
paperless-ngx's own configuration, and both are printed in the app's own
committed `pyproject.toml` (its `[tool.pytest_env]` section). The bundle
leak check treats every non-public contract env value as a run secret;
the run had no way to declare these two keys public (`measure.yml`
exposed no input for the CLI's existing `--public-env-key`), so the
check found its own "secrets" in the target app's public file and
refused the bundle — fail-closed, exactly as designed, on a false
positive. Not an environment failure and not a `not supported:` refusal.

## Contents

- `failed-log.txt` — the failed step's full log as preserved from the
  run (78 lines), sha256
  `24f8cb7e4e1772857c30c79cdc0dd36e72c3451f04a46200d834118fc9afdee6`.

## Redactions

**None.** The log was scrub-checked line by line before committing:
the only credential-named entry is `GROQ_API_KEY: ***` — GitHub's own
masking in the step's env dump; the key's value never appears. No
per-run generated credential (admin user/password, secret key) appears
anywhere in the log — the run died before provisioning produced any.
The only long tokens present are the three public commit SHAs, a file
path, and the run URL.

## Standing state

The predictions committed before this dispatch
(`predictions/wo6-blind-predictions.md`, relay `b5f78ef`) **stand** —
no model call happened, so the bug remains unseen. The fix (D203:
a `public_env_keys` input on `measure.yml`) and the re-run of the
**same** bug with the same inputs follow separately.
