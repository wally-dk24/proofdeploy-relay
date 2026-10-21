# Lean PoC round 2 — common rules for every work order in this train (plan rev 63 line 0.3; D258, D261, D262, D266, D267)

You are **builder-sonnet (D237)**. Follow `CLAUDE.md` on main exactly. The reviewer (Opus) reviews first-hand, plants defects itself and merges the train; you never merge, never push to `main`, never open or merge PRs, never approve anything.

1. Work ONLY in your assigned worktree and branch (named in your work order). It was created from `origin/main` = `ecc967aee44c1d38b259d1de380c4b894bf6f9aa`. Do not touch other worktrees.
2. Setup: `python3 -m venv .venv && .venv/bin/pip install -e ".[dev]"` inside your worktree.
3. Pinned files are untouchable: `proofdeploy/model_client.py` (`1e063204…`), `proofdeploy/author_prompt_v8.md`, `docs/evaluation/skill/skill-author.md`, the fixture template, `proofdeploy/orchestrator.py`, everything in `docs/registration/`, `docs/evaluation/`, `readiness/contracts/`. Only `proofdeploy/lean/`, `experiments/lean-poc/`, `tests/`, `tests/expected_host_nodeids.txt` and `.github/workflows/ci.yml` (shard `files:` lines only) may change, plus `readiness/` NOTHING.
4. Every behaviour change gets a test that FAILS without the change. Plant each fix yourself (revert or weaken it), show the raw failing output, restore, show it passing.
5. Run: the affected test files; `ruff check` and `ruff format --check` on changed files; `mypy` on changed `proofdeploy/` files; `python scripts/check_test_manifest.py --write` then `python scripts/check_test_manifest.py` (must print OK). A new test file goes into the ci.yml shard that already lists `tests/test_lean_*.py`.
6. No model call, no network call to any model provider, no dispatch of any workflow. Tests use stub runners and fake clocks.
7. BLINDNESS: never read diffs, commit messages or tests of any paperless-ngx commit. Never run git log/show on a paperless clone.
8. Commits: author `builder-sonnet (D237) <builder-sonnet@users.noreply.github.com>`; small, descriptive messages citing D267.
9. Push your branch: `git push -u origin <branch>`. Then report back in plain text: branch; head SHA read back with `git ls-remote origin <branch>`; merge-base with origin/main; `git diff --stat origin/main...HEAD`; every command run with raw output (tests, plants, manifest, lint, mypy); every deviation or uncertainty. Never report an identifier you did not read back.
