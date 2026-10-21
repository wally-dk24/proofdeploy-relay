# Lean PoC gate, round 3: predictions (committed before any dispatch)

Main `ad4ac1b6f78f4c162dc0b1a2604ca16f07f132d8` (train 8: judge skill v1 signed 2026-10-21, issue-register guards R1–R6). Three dispatches of `lean-poc.yml` from main: `golden`, `mutants`, `paperless`. Same units, five criteria and thresholds as rounds 1–2. Not measured results.

No-model rehearsal `39633507362` from the same main passed 12/12 sides (golden-bug, golden-clean, paperless-bug, 3 paperless cleans; base and head): provision ok, sign-in proof 2xx, 3 starts ok (2 restores), authenticated request 200. Evidence: relay `evidence/lean-rehearse-39633507362/`.

The round-2 disclosure stands (the reviewer knows the selected paperless fix's subject; Master kept the unit).

## Per criterion
1. **golden-bug FAIL, golden-clean PASS: predicted MET.**
   - golden-bug: seeded reads come first, so the difference is on note 1 (`done` false→true). Judge step 1 (facts) → regression quoting the clause `id 1 (done=false)`. If the model instead answers `correct` on the intent, rule 3 refuses it and the correction round asks again. FAIL `judged_regression`.
   - golden-clean: one group, `tag` added with "general"; no fact or earlier response names `tag`. Judge step 4 (intent) → correct. PASS.
   - Alternative: the model still answers `unclear` on golden-clean → INCONCLUSIVE `needs_information` (criterion 1 fails). This is the most likely single failure point.
2. **Mutants ≥ 70% of observable caught: predicted MET, ≥ 90%.** Same declared-refactor rule (code decides; no model calls). Seeded reads change which requests run first, so the observable count may differ from round 2's 15; 0 violations.
3. **Paperless bug, a real verdict: predicted MET; caught (bonus): predicted NO.** The rehearsal removed the round-2 crashes. Most likely INCONCLUSIVE `change_not_exercised` (B changes 20 backend files; 200-request budget) or `needs_information`; FAIL possible. Criterion-3 failure only if a new environment error appears.
4. **False alarms: predicted MET, 0 FAIL** across golden-clean and the 3 paperless cleans. The version-bump unit (`5569447e`) → INCONCLUSIVE `not_observable_over_http`. Risk: a head-only 5xx on the 570-file unit (`2ab1ac7c`) gives one hard-oracle FAIL (still within ≤ 1).
5. **≤ 10K tokens per unit: predicted MET.** Each call ≤ 3K including the correction reserve. A unit with a correction round uses about 2 calls; expected max ≤ 5K. 0 rate-limit errors expected (pacing).

**Gate overall: predicted PASS.** Most likely failure: golden-clean `unclear` (criterion 1). Stop rules predicted not triggered; the one at risk is "< 50% of impacted operations get a valid 2xx" on paperless, now measured from the recorded loop.
