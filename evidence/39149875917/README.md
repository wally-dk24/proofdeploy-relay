# D61 evidence pack — WO-6 blind run, skill v4 on Cerebras (D219/O20) — run 39149875917

- Run ID: 39149875917
- Workflow: measure (`.github/workflows/measure.yml`), conclusion `success`
- Run URL: https://github.com/wally-dk24/proofdeploy/actions/runs/39149875917
- Dispatch ref: `main` at `d886215cf3bd3266509adaa40ea7c01b32da6b25`
  (contains current main; merge-base with main is itself).
- Unit: real (non-golden) target — target_repo `https://github.com/paperless-ngx/paperless-ngx`,
  unit_repo EMPTY (contract-store path), bug_id `wo6-paperless-371c4f57`, arm=skill,
  base `3a34bdc7b078486bc85a97ce309a1c8180cdfc88`, bug `7dca0bfa6af103b76185aada3ce736b0ac1abc01`,
  fix `371c4f57d3ab208fc261e159fa86ca1dc80cb334`, public_env_keys
  `PAPERLESS_CHANNELS_BACKEND PAPERLESS_CACHE_BACKEND`.
- Predictions: relay `805307027ea6d115ff187ec7fa705640d14ca611`
  (`predictions/wo6-blind-run-v4-cerebras.md`, committed 2026-10-18T17:29:53Z,
  before this dispatch at 17:30:12Z).
- This is a REGISTERED blind run (plan 1.5(d), D67 pipeline proof):
  `measured_result: true` in the evidence record — CONFIRMED. `sandboxed: true`.
- Record schema 1.7.0: `model_finish_reason` = `stop`; `model_usage` present:
  prompt_tokens=7937 (cached 2560), completion_tokens=1896,
  reasoning_tokens=1349, total_tokens=9833.

## Per-record checks (void conditions)

- `contract_source.store_path` = `readiness/contracts/paperless-ngx__paperless-ngx.yml` — CONFIRMED.
- Contract sha256 = `eb60559b9fdd879e5cb8ca30d16c7cbfb1cea356e9385b8e367a249ab7c19e6a` — CONFIRMED.
- Rendered fixture sha256 `17edd5611e866bfb824aff6b3693671fc106cd75bd49e2d5db8fac47203c26db` present in the record — CONFIRMED.
- `skill_sha256` = `ff601eb15b09e6e0bf8f48b2037f0ab8b2a1b0c90ac022156f76d0169380b36a` (skill v4) — CONFIRMED.
- `public_contract_env_keys` = exactly `[PAPERLESS_CHANNELS_BACKEND, PAPERLESS_CACHE_BACKEND]` — CONFIRMED.
- `model_request.provider` = `cerebras`; `model_request.model` = `gpt-oss-120b`;
  endpoint `https://api.cerebras.ai/v1/chat/completions` — CONFIRMED.
- Model client pin `a35238a5…` present in the record — CONFIRMED.
  Author prompt pin `author_prompt_sha256` = `fa72a0e6c4a1fa9fb204986e08c8eef4b11fc13786f41389ecf2f42ea8555cf3` — CONFIRMED.
- The author call: `model_attempts` = 1, finish_reason `stop`,
  5 parsed probes, `invalid_probe_blocks` = 0.

## Where the record lives (measure.yml uploads no artifact zip)

- Branch: `records/run-39149875917`
- Evidence commit: `b60e40ba89846d63866f3ff74ac9a090aa59c1b0` (named in the score record);
  records branch head at packing time: `abb1e28`.
- sha256 of `runs.jsonl` as downloaded from the records branch:
  `2fd01a4b58888c5efb493d7a5dea536e475569cd530ed97fc7e8cb63c50ea108`
- sha256 of the run-logs zip as downloaded from the Actions API:
  `8cf47ad7d013064ca6ce95acbd744e8e3ed17a4cd10c887810bbf354448cc91b`
  (job log packed here as `job.log`, 60802 bytes — under 1 MB, untrimmed).

## Scrubbing

The harness's own record already carries its redactions (`***REDACTED***`
markers in the evidence record). Every file in this pack was scanned for
credential patterns (Groq `gsk_`, Cerebras `csk-`, GitHub tokens, bearer
values, the contract's readiness throwaway value): 0 matches.
No additional scrubbing was applied.

## Outcome

- Observed: the run's OWN score record carries verdict **`inconclusive`**.
- Cause (metadata): BOTH sides' provisioning ended `build_failed` in the
  sandbox install step — the app refused to configure because
  `PAPERLESS_SECRET_KEY` was not set in the install environment
  (`django.core.exceptions.ImproperlyConfigured`). Neither side reached
  READY, so no probes executed: `fix_parent_results` = [] and
  `fix_results` = [].
- Classification: environment-class INCONCLUSIVE (provisioning), not an
  author or transport failure — the author call completed cleanly
  (finish `stop`, 5 valid probes, 0 invalid blocks). Per D67 an
  environment INCONCLUSIVE does not pass; the cause is fixed and the
  SAME bug re-run, on instruction. No re-dispatch was made.
- Replay with the dispatch ref's own code (d886215 tree):
  `replay_verdict` = `inconclusive` — matches the stored score.
- Post-merge CI on the dispatch main `d886215`: `ci` 39149030497 SUCCESS,
  `sandbox-tests` 39149030419 SUCCESS — the harness main is green; the
  failure is in this run's provisioning path, not a red main.

CONTAMINATION GUARD: this pack's README and the accompanying report carry
metadata only. The fix commit's diff, tests and message were not opened,
quoted or summarized anywhere in this work.
