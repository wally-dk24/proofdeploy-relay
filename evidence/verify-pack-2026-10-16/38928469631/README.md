# Verify pack — run 38928469631 (python-flask-sqlite / bug)

- **Run ID:** 38928469631 — https://github.com/wally-dk24/proofdeploy/actions/runs/38928469631
- **Workflow:** `verify.yml` (workflow_dispatch; inputs app=python-flask-sqlite, change=bug), repo wally-dk24/proofdeploy
- **Head SHA:** `3eab21ac139307e37f5025eb7938743eb3b1fa02` (main, PR #120 merged)
- **Artifact:** `verify-python-flask-sqlite-bug-38928469631` (artifact id 12057937785)
- **Artifact zip sha256 (as downloaded):** `c3f552e57aec05a2a87e868da4b90d1174e79ef57bf7629b7585867a484e514d` (10,390 bytes)
- **Workflow conclusion:** failure (the workflow propagates verify's exit code 1)

## Result (from the artifact, not the workflow conclusion)

- **Verdict: FAIL, exit code 1** · typed_reason/cause: none (only
  INCONCLUSIVE carries them)
- **Model call:** succeeded on attempt 1 (`model_attempts: 1`). The
  author produced 2 valid probes; provisioning ran in the sandbox
  (provision 95.0s; total 132.0s); isolation_mode `none+socket`;
  sandbox evidence asserts `userns_mode: userns-remap`.
- **Probe outcomes (from the evidence record):**
  - Probe 0 — FAIL: GET /notes/1 with the credential-provider token
    returned **404** (expected 200; `done` json_path not found). The
    seed note (id 1) belongs to the seed user; the credential provider
    registered a fresh user, so the note was not visible to it.
  - Probe 1 — PASS: created its own note and asserted `done == true`
    on it — the semantics the diff itself introduced (the planted
    defect flips `done` to `row['done'] != 1`); stored done=0 reads
    back true under the defect.
  - The FAIL verdict is the mechanical rollup of 1 fail + 1 pass.
    Stated plainly: the verdict matches the predicted FAIL/1 outcome,
    but the failing probe failed on the 404 above, not on the changed
    line — the probe that exercised the changed line encoded the
    diff's own semantics and passed.
- **Model:** `openai/gpt-oss-120b` (temperature 0.2)
- **Pins recorded in the evidence record** (all equal the registered pins):
  model client (author_prompt_builder_sha256) `55b7d93283d511b681f7af24dd78aafc0cc750d652ed965f94fe7a4ab1d28598`;
  author prompt (author_prompt_sha256) `fa72a0e6c4a1fa9fb204986e08c8eef4b11fc13786f41389ecf2f42ea8555cf3`;
  fixture template (bundle template_sha256) `0994d1e367ddb3c679e750451dd67eab3a3d974fc1620b7f750216cdcdf0323b`;
  probe set sha256 `be6fa094301d57d65181f56944b1aa413fc87f41c159fb0bb327f15d4081000e`
- **Evidence record:** `verify-records/runs.jsonl`, record_id `4f49c3f6dd8e4de1b5c2e5555c25f6ba`, schema 1.5.0, measured_result: false
- **Replay:** both probes re-decided from the recorded responses
  (probe 0 → fail, probe 1 → pass) and the replayed rollup is **FAIL** —
  identical to the recorded verdict.

## Files

- `verify-output.json` — the verify JSON output (schema verify_output_version 1.0.0)
- `verify-records/runs.jsonl` — the evidence record
- `verify-run.log` — the run's captured verify output + exit line
- `verify-exit-code.txt` — `1`

## Redactions (D61)

- Applied by the collector: **0**. Pattern scan of every file in this
  pack for key-shaped strings (`gsk_…`, `sk-…`, api-key assignments)
  found none.
- Already redacted by the engine before upload (counts): the record's
  `contract_env.CANARY_SECRET` is `***REDACTED***` (1 value); the 4
  minted app tokens are each recorded as `***REDACTED***` (4 values);
  probe request headers record `Bearer ***REDACTED***`. The golden
  app's own fixture text (seed user named in its committed fixture)
  is the app's published test data, not a secret.
- File sizes: all < 1 MB; no trimming was needed.
