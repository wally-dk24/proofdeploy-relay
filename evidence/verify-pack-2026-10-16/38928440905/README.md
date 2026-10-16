# Verify pack — run 38928440905 (python-flask-sqlite / fix)

- **Run ID:** 38928440905 — https://github.com/wally-dk24/proofdeploy/actions/runs/38928440905
- **Workflow:** `verify.yml` (workflow_dispatch; inputs app=python-flask-sqlite, change=fix), repo wally-dk24/proofdeploy
- **Head SHA:** `3eab21ac139307e37f5025eb7938743eb3b1fa02` (main, PR #120 merged)
- **Artifact:** `verify-python-flask-sqlite-fix-38928440905` (artifact id 12058326645)
- **Artifact zip sha256 (as downloaded):** `c1d966c93442a7aca62637560a5e89864e31f1289ea7c621b263b9d450210d12` (5,935 bytes)
- **Workflow conclusion:** failure (the workflow propagates verify's exit code 2)

## Result (from the artifact, not the workflow conclusion)

- **Verdict: INCONCLUSIVE, exit code 2**
- **typed_reason:** `environment` · **cause:** `model_transport`
- **Detail:** the model call failed: Groq API call failed: HTTP Error 429: Too Many Requests
- The evidence record shows both registered attempts rate-limited:
  `model_attempts: 2` — attempt_1 and attempt_2 both
  `model_transport: Groq API call failed: HTTP Error 429`. The run
  stopped before probe authoring: 0 probes, no provisioning.
- **Model:** `openai/gpt-oss-120b` (temperature 0.2)
- **Pins recorded in the evidence record** (all equal the registered pins):
  model client (author_prompt_builder_sha256) `55b7d93283d511b681f7af24dd78aafc0cc750d652ed965f94fe7a4ab1d28598`;
  author prompt (author_prompt_sha256) `fa72a0e6c4a1fa9fb204986e08c8eef4b11fc13786f41389ecf2f42ea8555cf3`;
  fixture template (bundle template_sha256) `0994d1e367ddb3c679e750451dd67eab3a3d974fc1620b7f750216cdcdf0323b`
- **Evidence record:** `verify-records/runs.jsonl`, record_id `0ccb328d6de44662a8ce27bb717a240e`, schema 1.5.0, measured_result: false
- **Replay:** replaying the record (replay_side over `c_results` +
  compute_verdict, code at the merged head) reproduces **INCONCLUSIVE** —
  identical to the recorded verdict. The record carries zero executed
  probes (the run stopped at the model call), so there is nothing to
  re-decide; the recorded cause stands in the record.

## Files

- `verify-output.json` — the verify JSON output (schema verify_output_version 1.0.0)
- `verify-records/runs.jsonl` — the evidence record
- `verify-run.log` — the run's captured verify output + exit line
- `verify-exit-code.txt` — `2`

## Redactions (D61)

- Applied by the collector: **0**. Pattern scan of every file in this
  pack for key-shaped strings (`gsk_…`, `sk-…`, api-key assignments)
  found none.
- Already redacted by the engine before upload (counts): the record's
  `contract_env.CANARY_SECRET` is `***REDACTED***` (1 value); no minted
  tokens exist on this run (0). No other credential values appear; the
  only token-shaped strings are `${AUTH_TOKEN}` placeholders.
- File sizes: all < 1 MB; no trimming was needed.
