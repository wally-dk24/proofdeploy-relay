# WO-6 blind run — predictions (committed before any dispatch)

Unit: paperless-ngx/paperless-ngx, `bug_id wo6-paperless-371c4f57`,
arm `skill` (skill v3, author hash `e04cb811…`), store contract
`eb60559b…`. Dispatch inputs are in the selection record
(`selection/wo6-bug-selection-record.json` in this repo).

Wally's prior, stated without reading the bug:

- D67 pipeline: EXPECTED TO PASS — both sides already READY under the registered contract on the measured-path sandbox (run 39079057983); the author receives the diff + snapshot; the verdict is mechanical and the record replayable.
- Verdict prior: **no_catch slightly favoured over candidate_catch; INCONCLUSIVE a real possibility.** Reasons: skill v3 caught both golden bugs and §1a targets exactly the oracle-polarity failure — but those were one-line bugs in tiny fixture apps. This is a real fix inside a large Django application: the author must locate an HTTP-observable behaviour tied to the fix and assert the fixture-truth polarity from the snapshot alone, with auth only as the generated superuser. If the changed behaviour is not reachable through the contract's API surface, expect INCONCLUSIVE or no_catch rather than a catch.
