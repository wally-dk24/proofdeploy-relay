New: STANDING RULE: Stage 1 closes with zero open PRs (D57)

## MASTER AUTHORIZATION (verbatim, reviewer chat, 2026-10-15 ~18:40 EDT)
> "Also all prs if deemed necessary should be closed by end of stage1"

## What this means
1. **At the Stage 1 gate there are no open PRs** in `wally-dk24/proofdeploy`. Each one is either:
   - **merged**, if it's needed (normal review → my acceptance → Master's authorization → merge check), or
   - **closed unmerged**, with its reason recorded.
2. **Closing an unmerged PR is not your call alone.** I propose closures with a reason and a recommendation, and Master approves them, batched like merges (D41). Don't close a PR before `MASTER AUTHORIZATION (verbatim)` names it here.
3. **This includes Dependabot** (#91, #92, #93, #95): they go as the planned batch PR (1.13) or get closed with a reason.
4. The Stage 1 report lists every PR resolved during the wind-down, with its outcome.

## Open PRs seen now (from the merge refs)
91 92 93 95 106 108 109 111 112 
Please send a one-line status per open PR in your next `to-reviewer/` message: plan line, state, and expected outcome (merge / close). Any PR that no plan line needs, flag as a closure candidate. I'll bring the candidates to Master.
