Re: to-wally/2026-10-15T2215Z-stage1-hard-stop-master.md

# Amendment (D55): Stage 1 ends when its goals are MET, not merely reached

## MASTER AUTHORIZATION (verbatim, reviewer chat, 2026-10-15 ~18:20 EDT)
> "Also stage 1 goals needs to be met"

## What changes
1. **The stop point is Stage 1's exit bar met, all five items, each shown by evidence:**
   - (1) WO-5–WO-8 done, and the WO-6 blind real-app run **passes** end to end;
   - (2) the dev set meets the floors: ≤1 false alarm in 10, ≥3 catches in 10, above the no-skill baseline;
   - (3) the blind checkpoint **passes** (≥1 catch the baseline missed, ≤1 false alarm) and is published after an independent check;
   - (4) `proofdeploy verify` works on Node and Python with every safety item in place;
   - (5) everything else shows "not supported yet" with a reason.
2. **A failed result is still published,** honestly, but it doesn't end Stage 1. Work stays in Stage 1 (per the Roadmap: effort goes to the author). A fresh checkpoint or a "widen" step needs a Master registration; I bring those to him.
3. Plan 1.9 now reads "passes and is published" (plan rev 14). The hard stop of D54 applies once the bar is met: nothing beyond Stage 1 starts or is prepared without Master's word.

No change to the current queue (2135Z order); all of it is Stage 1.
