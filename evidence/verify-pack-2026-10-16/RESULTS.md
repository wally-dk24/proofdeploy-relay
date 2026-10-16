# Verify pack 2026-10-16 — results and prediction scorecard

Four `verify.yml` runs from main `3eab21ac139307e37f5025eb7938743eb3b1fa02`
(PR #120 merged), real pinned model `openai/gpt-oss-120b`. Predictions:
`predictions.md` in this directory (relay `58232de`, committed before
the first dispatch). Per-run evidence: the `<run-id>/` sub-packs.

## Results

| Run | App / change | Verdict | Exit | typed_reason / cause | Replay |
|---|---|---|---|---|---|
| 38928376000 | node-express-sqlite / fix | INCONCLUSIVE | 2 | environment / model_transport | INCONCLUSIVE — identical |
| 38928413088 | node-express-sqlite / bug | INCONCLUSIVE | 2 | environment / model_transport | INCONCLUSIVE — identical |
| 38928440905 | python-flask-sqlite / fix | INCONCLUSIVE | 2 | environment / model_transport | INCONCLUSIVE — identical |
| 38928469631 | python-flask-sqlite / bug | FAIL | 1 | — (FAIL carries none) | FAIL — identical |

## Scorecard against the predictions

- **node-express-sqlite / fix — MISS.** Predicted PASS / 0. Actual
  INCONCLUSIVE / 2 (environment / model_transport): the Groq model
  call returned HTTP 429 on both registered attempts; the run stopped
  before probe authoring.
- **node-express-sqlite / bug — MISS (provider outcome).** Predicted
  FAIL / 1 if the author catches the defect; the predictions file
  pre-declared a bug-side INCONCLUSIVE as a measured outcome with
  expected class `probe`. Actual class is `environment`
  (model_transport, HTTP 429 on both attempts) — the defect was never
  exercised, so this scores as neither a catch nor an author miss.
- **python-flask-sqlite / fix — MISS.** Predicted PASS / 0. Actual
  INCONCLUSIVE / 2 (environment / model_transport), same 429 shape.
- **python-flask-sqlite / bug — outcome HIT, mechanism differs.**
  Predicted FAIL / 1 if the author catches the planted defect; the
  verdict and exit match exactly (FAIL / 1) and replay reproduces
  them. Composition, from the record: probe 1 exercised the changed
  line but asserted the diff's own semantics and passed; the FAIL
  came from probe 0, which got a 404 on the seed note (owned by the
  seed user; the credential provider registered a fresh user). The
  predicted catch mechanism is not what produced the FAIL.

## Notes

- Three of four runs died on the provider's rate limit (HTTP 429)
  after the registered single retry; the fourth (dispatched last)
  got through on attempt 1. No run was re-dispatched; the outcomes
  stand as measured.
- Every record replays to its recorded verdict (4/4 identical).
- All pins recorded in the evidence records equal the registered
  pins (model client 55b7d932…, author prompt fa72a0e6…, fixture
  template 0994d1e3…).
- 1.10's remaining done-when item is unchanged: the WO-6 real-app
  verify output after plan 1.5(d).
