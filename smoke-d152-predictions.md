# D152 smoke test — predictions (committed BEFORE the first dispatch)

Smoke test under D152/D154/D159/D167, plan 1.10. NOT a registered measurement:
every measured record must show `measured_result: false` (D167). All runs are
labelled "smoke (D152)". Dispatches are made from `main` after PR #139 merges
(the D154/D167 merge); each run's D61 evidence pack states the dispatch ref's
merge-base with main (D155). `target_repo` is entered as the literal `golden`
for M1–M4 (D167). The `lockmismatch` revision is never a smoke or measured
unit (D159). Runs serialize on the `proofdeploy-model-calls` concurrency
group (queue: max); Master approves each run in GitHub himself (D100).

STOP RULE: any miss, false alarm, or INCONCLUSIVE stops further dispatching;
the run is classified by cause from its records and reported to the reviewer
before anything else runs.

Predictions (reviewer, D172) — the predicted outcome first, then the live
alternatives named in advance for each line:

- V1 verify golden node-express-sqlite, fix side → PASS, exit 0.
  Alternatives: FAIL exit 1 (the known-good fix side does not verify clean —
  a false alarm); INCONCLUSIVE (provisioning/auth/runner cannot decide).
- V2 verify golden python-flask-sqlite, fix side → PASS, exit 0.
  Alternatives: FAIL exit 1 (false alarm on the known-good side);
  INCONCLUSIVE (provisioning/auth/runner cannot decide).
- V3 verify golden node-express-sqlite, bug side → FAIL, exit 1 (catch).
  Alternatives: PASS exit 0 (a miss — the seeded bug verifies clean);
  INCONCLUSIVE (provisioning/auth/runner cannot decide).
- V4 verify golden python-flask-sqlite, bug side → FAIL, exit 1 (catch).
  Alternatives: PASS exit 0 (a miss — the seeded bug verifies clean);
  INCONCLUSIVE (provisioning/auth/runner cannot decide).
- M1 measure.yml unit_repo golden/node-express-sqlite, bug run → caught
  (fix^ fails, fix passes). Alternatives: missed (both sides pass — the
  probes do not discriminate); INCONCLUSIVE probe (no valid probes, or an
  environment-class failure).
- M2 measure.yml unit_repo golden/python-flask-sqlite, bug run → caught
  (fix^ fails, fix passes). Alternatives: missed (both sides pass);
  INCONCLUSIVE probe (no valid probes, or an environment-class failure).
- M3 measure.yml unit_repo golden/node-express-sqlite, clean run → no false
  alarm. Alternatives: false alarm (the clean unit is flagged);
  INCONCLUSIVE probe.
- M4 measure.yml unit_repo golden/python-flask-sqlite, clean run → no false
  alarm. Alternatives: false alarm (the clean unit is flagged);
  INCONCLUSIVE probe.
