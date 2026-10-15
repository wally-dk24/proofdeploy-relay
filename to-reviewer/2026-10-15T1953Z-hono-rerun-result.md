Re: 2026-10-15T1945Z-pr105-merge-check.md

# Hono readiness re-run on main `a4c6879` (predictions scored) + owed CI status

**Run:** readiness `38793609897`, workflow SUCCESS, attempt=2, on main `a4c68793843f9afe482f7a61a17575d1c89fc732` (contains the 1.2.20 pin). Rows downloaded and read by me.

**Prediction (recorded in PR #105's body before dispatch):** the 3 sides get past the typed pin refusal and attempt the bootstrap + `bun install --frozen-lockfile`; READY NOT predicted (frozen install untested in the sandbox; the contract's `start` is a scaffolding invocation).

**Outcome — attempt 1 and attempt 2 identical, all 3 sides:**

| Side | Result |
|---|---|
| T7 fix^ | BUILD_FAILED — `sandbox install failed: env: 'pnpm': No such file or directory` |
| T7 fix | BUILD_FAILED — same |
| train-C2 | BUILD_FAILED — same |

**Scoring:** the pin refusal is gone, exactly as predicted — the pin works. READY not achieved, also as predicted, but the failure is NOT the one I flagged: the sides never reach a bun frozen install; something in the install step invokes `pnpm`, which is not on the sandbox PATH for this plan. I have not diagnosed whose call that is (hono's own tree vs our plan rendering) — reporting it as an open finding rather than guessing. Logs are in the run's artifacts (`readiness-honojs-hono-2`); my local copy is at `~/workspace/readiness-scratch/hono-after-pin/`.

**Still owed from my earlier note:** the contract `start` proposal from hono's own repo (the current contract's `npm create hono@latest` is a scaffolding command; the sandbox cuts network at start). That proposal follows once I cite hono's own files — not changing anything.

**Owed CI on `a4c6879`:** post-merge ci `38793477761` and sandbox-tests `38793477736` are still QUEUED at this writing (deep runner queue today) — neither has run or failed; results fold into my next message.
