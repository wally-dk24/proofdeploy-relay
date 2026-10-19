New: O21 replay — re-ask only the 11 failed golden bug runs with the new recipe (D236)

**Plan line:** 1.7 author preparation (D219, D233–D236). **Type:** experiment; no registration, not a measured result, no sandbox, no app provisioning. **Master's direction (verbatim):** "I still do not want to rerun the entire stack again. I want to do this on the failed ones we had earlier".

## What this is
The 11 golden bug runs that missed (slots in `slots.json`) all gave the author byte-identical app material per app. Each slot sends that same material again to the **same model through the same pinned client**, with only the instructions changed: prompt header v8 + skill v5 draft (`claude/skill-registration-v5-draft.md`). There is one optional correction round. The reply is scored offline with a pre-registered judge (`judge.py`), because the right answer for these golden bugs is known.

## Files (verify `SHA256SUMS` before use; do not edit any of them)
- `node_bug.v8.txt`, `python_bug.v8.txt`: the exact prompts to send.
- `check.py`: the reviewer's prototype evidence check plus `correction_message()`.
- `judge.py`: the pre-registered verdict.
- `slots.json`: 11 slots → prompt file.

## Procedure (serial, one slot at a time, no other prompts, no tuning)
For each slot 1..11:
1. `r1 = proofdeploy.model_client.call_model(prompt_text)`. Use main `e8bf530`'s pinned client, unchanged (pin `a35238a5…`, gpt-oss-120b on Cerebras, temperature as pinned). Save the raw text `r1`, `finish_reason` and `usage`.
2. Run `check.check()` on `r1` (or `check.surviving()`). Then call `msg = check.correction_message(res)`.
3. If `msg` is not None, make **one** more call with prompt = `prompt_text + "\n\n---\n\nYour previous answer:\n\n" + r1 + "\n\n" + msg`. Save `r2` the same way. The final reply is `r2`.
4. Otherwise the final reply is `r1`.
5. `judge.judge(prompt_path, final_reply)` → CAUGHT or MISSED, plus details.

Transport failures (HTTP 429/5xx, timeout): wait 60 s and retry that call once; record it. A second failure marks the slot ERROR (not MISSED) and stops for a ruling.

## Venue
The model key lives only in the GitHub `measurement` environment, and local model calls are held (D208) until the local credential entry holds the Cerebras key.
- **A (preferred, fastest):** if Master confirms the local entry now holds the Cerebras key, run locally with a small script that imports the pinned client.
- **B:** otherwise, a small PR adding a dispatch-only workflow `author-replay.yml` (the `measurement` environment, the `proofdeploy-model-calls` concurrency group with `queue: max`, SHA-pinned actions, no new secret) plus the script. It merges under D139 after my review and is dispatched with Master's approval.

Tell me which venue before running.

## Pre-registration (reviewer, committed with this file, before any call)
- **Pass:** ≥ 9 of 11 slots CAUGHT. For comparison, all 11 were misses originally.
- **Reviewer's prediction:** 9–11 CAUGHT. The main risk is evidence rejections that survive the correction round, which `judge.py` scores as MISSED (dropped probes).
- **Reported per slot:** verdict; whether the correction round was used; rejection reasons in r1 and r2; finish_reason; usage.

## Evidence back (relay `evidence/o21-replay/`)
- `results.json`: per slot, as above.
- Every prompt actually sent, and r1/r2 raw, each with its sha256.
- The script you ran and its sha256.
- The client sha256 asserted at run time.
- Main SHA.
- The venue (A or B, with run ID).

Cost: ≤ 22 calls, about $0.02.

## Do not
- edit the prompts, the checker or the judge;
- change any pin;
- run golden clean units, held-out, checkpoint or paperless units;
- start any build of R-SEL / R-BUNDLE / R-METHOD (awaiting Master's signatures).
