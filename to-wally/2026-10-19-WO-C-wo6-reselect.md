New: WO-C — re-run the WO-6 bug selection under R-SEL (D242, D247). No model calls.

**Why:** the first WO-6 bug (commit 9, `371c4f57`) was upgrade-path only (D229), and R-SEL now rejects it at check (b). This work order re-runs the registered walk from main, so the next qualifying commit becomes the blind-run bug. The rules are REG-C item 7 + Addendum 2 + D169/D177/D190 + R-SEL (D242), all already in `scripts/wo6/regc_bug_select.py` on main.

**BLINDNESS (hard rule):** you and the reviewer stay blind to the selected bug. Never open, print or report:
- any diff, patch or file content of any walked commit;
- any commit message or subject;
- any test.

Report metadata only: SHAs, dates, check letters and reasons, tag names, counts, run IDs, hashes. The script's stdout is designed to carry no subjects. Do not add any output.

## Steps
1. Clone fresh from main: `git clone -q https://github.com/wally-dk24/proofdeploy /home/claude/ws-C && cd /home/claude/ws-C` (state `git rev-parse HEAD`). Then `python3 -m venv .venv && .venv/bin/pip install -e ".[dev]"`.
2. Use a fresh work directory: `/home/claude/wo6c/` (it must not exist yet).
3. Run, from `/home/claude/ws-C`:
   ```
   .venv/bin/python scripts/wo6/regc_bug_select.py --check-d github \
     --main-sha "$(git rev-parse HEAD)" \
     --out /home/claude/wo6c/wo6-bug-selection-record.json
   ```
   - The defaults are correct: `--repo` is the signed-off app (paperless-ngx at its recorded HEAD `0cb2cdbf`); `--survivors` is the committed list.
   - Check (d) dispatches `readiness.yml` with pair inputs to GitHub runners. The `gh` CLI here is authenticated and uses the REST API. These are readiness runs with no model, so no approval is needed.
4. **Stops:**
   - If the script stops for a ruling (our gap, unruled class, exhausted retries) or errors, stop and report its last 30 stdout lines (they carry no subjects) and the journal's last entries (metadata fields only).
   - Do not change the script. Do not resume with different arguments.
5. **Expected:**
   - Commits 1–8 reject as before (a 6, b 2).
   - Commit 9 (`371c4f57`) now rejects at (b) with `upgrade-path only`.
   - The walk continues until a commit passes (a)–(d).

## Report back (metadata only)
- main SHA; script sha256; app repo and HEAD.
- Walked count; tally by failing check; for commit 9, its check and reason.
- **Selected:**
  - fix SHA and fix^ SHA;
  - B SHA, B's committer date and B's first parent;
  - tag name and its creatordate;
  - the fix's committer date;
  - for each check (d) dispatch: run ID, rows READY per side, isolation, network and contract sha256.
- sha256 of the output record and of each journal file.
- Commit the record and the journal to a new branch `records/wo6-bug-selection-2026-10-19` (data only) and push it. State the head SHA.
- Do not open a PR. Do not read the selected commit's diff.
