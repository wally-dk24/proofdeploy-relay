Re: 2026-10-15T1805Z-pr104-pr105-accept-ghost-hold.md

**MASTER AUTHORIZATION (verbatim)**, reviewer chat, 2026-10-15 ~18:10Z, answering "Ghost: A (fetch submodules at their pinned commits into the snapshot) or B (move readiness to the admin API)": "Option A"

Logged as D46. The Ghost hold is lifted for this work order only. **The #104 → #105 batch is still awaiting Master's authorization; don't merge those yet.**

# WORK ORDER: plan 1.2(l), git submodules at their pinned commits in every snapshot (D46)

Relies on D46, rule 9, ADR 0012 (symlink rule) and D36 (our state stays outside the app's tree). Own PR, based on current main.

1. **What.** When a side's tree contains gitlinks (mode 160000) listed in `.gitmodules`, the snapshot includes each submodule's content at **exactly the recorded commit**, applied identically to both sides of a unit and to every repo on any stack. The Ghost contract stays unchanged: readiness remains `/`.

2. **How (on the host, at snapshot time; never run anything from the repo):**
   - read `.gitmodules` from the side's tree, not the working dir;
   - for each gitlink: resolve the URL (relative URLs like `../../TryGhost/Source.git` resolve against the repo's own GitHub origin);
   - `git fetch --depth 1 <url> <recorded-sha>` into a scratch bare repo outside the checkout (D36), then extract that commit's tree into the submodule path;
   - recurse into nested submodules, with a fixed depth limit you state;
   - git's object addressing guarantees the content equals the recorded commit; still assert the fetched commit id equals the gitlink.

3. **Safety (the URLs are untrusted repo input):**
   - **https only:** `-c protocol.allow=never -c protocol.https.allow=always`, which refuses `file://`, `ssh`, `git://` and `ext::`;
   - no credentials: `GIT_TERMINAL_PROMPT=0`, no credential helper, no askpass;
   - every argument passed as argv, never through a shell;
   - the submodule path must normalize to inside the checkout: refuse `..`, absolute paths and symlinked parents (ADR 0012);
   - stated per-fetch time and size limits;
   - nothing from the submodule is executed at snapshot time.

4. **Failure typing:**
   - a URL refused by the scheme or path rules, or a commit the remote doesn't serve ("not our ref", repo not found) → typed `not supported: submodule <path> at <sha> unavailable from <url>: <git's first error line>`, carrying the `not supported:` prefix (H5 class);
   - a network or transient error → the existing harness retry, never typed not-supported;
   - never a silent fallback to an empty directory.

5. **Recorded on each side's readiness row and evidence record:** `submodules: [{path, url, commit, tree_sha}]` (empty list when none). A schema-additive field, like 1.4(a) allows.

6. **Tests and plants:**
   - unit tests with a local fixture via an injected test-only transport (production code keeps https-only);
   - **one real test** fetching Ghost's `ghost/core/content/themes/source` at `219dc7dbe62bb6dfa8db9fd651528571eae6cfd3` from `https://github.com/TryGhost/Source.git` and checking the extracted tree id against the commit's tree;
   - refusal tests for `ext::`, `file://`, `ssh://` and a `../` path;
   - **Plant A:** skip the submodule step → the real Ghost test FAILS. **Plant B:** drop the protocol restriction → the `ext::`/`file://` refusal tests FAIL.

7. **Evidence to send** (`to-reviewer/`):
   - head SHA, CI run IDs, plants, manifest, pins;
   - **an inventory of gitlinks at every registered side** across all readiness repos (repo, side, path, commit), so we know what else this touches;
   - **for Ghost:** run the pinned gscan `check` + `format` against the fetched `source` theme at each of the 3 SHAs and report `hasFatalErrors` (I expect `false`). Then write a prediction for the Ghost readiness re-run (6 sides) before any dispatch: does GET `/` now return 200? Does the theme need any asset build (check whether its built assets are committed at `219dc7db`)?

**Done when:** CI green on both workflows; both plants caught; manifest OK; pins unchanged; the gitlink inventory and the Ghost gscan evidence posted. The Ghost readiness re-run follows the merge, with predictions scored.

**Sequencing:** this joins the queue after the #104 → #105 batch. #102 (requested changes) and #106 (1.2(k)) continue in parallel; send their packages when ready.
