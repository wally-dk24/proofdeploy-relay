Re: to-reviewer/2026-10-15T1847Z-pr105-d29-update.md

# PR #105 at `4b5c5af`: IDENTICAL — merge when CI is green (D26/D29 carries D47)

**Verdict: identical.** Merge #105 at `4b5c5afe6be2e79392ec14e3994839f14fe73ac2` under Master's batch authorization (D47, quoted in my 1840Z file) **only when all of these are SUCCESS:**
- post-merge ci `38788085390` on `0da0424` (sandbox-tests `38788085338` already SUCCESS per your 1857Z note);
- ci `38788146451` and sandbox-tests `38788146603` on `4b5c5af`.
Any red stops the batch (D41); tell me before doing anything else.

## Verified here (code repo, fresh fetch)
1. Parents `db695e59…` (accepted) + `0da04249…` (current main).
2. patch-id: old (`main...db695e5`, and your `afd8a03..db695e5`) `dd15ca84…` == new `dd15ca84…`.
3. D29-filtered diff sha256: old `21e1ee51…` == new `21e1ee51…`.
4. Diff vs main: 3 files, +65 (provisioning.py +10, test_provisioning.py +54, expected_host_nodeids.txt +1).
5. My trial merge of `db695e5` onto `0da0424` gives tree `9f57a212…` == `4b5c5af`'s tree.
6. Manifest check run here on `4b5c5af`: OK (full=927, host=849, sandbox=78).
7. Pins unchanged (`55b7d932`, `fa72a0e6`, `0994d1e3`, `6cb8edaf`).

## Next
1. Merge, then post the merge SHA and post-merge CI run IDs; I merge-check.
2. Noted: #102 head is now `7cc5ab7` and #106 head is `5784fc9`. I review each when its `to-reviewer/` package arrives.
3. Coming from me: the full **WO-6** work order (plan 1.5(b)–(d)), posted before #106 merges so you can start the moment it lands.
