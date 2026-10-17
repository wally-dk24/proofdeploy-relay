# Plan 1.14 dry run — Ghost re-run after D143: predictions, recorded BEFORE the first dispatch

Run: re-run of the two Ghost units of the plan 1.14 rendered-text
dry run, from the merged D143 fix (reviewer's order: PR #136
merged under D139; then re-run Ghost C5 and
checkpoint-bug-ghost-1 from the merged main, same method as the
7 units that passed). The first dry run's pack is
`evidence/plan-114-dry-run/` (relay commits 9bb54e5 + dcb74ec);
this pack is its sibling, `evidence/plan-114-dry-run-ghost-rerun/`.

Code under test: proofdeploy main
`6dc1a4df3230578c88549a05f93070d1ab487f60` ("Merge pull request
#136 from wally-dk24/fix/d143-leakcheck-gitlinks"; post-merge CI
green). The runs execute from the worktree
`~/workspace/proofdeploy-dryrun` checked out at that exact
commit, so its `readiness/contracts/` files are the store the
runs load.

Method: identical to the first dry run (and to the WO-1.14
precedent, evidence/wo114-dryrun). This dev VM cannot pass the
sandbox measurement gate (no userns-remap), so each run is
LOCAL: `proofdeploy measure --no-sandbox --dry-run-before-model`
(the exact CLI the `measure` workflow's measure step invokes)
against the blobless clone of the real repo at
`/home/hatch/plan114-dryrun-targets/ghost` (origin
`https://github.com/TryGhost/Ghost.git`, so the contract slug is
derived from the real origin URL). The pipeline's own stop is
`--dry-run-before-model`: the orchestrator assembles the author
bundle, runs the answer-key check, and returns the dry-run
summary immediately before the model call — no spend, no
provisioning, no records. GROQ_API_KEY is unset in every run's
environment (`env -u GROQ_API_KEY`). Arm: `--skill
docs/evaluation/skill/skill-author.md` (sha256
e42f12d71ea9f5ceee0f6d13b553f7cb13426954053db684ae4e2a6a8d73f5f9,
= the registered EXPECTED_SKILL_HASH; verified in the tree at
6dc1a4d).

Units (registered checkpoint units, readiness/manifest.json;
bug-unit SHAs per the orchestrator's Master-approved record,
Addendum 65 — the same unit definitions as the first run):

- C5 (checkpoint-control, clean unit): C
  5d482965b7f5ed05ffef956d0eaed8b8a51af0ef. Snapshot tip = C.
- checkpoint-bug-ghost-1: base
  515c39b3abebe8d77941a6e7168ee74c0dce4933 (= B^), B
  1de328d8203cbfc81beadfea966982b049293fa2, fix^
  93e0084723cdaf22d656bcd4811b87433e6b3875, fix
  f67640b2c18c76abd9cd3f6d19d6ae93327b42fa. Snapshot tip = B.

Gitlink pins, derived BEFORE dispatch from `git ls-tree` in the
Ghost clone at each unit's snapshot tip (raw output ships in
this pack as `ls-tree-themes-at-C5.txt` and
`ls-tree-themes-at-B.txt`). The two units do NOT share pins:

- C5 (tip 5d482965):
  - ghost/core/content/themes/casper =
    7f4adb690c40850382fed4312d90e21ff0313fdc (mode 160000)
  - ghost/core/content/themes/source =
    219dc7dbe62bb6dfa8db9fd651528571eae6cfd3 (mode 160000)
- checkpoint-bug-ghost-1 (tip B 1de328d8):
  - ghost/core/content/themes/casper =
    bb3d0de3c7065677bb18b38d8185a8a980b163bd (mode 160000)
  - ghost/core/content/themes/source =
    3a5643b42a4700e961e65ebda92f6a6c56bf8c86 (mode 160000)

Contract store at merged main 6dc1a4d (sha256 of the stored
file, verified in the worktree at that commit — the file is
unchanged by PR #136):

- readiness/contracts/TryGhost__Ghost.yml —
  30788f8dbc99d529d6605e7b1fbc54300f8c8156f20fca0cf7e20a5feb4dbe37

Signed rendered author text (Master-signed):

- Ghost: ab955a3081fc9d293d1b84fd257299069c0340c59037a5a8aa09f6ef11afe7f0
  (918 bytes)

## Predictions

For EACH of the two units, the dry run will:

1. Exit 0 and print a summary with `dry_run: true` and
   `stopped_before_model_call: true`, with NO evidence or score
   records written (the dry-run path writes none). This is the
   point of the D143 fix: in the first run both units stopped at
   the answer-key check with exit 2 and no summary, because
   leakcheck step 3 counted the two gitlinks as expected files.
2. Report `contract_source.store_path` =
   `readiness/contracts/TryGhost__Ghost.yml` and
   `contract_source.sha256` =
   30788f8dbc99d529d6605e7b1fbc54300f8c8156f20fca0cf7e20a5feb4dbe37.
3. Report `rendered_fixture_sha256` = the SIGNED Ghost value
   ab955a3081fc9d293d1b84fd257299069c0340c59037a5a8aa09f6ef11afe7f0.
4. Carry, in its bundle manifest, an `omitted_gitlinks` record
   of exactly two entries — the manifest serializes it as a
   list of {"path", "commit"} objects sorted by path — equal to
   the unit's pins derived above:
   - C5: [{"path": "ghost/core/content/themes/casper",
     "commit": "7f4adb690c40850382fed4312d90e21ff0313fdc"},
     {"path": "ghost/core/content/themes/source",
     "commit": "219dc7dbe62bb6dfa8db9fd651528571eae6cfd3"}]
   - checkpoint-bug-ghost-1:
     [{"path": "ghost/core/content/themes/casper",
     "commit": "bb3d0de3c7065677bb18b38d8185a8a980b163bd"},
     {"path": "ghost/core/content/themes/source",
     "commit": "3a5643b42a4700e961e65ebda92f6a6c56bf8c86"}]
   Nothing at or under a gitlink path is in the snapshot (any
   such file would be `extra` and refused), and the author's
   file list excludes the gitlink paths.
5. Make ZERO model requests: the run stops immediately before
   the model call (the summary is produced before
   `_author_probes` can run), no provisioning occurs, and
   GROQ_API_KEY is absent from the environment, so no model
   client call is possible. Each unit's records directory will
   hold ZERO commits and its workdir only bundle artifacts
   (`author-bundle/`, `author-bundle.manifest.json`,
   `diff.patch`, `snapshot-tip/`).

If any run instead exits non-zero, renders a different sha256,
records a different contract_source, records different
omitted_gitlinks, writes records, provisions, or calls the
model, the corresponding prediction is WRONG and will be
reported as wrong in the pack README — not re-run into
compliance.
