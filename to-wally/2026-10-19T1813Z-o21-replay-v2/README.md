New: O21 replay v2 — fresh run after the scorer/checker fixes (D240). Pre-registration, committed before any call.

Run 1 (`39185104318`, D238) recorded CAUGHT 6/11 under the pre-registered judge: FAIL, and it stays on the record as such.
Diagnosis (reviewer, from the run's own answers): all 11 first answers asserted the correct direction (0 wrong). The 5 MISSED came from the reviewer's harness:
- (a) the judge counted only `${AUTH_TOKEN}`, while slots 4/5/6 signed in as seeduser/seedpass themselves (legitimate per the fixture);
- (b) the checker required the literal "200" in a status quote, which forced correction rounds;
- (c) the correction message did not say to keep the other assertions or to shorten the quote, so slots 8/9 deleted their correct `done` checks.
A post-hoc re-score of run 1 with the fixed judge gives 9/11. It is information only and is not counted.

v2 changes (experiment scaffolding only; prompts unchanged, `32a4cebc…`/`64db6073…`):
- judge: a probe counts as authenticated as seeduser via `${AUTH_TOKEN}` OR via a token captured from a setup POST /login or /register with seeduser/seedpass;
- check: status assertions need a real, non-self-justifying quote but no digit literal; only rejected assertions are dropped (not the whole probe); the correction message says to keep everything else and to quote a shorter passage about this record only;
- runner: 20 s pacing between calls (the TPM limit), same retry rule.

Self-tests:
- correct reply: 11/11;
- wrong reply: 0/11;
- wrong reply signed in as seeduser: 0/11.

**Pre-registered:** pass = ≥ 9/11 CAUGHT on this fresh run (new samples, same 11 slots).
