# Second WO-6 blind run (plan 1.5(e)), attempt on fix d3710856 — predictions, committed BEFORE dispatch

Date: 2026-10-20. Written by the orchestrator (Opus, D237), blind: IDs, dates, check letters and sizes only.

## Selection (verified by the orchestrator by ID only)
- Fresh walk under R-SEL + R-SIZE + Amendment 1 from main `d8e2ecb172a13bcf69993ec20aef31840662f79d`, script `04f63dd1…`; walked 64: a 43, b 4, c 5, d 0, e 11 (all oversized: 80,049 to 1,340,972 chars), selected 1. Record branch `records/wo6-bug-selection-2026-10-20` @ `241d1ef5e672a2ae7138f2fb2bb8cbfd9c378c39`.
- Fix `d3710856992a91783783819217b43392aab73e00` (committed 2026-08-30T14:50:50Z), fix^ `14b6a41b88e75fb7d35aa84b33986870bfd1aa69` (its only parent).
- B `aa67fd3aefa6489984fc15a5c32c1ad2b16ac23a` (2026-08-09T07:15:27Z), B^ `17dc482872cf62e12965f6207772c83d1deb651b`; B is in tag `v3.1.0` (created 2026-08-27T01:42:44Z, before the fix).
- Check (e): author prompt 56,176 chars (≤ 80,000), measured in a full clone, the same regime as measure.yml's clone (D255).
- Check (d): readiness run `39350296019` from `d8e2ecb1`: fix^ and fix READY, userns-remap, none+socket, contract `eb60559b…`, render `17edd561…`. No rehearsal (D248).

## Dispatch
measure.yml from main `d8e2ecb1`, dry_run none: target `https://github.com/paperless-ngx/paperless-ngx`, unit_repo empty, arm skill, bug_id `wo6-paperless-d3710856`, base `17dc4828…`, bug `aa67fd3a…`, fix `d3710856…`, public_env_keys `PAPERLESS_CHANNELS_BACKEND PAPERLESS_CACHE_BACKEND`. Master approves in `measurement`.

## Required on the record (else void)
Schema 1.9.0; measured_result true; dry_run null; contract_source `readiness/contracts/paperless-ngx__paperless-ngx.yml` sha `eb60559b…`; render `17edd561…`; skill-author `75d52a77…`; template v8 `09c55dca…`; model_client `1e063204…`; provider cerebras, model gpt-oss-120b; public keys exactly the 2; container_env_keys the 6.

## Prediction
1. D67 pipeline conditions HOLD: both sides READY; ≥1 valid probe; executed on both sides; mechanical verdict; record committed; replay reproduces.
2. The model call succeeds (prompt ≈ 56K chars, about 15–18K tokens, under the 30K TPM and 65K context limits). A correction round, if needed, may hit the per-minute limit; the client retries it once after 60 s (D244). A transport failure now yields one retry and an INCONCLUSIVE record (#156), never a crash.
3. R-BUNDLE: the bundle carries `src/paperless/urls.py`; probes target routes that exist.
4. R-METHOD: every surviving assertion carries verbatim evidence; finish_reason stop.
5. Verdict: UNKNOWN, no lean (candidate_catch and no_catch both live).
