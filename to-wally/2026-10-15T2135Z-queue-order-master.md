Re: to-wally/2026-10-15T2130Z-pr107-merge-check.md

# Queue order (Master-confirmed)

## MASTER AUTHORIZATION (verbatim, reviewer chat, 2026-10-15 ~17:35 EDT)
On my answer that #109 (1.2(l), D46) is not a WO-6 prerequisite, and that #106 stays first with #109 and #108 reviewed as their packages arrive:
> "Yea, let wally know of the order too"

## The order
1. **#106 (1.2(k)): first.** It gates WO-6 Phase B. Send the package naming its final, plant-free head.
2. **In parallel, review as each package arrives (no fixed order between them):**
   - **#109 (1.2(l), submodules at pinned commits).** Seen at `f5c2c94` (content `6570a7f`). Package per the 1811Z work order: plants A and B, the real Ghost `source` fetch at `219dc7db…`, the gitlink inventory across all registered sides, gscan on the fetched theme at the 3 Ghost SHAs, and a Ghost readiness prediction. **Aim to have it merged before WO-6 Phase C (provisioning):** if a WO-6 survivor has gitlinks, provisioning it needs 1.2(l).
   - **#108 (WO-6 Phase A) re-submission** with F1–F6 (my 2115Z file). No live run before my acceptance and #106's merge.
   - **WO-CI-SPEED:** the measurement table first, then the lazy probe and collect-once in one PR (D53).
3. **Then:** 1.2(j) follow-up part 2, the #102 two-side-summary follow-up, the cleanup follow-up, and the hono contract proposal (report only).
4. **After the next merge batch:** the WO-CI-SPEED shard re-pack, then the Dependabot batch (#91/#92/#93/#95), now due since #102 merged.

When packages are ready together, I'll bring Master one batch (D41) in the order they were accepted, with #106 first if it's among them.
