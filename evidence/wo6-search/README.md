# WO-6 Phase B — REG-C Addendum 2 live search (2026-10-15/16)

**Outcome: NO RESULT. Both executions of the accepted script died
before writing any output. There is no survivor list — it is
unknown, not zero.**

## What this pack points at

- Run record (both deaths recorded in full, verbatim tracebacks):
  PR https://github.com/wally-dk24/proofdeploy/pull/108,
  file `docs/registration/wo6-regc-add2-run-2026-10-15.md`,
  at commit `95e1c55da4cff5e93d25c2f949b9b6ad08da51ed`
  (branch `feat/wo6-regc-add2-script`).
- Script as run: `scripts/wo6/regc_add2_run.py` in that PR,
  sha256 `f0a3b99c7cb84a5d917d8e28f2ce8ef5e369c372191e8dab152f350cbeab51e5`
  (byte-identical to the accepted head `ff5ff37`; verified before
  each execution).
- Run from main `0889721874744f6cd6c0b76c9e6c56d0d610566a`;
  snapshot date 2026-10-15. Gate CI on the accepted head:
  ci `38826471742` SUCCESS, sandbox-tests `38826471563` SUCCESS.

## The two executions (D66)

1. 2026-10-16T00:18Z → 06:14Z (exit 1): died ~5h56m in, inside the
   Python sqlite tarball scan, when a tarball download broke
   mid-body (35,618,816 bytes read) and `http.client.IncompleteRead`
   escaped the fetcher's `OSError`-only retry loop. No output
   written (the script writes only at the end; its output
   directory was never created).
2. The single D66 re-run, 2026-10-16T06:17Z → 06:52Z (exit 1):
   died the same way ~35m in, on a recursive git-trees response
   (382,279 of 432,459 bytes). No output written.

Per D66, a re-run dying ends the matter on this side; what happens
next is Master's decision.

## Redaction

Nothing was redacted: the run produced no output files, and the
recorded logs contain only local file paths, byte counts, and the
public API paths already visible in the script. No credential,
token, or per-run secret appears anywhere (the token was supplied
via environment only; the script records only `authenticated`).
