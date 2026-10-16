# Verify pack 2026-10-16 — predictions (committed BEFORE the first dispatch)

- **What this is:** the D61 evidence pack for `proofdeploy verify` with the
  **real pinned model** (plan 1.10; D84 item 3 / D92 item 4). Four
  `verify.yml` dispatches from main, one per golden-app change below.
- **Code under test:** proofdeploy main
  `3eab21ac139307e37f5025eb7938743eb3b1fa02` (PR #120 merged).
- **Post-merge CI gate (read before dispatch):** ci `38925650904`
  SUCCESS, sandbox-tests `38925651014` SUCCESS, both on `3eab21a`.
- **Workflow:** `.github/workflows/verify.yml` (dispatch-only; inputs
  `app` + `change`; the job waits at the **`measurement` environment**
  approval gate — Master is its required reviewer — before running).
- **Predictions committed at:** 2026-10-16T15:58Z (before any dispatch).
- **Registered exit codes:** 0 PASS · 1 FAIL · 2 INCONCLUSIVE ·
  3 not supported (typed) · 4 usage/config · 5 internal.

## Grounding

The sandbox suite on this code (scripted model) proved: good change →
PASS / exit 0; planted defect → FAIL / exit 1; unprovisionable change →
INCONCLUSIVE / exit 2 with class `environment`. These runs use the
**real pinned model**, so the author may or may not catch a planted
defect, and may or may not produce a valid probe. A bug-side PASS or
INCONCLUSIVE is a **measured outcome, not a harness failure**, and is
scored against these predictions as-is.

## Per-run predictions

| Run (app / change) | Predicted verdict | Predicted exit | typed_reason / cause |
|---|---|---|---|
| node-express-sqlite / fix | PASS | 0 | none (typed_reason carries no refusal on PASS) |
| node-express-sqlite / bug | FAIL | 1 | none on FAIL — this holds IF the author catches the planted defect |
| python-flask-sqlite / fix | PASS | 0 | none |
| python-flask-sqlite / bug | FAIL | 1 | none on FAIL — IF the author catches the planted defect |

Bug-side live alternatives, stated in advance:

- **PASS / exit 0** — the author missed the defect (measured outcome).
- **INCONCLUSIVE / exit 2** — a live possibility on the bug sides. If
  it occurs, the expected shape is class **`probe`**: cause
  `author_output_invalid` when the author's output yields no valid
  probe, or cause `probe_inconclusive` when an executed probe's
  result is inconclusive. (A provisioning-side failure would instead
  be class `environment`, cause `provisioning_failed` — not expected
  on these golden apps, whose provisioning the sandbox suite proves.)

## After approval

Each run uploads one artifact `verify-<app>-<change>-<run-id>`:
`verify-output.json`, `verify-records/`, `verify-run.log`,
`verify-exit-code.txt`. The artifact's outputs/records/log are
collected into this directory per run (as `evidence/<run-id>/`
sub-packs) and the verdicts scored against the table above.
