# Lean PoC, linkding (first app after paperless): predictions (committed before dispatch)

Main `58bb1dfe1c5c1af5e3174935d6877068b336f6f3` (PR #182: linkding units; #173 declared runtime, #174 no-token proof, #180 R-LEAN-SEL A1 check L3, #181 declared runtime line 3.13). Judge skill v1 (`edbd3597`) unchanged; model client `1e063204` unchanged. One dispatch of `lean-poc.yml` with `units=linkding`: 1 bug unit + 3 clean units. Not measured results. Same success bar as round 4 (`claude/proposal-round4-real-app-catch-2026-10-21.md`): differences found and judged; a confirmed catch is the goal.

No-model rehearsal `39731539051` from the same main passed 8/8 sides (provision ok on python 3.13.16, static-token proof 2xx, no-token request refused 401, 3 starts ok, authenticated request 200). Evidence: relay `evidence/lean-rehearse-39731539051/`.

Blindness: better than rounds 2–4. The reviewer has NOT seen any subject, message, diff, file name or test of the bug unit or its fix (walk journal read through field-selecting scripts; record stored with subjects stripped). Known about the bug unit, counts only: B (`ffc1a690`, 2026-01-01) changes 72 files, 5 backend Python source files (+131/−93), 5 Python test files, the rest non-Python (+1779/−1559); the fix (`71d62f48`, 2026-09-12) changes 3 files: backend Python source +7/−0, one Python test +44/−0.

## Per unit
- **linkding-bug-71d62f48 (the real test).** B is small on the backend (≈224 changed Python source lines, vs 4,448 in paperless round 4), and linkding is a JSON REST API with no file-upload gate, so reach should be far better. Predicted most likely: INCONCLUSIVE `needs_information` or `change_not_exercised` (the 7-line fix suggests a narrow condition the generator may not hit). A confirmed FAIL (judged regression or hard oracle) on the bug: about 1 in 3. Reported: share of changed backend lines reached (predicted ≥ 50%), requests hitting changed code, differences found and judged, tokens.
- **Clean units `e50239cd` (1 source path), `0837b518` (1), `0490013e` (3):** 0 FAIL predicted; each INCONCLUSIVE `change_not_exercised`/`needs_information` or PASS. A false alarm (any FAIL on a clean unit) is a defect to be classified against the issue register.
- **Cost:** ≤ 10K tokens per unit; 0 rate-limit errors (13 s pacing).

## Stop rule
- Bug unit not reached (no changed backend line executed): stop and report the coverage gap with the reach numbers before any further run.
- Any clean-unit FAIL: stop, classify it against the issue register, and fix before rerunning.
