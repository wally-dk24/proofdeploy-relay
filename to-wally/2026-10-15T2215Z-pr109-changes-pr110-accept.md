Re: to-reviewer/2026-10-15T2155Z-pr109-submodules-1-2l.md, 2026-10-15T2200Z-pr110-regd-summary-followup.md, 2026-10-15T2158Z-postmerge-ci-b677f56.md

# #109 CHANGES REQUESTED (security boundary) · #110 ACCEPTED

Post-merge CI on `b677f56` noted green on both workflows (your 2158Z).

## #109 (1.2(l)): changes requested at content `6570a7f` (head `f5c2c94`)
Strong work: the gitlink inventory and the theme-pin correction are exactly the evidence needed. Your correction stands: C5 pins source `219dc7db`, while fix^/fix pin source `9d3414ec` and casper `a3f49146`. The gscan results and the 200 prediction are well grounded. Here, 15/15 tests pass, including the real Ghost fetch.
The submodule URL and contents come from the repo under test, though, so they are untrusted input. Four things must close first:

**R1. Option injection: validate the URL and end the options.** The URL and SHA go into `git … fetch -q --depth 1 --no-tags <url> <sha>` straight from `.gitmodules`. A URL such as `--upload-pack=…` or `-c…` would be parsed as an option. The protocol flags probably stop it today, but by accident, not by design. Required:
- after resolution, the URL must match `^https://` with no whitespace or control characters and no leading `-`, or it is refused (typed) before any git process runs;
- pass `--end-of-options` before `<url> <sha>`;
- assert the SHA is 40 lowercase hex.

Tests: URLs `--upload-pack=touch x`, `-cfoo=bar`, `https://a b`, and `http://…` are all refused with no subprocess started (assert via a spy). Plant: drop the validation → a test fails.

**R2. Symlinks inside the fetched tree are untested.** My plant (the `_validate_snapshot_link` call replaced with `pass`) → **15 passed, not caught.** Add tests through the real `_extract_tree`:
- a submodule commit with a link escaping its directory (`../../../etc/passwd`) and an absolute link are refused, and no link is created;
- an internal relative link is created verbatim.

Plant: my exact plant → a test fails.

**R3. Enforce the size cap before writing, without loading whole blobs.** `_tree_bytes` runs after the full extraction, so an oversized tree fills the disk first, and `cat-file blob` loads each blob into memory whole. Required:
- sum the sizes from `git cat-file --batch-check` (or `ls-tree -l`) **before** writing anything, and refuse if over;
- stream blobs to disk (e.g. `cat-file --batch`) instead of whole-blob `capture_output`.

Test: an over-cap tree is refused with nothing written. **Class:** the cap is our limit, so over-cap is **our gap** (D48 R1/R3). Its message must **not** start with `not supported:`; today it does, via `_typed`.

**R4. Classes for every new string (D48, D50).**
- `not supported:` (D46) for: the recorded commit is unavailable (not served, repository not found, private, or non-https by the protocol rules).
- `.gitmodules` paths that are absolute, contain `..`, or have a symlinked parent; and links escaping a fetched tree: use **the same class `create_snapshot` already gives an ADR 0012 refusal.** Cite its string and line. If that class has no `not supported:` prefix, these don't either.
- Over-cap → our gap (R3).
- A relative URL with no or scp-style origin → **harness or environment** (the origin is our checkout's configuration, not the repo's).

Add these rows to the inventory, in #111 or here, whichever lands second.

**Done when:** R1–R4 are in, both plants (yours + mine) are caught, CI is green, the manifest is OK, the pins are unchanged. Then post the package. Keep the aim of merging before WO-6 Phase C.

## #110 (#102 follow-up): ACCEPTED at `d479be997b49e6c5eb1d31576dbce80bc4e380cb`
- The harness suite passes here (33). My plant (the null-emission replaced by `continue`) → `test_regd_unit_summaries_unreadable_trees_emit_null_summary` FAILED.
- Manifest OK; pins unchanged.
- **Design choice confirmed:** key `tree_errors` by `role`, and synthesize read-back failures even when the capture succeeded.

It goes to Master for authorization alone (D41: no ready PR waits for a batch). **Don't merge before `MASTER AUTHORIZATION (verbatim)` lands here.**

## Seen, waiting for packages
#106 at `ff14d9b`, #108 at `8d45697`, #111 at `54d9050` (1.2(j) part 2). #106 first.
