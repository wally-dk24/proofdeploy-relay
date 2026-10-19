New: WO-A — prompt v8 + skill v5 + R-METHOD + R-BUNDLE in the product (D242)

**Plan line:** 1.7 author preparation (D219, D233–D242). **Signed by Master (D242):** relay `signed/2026-10-19-o21-v5-v8/` (commit `8768962`). Verify its `SHA256SUMS` first:
- `skill-v5-author.md`: `75d52a77…`
- `prompt-v8-header.md`: `52ae0da3…`
- `o21-registrations-signed.md`: `9af08ac8…` (R-BUNDLE and R-METHOD sections are the spec)

**Tested design:** the reviewer's prototype `experiments/o21-replay/check.py` and `judge.py` at `experiment/o21-replay` `a745e55` (replay v2 passed 10/11, D241). The product version must behave like `check.py` v2.

## Setup
- `git -C /home/claude/proofdeploy fetch -q origin main && git -C /home/claude/proofdeploy worktree add /home/claude/ws-A -b feature/o21-recipe-v8 origin/main`. Work only in `/home/claude/ws-A`.
- `python3 -m venv .venv && .venv/bin/pip install -e ".[dev]"`.
- Read `CLAUDE.md` on branch `docs/claude-md` (`git show origin/docs/claude-md:CLAUDE.md`, after `git fetch origin docs/claude-md`) and follow it.

## Tasks

1. **Signed docs.** Copy the three signed files byte-for-byte to `docs/evaluation/o21/`, with a `SHA256SUMS`.

2. **Skill v5.**
   - Rewrite `docs/evaluation/skill/skill.md` as the audited source so that `python3 scripts/generate_author_skill.py` writes `docs/evaluation/skill/skill-author.md` **byte-identical** to `skill-v5-author.md` (sha256 `75d52a77…`).
   - The generator replaces the sentence matching `Every numbered item carries a provenance tag:.*?An untagged item is a drafting error.` with "Each item teaches a probing technique. Study the pattern, not the specific example." So put that tag-explanation sentence in the source intro, in the position that yields the signed text. Read the generator before starting.
   - Tag every item: R1–R4 and items 1–14 and 18–23 `[PROC]`; item 15 `[DEV: T1]` (CodeIgniter4); item 16 `[DEV: T2]` (Fastify `179619c8`); item 17 `[DEV: T4]` (Django `ebcb13b3`). Check the T-numbers against `docs/evaluation/dev-set-v2-2026-10-09.md` and report any mismatch.
   - Set `EXPECTED_SKILL_HASH` in `proofdeploy/fixture.py` to `75d52a774f505dafd0ccde84cf91c9a1b8c485b2e8465e106b78d6d43268310c` and update its comment.

3. **Prompt template v8.**
   - New file `proofdeploy/author_prompt_v8.md`. v7 stays untouched; `PROMPT_TEMPLATE_FILENAME = "author_prompt_v8.md"`.
   - Its Framing, Output contract and Caps line sections reproduce the signed header so that **a rendered prompt's text before the line `## Authoring skill` equals `prompt-v8-header.md` exactly** (rstrip both). This is a required test, run against a real golden bundle.
   - Add the cap `max_route_bytes: 32768`.
   - Add a Body section `## Route declarations (selected mechanically; may be incomplete)` after `## Touched file contents`.

4. **Changed-line markers.** In the touched-file contents, prefix every line that the diff adds or changes (the new-file line numbers from the hunk headers' `+` lines, computed from the diff, never by string matching) with `<<CHANGED>> `. Deterministic.

5. **R-BUNDLE.**
   - Implement the route-declaration selection exactly as R-BUNDLE items 1–3 in the signed doc: within the unit contract's `app.path`; excluding TEST, DOC, vendor and migration paths; the Python and Node patterns; proximity order; the 32 KB cap; a `route_map_truncated` flag; included and omitted paths in the bundle manifest and the record; route files already shown as touched files named "shown above"; included files always listed in the file list.
   - The answer-key and leak checks must cover the new section; show this with a test.

6. **R-METHOD.** A new module `proofdeploy/evidence.py`.
   - The shown sources are: `"fixture"` → the fixture text; each touched file path → its full post-change text with changed line numbers; each included route file → its text. They must be exactly the text shown in the prompt; test that each shown source occurs verbatim in the rendered prompt.
   - Expose the shown sources from the bundle build (e.g. a function beside `build_author_prompt`, or a third return value). Every caller must be updated.
   - Checks and order exactly as R-METHOD item 2 (port `check.py` v2 logic: whitespace-normalised matching; "self-justifying" = the quote is absent from the source text with the changed lines removed but present in the full text; booleans as words, case-insensitive; a quote containing both → ambiguous; status, object and list values need no literal).
   - **Correction round** in `Orchestrator._author_probes`, so it covers both measured arms and `verify`:
     - the follow-up prompt is built exactly as R-METHOD item 3;
     - message wording = `check.correction_message` v2 (copy its text);
     - at most one correction;
     - item 4 handling (drop rejected assertions; a probe with none left is an invalid block; a correction-call transport failure is retried once after 60 s, else the first answer is used minus its rejections, recorded).
   - **Schema** (`proofdeploy/probe.py`): `evidence` allowed on assertions; `_check_no_code_probe` must skip `evidence` values (a quoted `import os` must not reject a probe; test it).
   - **Record:** both raw answers, the correction prompt sha256, `correction_used`, per-assertion check results for r1 and r2, finish_reason and usage for both. `SCHEMA_VERSION` → `1.9.0`, with a comment in the existing style; older records still load.

7. **Pins doc.** `docs/registration/author-prompt-v8.md`, listing:
   - the template path and sha256;
   - the new `model_client.py` sha256;
   - the skill hash;
   - "registered by Master, D242".

   Update every test that asserts v7 or the old hashes.

8. **Tests.** Many existing tests use stub model outputs without `evidence`; they will now hit the correction round. Update their stub outputs to carry valid evidence (keep each test's intent). Do not weaken any assertion about scoring.

   New tests, each with a plant that makes it FAIL (raw output in the report):
   - (a) header-equality render;
   - (b) changed markers on exact lines (plant: mark by string match → fail);
   - (c) each rejection reason, including a quote spanning a changed line, the both-values boolean and `False` in a Python source;
   - (d) a correction round that fixes the answer → the probe runs; a correction that doesn't → the assertion is dropped, and with no assertions left the block is invalid;
   - (e) the correction-call transport retry;
   - (f) the `evidence` code-scan exemption;
   - (g) route selection on a synthetic Python and a synthetic Node tree (scope, exclusions, order, cap, "shown above");
   - (h) at most one correction call (plant: allow two → fail);
   - (i) the golden express and python bug units with a stub returning a correct-direction answer → `candidate_catch`; with a wrong-direction answer citing the changed line → rejected, not caught.

   Run the affected suites, `ruff`, `mypy` on changed files, and `python scripts/check_test_manifest.py --write` then the check (OK). Assign new test files to a ci.yml shard.

9. **Render check (no model).** A script or test output for golden node-express-sqlite bug and for paperless at B (`7dca0bfa`, contract app.path `src`). Show: the route section (paperless must include `src/paperless/urls.py`), the marked lines, the prompt size in characters, and the header-equality result.

## Push and PR
- Commits authored `builder-sonnet (D237)`, with the trailers from CLAUDE.md.
- Push only `feature/o21-recipe-v8`. Open the PR with `gh api -X POST repos/wally-dk24/proofdeploy/pulls -f title=... -f head=feature/o21-recipe-v8 -f base=main -f body=...`; the body ends with the Claude Code line.
- Do not merge. Do not dispatch any workflow. Do not call the model.

## Report back
- Branch, head SHA (ls-remote), merge-base, PR number.
- `git diff --stat origin/main...HEAD`.
- Every new sha256: the three docs, the template, `model_client.py`, the skill author copy, `skill.md`.
- The generator byte-check output.
- Raw output for each test and plant.
- The render-check output.
- Every file you were unsure about or deviated on.
