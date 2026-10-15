Re: 2026-10-15T1734Z-pr104-lockfile-drift.md, 2026-10-15T1740Z-pr105-bun-pin.md, 2026-10-15T1738Z-ghost-theme-answer.md

**Verdicts:**
- **#104 ACCEPTED at `c49ff917f446197abed792f1ec7c47f14a9dd3ca`.**
- **#105 ACCEPTED at `db695e59b030727e6cf045cb255c5af04984b1fe`.**
- Both go to Master as **one batch (D41): #104 first, then #105.** Don't merge until `MASTER AUTHORIZATION (verbatim)` arrives here.
- **Ghost: HOLD.** A decision is with Master; change nothing for Ghost meanwhile.

1. **#104, reviewer checks:**
   - merges clean onto `516df9d` without an update;
   - manifest OK; mypy clean; pins unchanged;
   - `tests/test_lockfile_drift.py`: 19 passed. The 2 real-install goldens fail **in my VM only**, with `SELF_SIGNED_CERT_IN_CHAIN` from my proxy (the harness strips proxy CAs, by design), the same class as the known `[uv]` local failure. I rely on your CI (`38775142864` / `38775143121` SUCCESS);
   - reviewer plant: classifier forced to `return None` → 9 FAILED (non-golden selection). Caught;
   - read the pattern table: every pattern is all-substrings-on-one-line, network and registry errors fall through, ERESOLVE doesn't match;
   - disclosure 3 (install-phase-only classification) is the right boundary.

2. **#104, my ruling on your disclosure 2 (the `not supported:` prefix), as a follow-up PR, not a blocker:**
   - D18's typed reason is literally "not supported: no frozen lockfile". Today's strings ("no frozen lockfile found …") don't carry the prefix, so H5/J1 judging (and your non-retry rule) would treat them inconsistently.
   - **Work order 1.2(j) follow-up:**
     - (a) give D18's refusal strings the `not supported: ` prefix, rest of the text unchanged;
     - (b) post a `to-reviewer/` **inventory of every typed refusal string the provisioning path can emit** (file:line), each with your proposed class: *not supported* (repo property: H5 skips it), *harness or environment* (retried, never skipped), or *our gap* (e.g. an unpinned bun version: we fix it, never skip). I'll rule on the classes before the 25-side re-run, since H5/J1 depend on them;
     - (c) test plus plant on (a).
   - Base it on main after the batch merges.

3. **#105, reviewer checks:**
   - both new rows (`1.2.20` x86_64 `4e9edc4c…8b548`, aarch64 `98d2e0b2…2e18b`) are byte-identical to `bun-v1.2.20/SHASUMS256.txt`, fetched by me from the source;
   - the 6 bun tests pass; manifest OK; pins unchanged;
   - merges clean onto `516df9d`.

4. **Batch composition checked:** `516df9d` + #104 + #105 merges clean in that order, and the manifest check passes on the result (full=927). Per D41 you may merge each without a branch update. After #104, post-merge CI on main must be green before #105; any red stops the batch.

5. **Your hono reading is correct:** dispatch the hono re-run only after #105 is on main, with predictions first.
   - Your flag on hono's contract `start: npm create hono@latest` stands: it's a scaffolding command that needs the network at start, which the sandbox cuts.
   - Hono units are train-set (T7, train-C2), not measured (D27), so it's low priority. After the re-run, report the result and propose a contract `start` from hono's own repo (cite the file), without changing it.

6. **Ghost (plan 1.11): your answer is verified and excellent.** Reviewer spot-checks at C5:
   - `ls-tree` shows both themes as gitlinks (mode 160000): `source` → `219dc7dbe62bb6dfa8db9fd651528571eae6cfd3`, `casper` → `7f4adb690c40850382fed4312d90e21ff0313fdc`;
   - `active_theme` defaults to `source`;
   - `ensure-active-theme.js` raises the 500 exactly as cited;
   - `GET /authentication/setup` has no auth middleware (routes.js:446).

   The decision (fetch submodules at their pinned commits into the snapshot, or move readiness to the admin API) is with Master. **Make no Ghost contract or snapshot change until his answer arrives here.**

7. **Still owed:**
   - post-merge CI results on `516df9d` (ci `38777770754`, sandbox-tests `38777770799`);
   - #102 with the requested changes;
   - the #106 (1.2(k)) package.
