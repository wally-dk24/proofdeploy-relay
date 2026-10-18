# Failed blind run 39082717051 — WO-6 plan 1.5(d), re-run after D203/D204

Committed per D68 (the failed run's pack, append-only). The first
attempt's pack is at `runs/39080109626/`.

## Run

- **Run ID:** 39082717051 — https://github.com/wally-dk24/proofdeploy/actions/runs/39082717051
- **Workflow:** `measure.yml`, dispatched from **main `c6e44afcb7b587bf9f894bc1324b1863f3816615`** (the PR #144 merge commit; merge-base with main: `c6e44afc` — the dispatch ref was main itself).
- **Target repo:** https://github.com/paperless-ngx/paperless-ngx
- **Dispatch inputs (as dispatched, D201/D202 + D204):**
  - `unit_repo`: **empty** (D202 ruling — the CLI resolves the contract from the cloned repo's origin)
  - `arm`: skill (v3, author skill sha256 `e04cb811…`)
  - `bug_id`: `wo6-paperless-371c4f57`
  - `base_ref` (B's first parent): `3a34bdc7b078486bc85a97ce309a1c8180cdfc88`
  - `bug_ref` (B): `7dca0bfa6af103b76185aada3ce736b0ac1abc01`
  - `fix_ref`: `371c4f57d3ab208fc261e159fa86ca1dc80cb334`
  - `clean_ref`: empty
  - `public_env_keys`: `PAPERLESS_CHANNELS_BACKEND PAPERLESS_CACHE_BACKEND` (the D203/D204 addition)

## Outcome

**Failed at the author (model) call.** The bundle leak check that
stopped the first attempt **passed** this time — the failed step's env
dump shows `PUBLIC_ENV_KEYS: PAPERLESS_CHANNELS_BACKEND
PAPERLESS_CACHE_BACKEND`, and the run proceeded past bundle assembly
to the model call, which the provider refused. Verbatim error line:

```
proofdeploy: error: Groq API call failed: HTTP Error 413: Payload Too Large
```

(step exit code 2). No evidence or score record was created —
`records/run-39082717051` does not exist (verified against the code
repo's branch list). There is no verdict, and no model answer was
received: the provider refused the request before processing it.

## Classification: provider free-plan ceiling — not a harness defect

Groq's free plan for `openai/gpt-oss-120b` caps usage at 8K tokens per
minute / 200K tokens per day (Groq's published table; confirmed on the
account's own Limits page). This bug's author request is ~9K tokens
(3 changed files), just over the per-minute ceiling, and Groq answers
such a request with HTTP 413. The D203/D204 fix worked exactly as
designed: the two keys were validated fail-closed, passed as
`--public-env-key`, and the leak check passed. The on-disk bundle size
is not the request size; this is a plan ceiling, not a bundle wall and
not a `not supported:` refusal.

## Contents

- `failed-log.txt` — the failed step's full log as downloaded from
  the run (89 lines), sha256
  `9bc200ab289432705e0db44e2fd7e26e6b92179873e4c624a4014f6277b03735`.

## Redactions

**None.** The log was scrub-checked line by line before committing,
plus a pattern sweep for credential-shaped values: the only
credential-named entry is `GROQ_API_KEY: ***` — GitHub's own masking
in the step's env dump; the key's value never appears. No per-run
generated credential appears anywhere in the log. The only long
tokens present are the three public commit SHAs and the run URL.

## Standing state

The predictions committed before the first dispatch
(`predictions/wo6-blind-predictions.md`, relay `b5f78ef`) **stand** —
the request was refused before processing, no model answer exists,
and the bug remains unseen. The re-run of the **same** bug follows
once the provider ceiling is lifted.
