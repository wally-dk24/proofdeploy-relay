# WO-R5-O: hard checks only flag regressions (R9); run-time values are noise (R10)
Worktree `/home/claude/wt-r5-oracle`, branch `fix/lean-r5-oracle`. Files: `proofdeploy/lean/compare.py`, `proofdeploy/lean/run.py` (clock only), tests.

R9 (round 4 false alarm on a clean unit: 6 items). A hard failure counts only when the change made the request worse:
1. Status-type failures (5xx, timeout, transport error) count only if the same request was healthy on BOTH old runs (status < 500, response received). Old side already 5xx → not a hard failure (recorded as `preexisting_failure`, listed in the manifest).
2. "Body not parseable JSON" counts only when the new side's status is 2xx AND the old side returned 2xx JSON for that request. A 4xx with a plain-text body is data, not a failure.
3. A new traceback in the new side's server log counts only if it is not explained by a request already excluded by 1 (match by request window: the traceback appears between that request's start and the next request). Record the exclusion.
4. Timeouts: keep counting them (1 applies), but record `timeout_seconds` used.
Tests with realistic shapes: old 500 → new timeout = not a failure; new endpoint (old 405) → new 400 text = not a failure; old 200 JSON → new 200 HTML = failure; old 200 → new 500 = failure (stays); old 200 → new timeout = failure. Plant each rule.

R10 (round 4: `date_joined` and `index_last_modified` differed between sides, stable within each). Values that move with run time are noise:
5. First check why the frozen clock is not effective in the sandbox (`wrap_clock_cmd` / libfaketime): report the cause from the code and the round-4 manifests' `sides.*.clock` fields (no paperless content). If it can be made effective in the sandbox with an image-available library, do it.
6. Independently, in comparison: a field whose old and new values both parse as ISO-8601 or epoch timestamps, and where both lie within the wall-clock window of their own side's runs (record the windows per run), is noise with reason `run_time_value`, never a difference. Test both ways (a timestamp outside the run window, e.g. a fixed date changed by the code, is still a difference). Plant it.
