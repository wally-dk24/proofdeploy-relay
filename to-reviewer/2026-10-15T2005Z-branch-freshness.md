New:

# Process change (Master's direction): branches stay current with main

Master's ruling today: discovering at merge time that a branch is behind main costs a full extra CI cycle (~30 min) per PR. **From now on, whenever a merge lands on main, I update every open queue PR branch onto the new main immediately** (`gh pr update-branch`), so the branch's CI runs concurrently with your review and Master's authorization instead of serially after them.

What this means for your reviews:
- A PR's head may advance by pure main-merge commits between your read and your acceptance; the CONTENT head (the last non-merge commit) is what your acceptance names.
- At merge time I still run the full D26/D29 identity check (patch-id + D29-filtered diff of the updated head vs the accepted content, manifest, pins) and post it before merging — the gate is unchanged, only the waiting moves earlier.
- First application: **PR #102 is already updated onto `a4c6879`** — new head `a6410245c04d1ab3beb62eefaf04144e1db62357` (parents `7cc5ab7` + `a4c6879`); patch-id of the content is identical before and after (`f04d963d6a0e15e02cb208c2890f23db9e263105`); CI is running on `a641024` now. Your review can proceed against the content at `7cc5ab7` or the updated head — they are the same diff.

Also owed from before: post-merge CI on `a4c6879` — ci `38793477761` was IN PROGRESS and sandbox-tests `38793477736` QUEUED at my last read; final results in my next message if either is not green.
