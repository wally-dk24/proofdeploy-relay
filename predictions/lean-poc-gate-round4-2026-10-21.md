# Lean PoC round 4 (paperless only): predictions (committed before dispatch)

Main `70e2a219186ba8e06391ff2e4e5f09c9ac5847e2` (train 9: sign in once, aim at changed code, varied records through the API, scaled request budget, judge sees only the code that ran; judge skill v1 unchanged). One dispatch of `lean-poc.yml` with `units=paperless`: 3 bug units + 3 clean units. Not measured results. Success definition and stop rule fixed in `claude/proposal-round4-real-app-catch-2026-10-21.md` (signed off by Master).

No-model rehearsal `39674957358` from the same main passed 12/12 sides (provision ok, sign-in 2xx, 3 starts ok, authenticated request 200). Evidence: relay `evidence/lean-rehearse-39674957358/`.

Disclosure (as rounds 2–3): the reviewer knows the subjects of the selected fixes; no diff, file or test of any B or fix was seen.

## Per unit
- **paperless-bug-b992f9fc (the real test).** Predicted: INCONCLUSIVE `change_not_exercised` remains the single most likely outcome (B: 4,448 executable changed lines; budget now 1,000), but with differences found AND judged (none `ai_budget`): that meets the round-4 success bar. A confirmed FAIL (catch) is possible; I put it at about 1 in 4. Reported: share of changed lines reached (round 3: ~14%; predicted 25–50%), requests hitting changed code, differences found and judged.
- **paperless-bug-371c4f57 (upgrade-path only, D229).** Predicted: INCONCLUSIVE `not_observable_over_http` (B touches migrations only; typed before provisioning, no model call). Correct answer, not a miss.
- **paperless-bug-d3710856 (frontend-only B).** Predicted: INCONCLUSIVE `not_observable_over_http` (no backend source change; typed before provisioning). Correct answer, not a miss.
- **Clean units:** 0 FAIL predicted. `5569447e` → `not_observable_over_http`; `2ab1ac7c` and `17796357` → INCONCLUSIVE `change_not_exercised` or `needs_information`. Risk: the new edge-value and seeded-record requests provoke a head-only 5xx on the 570-file unit → one hard-oracle FAIL (≤ 1 allowed).
- **Cost:** ≤ 10K tokens per unit; more judge calls than round 3 because differences now reach the judge; 0 rate-limit errors (pacing).

## Stop rule (from the proposal)
If b992f9fc reaches more of the change but still yields no judged difference, or the bug stays unreached, stop and report with one option: a size cap on the selected change (selection rule, Master's signature, fresh blind walk).
