# WO-R2-J: judge fixes (a) evidence-check list markers, (b) declared-refactor rule, (c) pacing + 429 backoff
Plan line 0.3 (a)(b)(c). Decisions D261, D266 (C1a, C1b, C2), D267 items 1–3. Worktree `/home/claude/wt-r2-judge`, branch `fix/lean-r2-judge`. Files: `proofdeploy/lean/judge.py`, `proofdeploy/lean/verdict.py`, tests. Do NOT touch `experiments/lean-poc/gate.py` (another builder owns it).

## (a) Evidence check strips list markers and whitespace (C1a)
The judge prompt shows seed facts as `- <fact>` lines; the judge quoted `- seed user …` and `check_evidence` rejected it (`quote_not_in_source`).
- Before the verbatim match, normalise the quote: for each line of the quote, strip leading/trailing whitespace and ONE leading list marker matching `^(?:[-*•]|\d{1,3}[.)])\s+`; then collapse whitespace as `_norm` does today. Use the normalised quote for every later step (verbatim, self-justifying, value checks).
- Nothing else is loosened: a quote that is not verbatim after normalisation is still rejected.
- Tests: the exact round-1 golden-bug shape (quote `"- " + fact`) now passes the verbatim step; `"1. " + fact` passes; a marker-only quote (`"- "`) is `empty_quote`; a quote with a changed word is still `quote_not_in_source`.

## (b) Declared-refactor rule, decided by code (C1b)
When the change's stated intent declares no behaviour change, the correct value of any stable base≠head difference is the base value. Code decides; the model is not asked about those groups.
- `declares_no_behaviour_change(intent) -> str | None` returns the matching sentence of the intent (sentences split as `seed_facts_of` splits, i.e. on `(?<=[.!?])\s+`, plus line breaks), or None. A sentence matches when, lowercased and whitespace-normalised, it contains one of exactly these phrases: `no behaviour change`, `no behavior change`, `no functional change`, `no behaviour changes`, `no behavior changes`, `no functional changes`. Nothing else matches (the word "refactor" alone does NOT).
- In `run_judge` (and therefore in replay via the same pure path): when it returns a sentence, EVERY group gets, without a model call, `Judgment(verdict="regression", model_verdict=None, expected_value=<group.before>, evidence_source="intent", quote=<the sentence>, check={"ok": True, "reason": None, "passed": ["declared_no_behaviour_change"], "rule": "declared_no_behaviour_change"})`. For `added_field` (before is absent) record `expected_value=None` plus `"expected_absent": True` in `check`. No call plans, no calls, 0 tokens.
- `decide` already turns an ok regression into FAIL `judged_regression`; its details must show `source: "intent"`, the quote and `rule: "declared_no_behaviour_change"` (add the rule to each regression detail; None for model-judged ones).
- Replay must reproduce it exactly (`replay()` raises nothing; prompts_sha256 empty).
- Tests: intent `"no behaviour change intended"` + one stable difference → FAIL judged_regression, expected = before, 0 calls, stub runner never invoked; intent `"refactor: tidy note handling"` alone → model path used; `"No functional changes."` in a multi-sentence message → matched sentence recorded; replay of each record round-trips.

## (c) Pacing and HTTP 429 backoff (C2)
Free tier: 5 requests per minute. Round 1 got HTTP 429 on 5 mutants.
- A process-wide pacer in `judge.py`: before every model call (including retries), wait until at least `min_call_interval` seconds have passed since the previous call STARTED in this process. Default 13.0 s; override with env `PROOFDEPLOY_MIN_CALL_INTERVAL` (float ≥ 0; invalid → ValueError naming the variable). Clock and sleep are injectable (module-level functions tests can patch); tests never really sleep.
- 429 detection: the pinned client raises `RuntimeError("model API call failed: HTTP 429: …")`, normalised by `_call_pinned` to `ModelCallError(cause="model_transport")`. Parse `HTTP (\d{3})` from the message; 429 is a provider limit.
- On 429: back off 20 s, then 40 s, then 60 s (on top of pacing), at most 3 retries (env `PROOFDEPLOY_429_RETRIES`, default 3). Exhausted → `CallResult(status="error", error_cause="provider_limit")`. Other transport errors keep today's single retry.
- Record each wait in `attempts` (e.g. `attempt_2: waited 20.0s after 429`) and add `waited_seconds: float` to `CallResult` (default 0.0; old records still load).
- `decide`: if any unclear group's cause is `environment:provider_limit` → `INCONCLUSIVE` with cause `provider_limit` (checked where `environment` is decided today, before it; never PASS).
- Tests: two calls 1 s apart on the fake clock → second waits 12 s; 429 then success → one 20 s backoff, status ok; four 429s → `provider_limit`, verdict INCONCLUSIVE provider_limit; a non-429 transport error → old behaviour (one retry); env override parsed; replay of a provider_limit record round-trips.

## Done when
All above tests exist and pass; each of (a), (b), (c) planted by you and caught; existing `tests/test_lean_judge.py`, `tests/test_lean_pipeline.py` and `tests/test_lean_gate.py` pass; manifest OK; ruff, mypy clean. Report per 00-common.md item 9.
