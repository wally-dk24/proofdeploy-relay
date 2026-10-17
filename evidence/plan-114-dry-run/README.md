# Plan 1.14 rendered-text dry run — evidence pack

All three checkpoint repos through the MEASURED path, stopping
before the model call (reviewer's order after train 3 and the
Ghost re-proof). Code under test: proofdeploy main
`98e9c21dad1dcddeebf23d5351d0ec471f7f631b` (executed from the
worktree `~/workspace/proofdeploy-dryrun` at that commit).

**Result in one line: Directus 3/3 units and Vendure 4/4 units
completed the dry run with every prediction matched; both Ghost
units stopped at the answer-key check on a submodule (gitlink)
gap — with the rendered author text itself byte-equal to the
signed text in the assembled bundle. ZERO model requests were
made by any run.**

Predictions were committed BEFORE the first dispatch:
`predictions.md` in this directory, relay commit
`5b1a644a3abbdf865f925c5d42dc466ef864151f` (pushed
2026-10-17T00:55:15Z). First dispatch: 2026-10-17T00:57:54Z.
Last run finished 2026-10-17T01:12:59Z.

## Method (per D61; local runs, no GitHub run IDs exist)

Same shape as the WO-1.14 dry-run precedent
(evidence/wo114-dryrun/): this dev VM cannot pass the sandbox
measurement gate (no userns-remap), so each unit ran locally as
`proofdeploy measure --no-sandbox --dry-run-before-model` (the
exact CLI the `measure` workflow's measure step invokes) against
a blobless clone (`git clone --filter=blob:none --no-checkout`)
of the real repo, so the contract slug is derived from the real
origin URL. The pipeline's own stop is `--dry-run-before-model`:
the orchestrator assembles the author bundle, runs the answer-key
check, and returns the dry-run summary immediately before the
model call — no spend, no provisioning, no records. There are no
workflow artifacts or artifact zips for these runs; this pack is
the artifact (per-repo/per-unit `command.txt`, `stdout.txt`,
`stderr.txt`, `author-bundle.manifest.json`, plus
`runs.jsonl`). Arm: `--skill docs/evaluation/skill/skill-author.md`
(sha256 e42f12d71ea9f5ceee0f6d13b553f7cb13426954053db684ae4e2a6a8d73f5f9
= EXPECTED_SKILL_HASH). GROQ_API_KEY was unset in every run's
environment (`env -u GROQ_API_KEY` in each command.txt).

Bug-unit SHAs: B/fix/fix^ per readiness/manifest.json and the
orchestrator's Master-approved record (Addendum 65); base = B^
resolved in the target clones (Ghost 515c39b3abebe8d77941a6e7168ee74c0dce4933,
Directus c97d2ca57bd6a03b57601137aa4aacd675e22516, Vendure-1
a99500d5bf30a6a7dc17e88bb4d55253f6b1c59c, Vendure-2
842a7df0d8fecd1ec9070458b2b96eb1e8627b67). Every fix^ was
re-verified as the fix commit's first parent in the clones.

## Per-unit results

Signed renders: Ghost
ab955a3081fc9d293d1b84fd257299069c0340c59037a5a8aa09f6ef11afe7f0
(918 B); Directus
dbbdcfd4b8287be91918d2c2aaade11a4c9c7b8496ac16f0f9409b1f1656b610
(1370 B); Vendure
ca9e5e07e310337aee0ff1822efdcfb2f68a02f68ad3237b5a83144cbd5618ae
(1390 B). Store contracts at this main: TryGhost__Ghost.yml
sha256 30788f8dbc99d529d6605e7b1fbc54300f8c8156f20fca0cf7e20a5feb4dbe37;
directus__directus.yml
b6bdd177d1e8c2483451ffe19d052d789183b13da8dec551da2539ebd6740a84;
vendure-ecommerce__vendure.yml
7d62949db5a4f66e8863b44b09d4d14f4e9f8e215d576d5c9a675eab47b8279f.

| unit | exit | contract_source (from the run's summary) | rendered sha256 vs signed |
|---|---|---|---|
| directus C3 | 0 | readiness/contracts/directus__directus.yml, b6bdd177… | dbbdcfd4… MATCH |
| directus C4 | 0 | readiness/contracts/directus__directus.yml, b6bdd177… | dbbdcfd4… MATCH |
| directus checkpoint-bug-directus-1 | 0 | readiness/contracts/directus__directus.yml, b6bdd177… | dbbdcfd4… MATCH |
| vendure C1 | 0 | readiness/contracts/vendure-ecommerce__vendure.yml, 7d62949d… | ca9e5e07… MATCH |
| vendure C2 | 0 | readiness/contracts/vendure-ecommerce__vendure.yml, 7d62949d… | ca9e5e07… MATCH |
| vendure checkpoint-bug-vendure-1 | 0 | readiness/contracts/vendure-ecommerce__vendure.yml, 7d62949d… | ca9e5e07… MATCH |
| vendure checkpoint-bug-vendure-2 | 0 | readiness/contracts/vendure-ecommerce__vendure.yml, 7d62949d… | ca9e5e07… MATCH |
| ghost C5 | 2 | not emitted (run stopped before the summary — see below) | bundle render ab955a30… MATCH (see below) |
| ghost checkpoint-bug-ghost-1 | 2 | not emitted (run stopped before the summary — see below) | bundle render ab955a30… MATCH (see below) |

Every exit-0 summary carries `dry_run: true` and
`stopped_before_model_call: true` (verbatim in the unit
`stdout.txt` files and in `runs.jsonl`).

## Ghost: what stopped the two runs (verbatim, both units)

```
proofdeploy: error: answer-key check: snapshot file list != git archive B: extra=[] missing=['ghost/core/content/themes/casper', 'ghost/core/content/themes/source']
```

(Identical line for C5 and for checkpoint-bug-ghost-1; each unit's
`stderr.txt`.) Exit code 2. No summary was printed, so prediction
1 (exit 0 + summary) is WRONG for both Ghost units, and is
reported wrong here — not re-run into compliance.

Cause, from the code at this main: the answer-key check
(`proofdeploy/leakcheck.py`, step 3) builds the expected snapshot
file list from `git ls-tree -r <tip>`, counting every non-symlink
entry as an expected file. Ghost's trees at both C5 and B carry
two git submodules — `ghost/core/content/themes/casper` and
`ghost/core/content/themes/source`, mode 160000 (raw ls-tree
output in `ghost/ls-tree-themes-at-C5.txt` and
`ghost/ls-tree-themes-at-B.txt`). The bundle snapshot is built by
`git archive` (`proofdeploy/fixture.py` create_snapshot), in
which gitlink entries arrive as EMPTY DIRECTORIES, so the two
paths are directories, not files, in the snapshot — hence
"missing". Submodule materialization
(`materialize_snapshot_submodules`, plan 1.2(l)/D46) is wired
into the two provisioning paths only (orchestrator.py lines 1572
and 1641), not into the author-bundle path, and the answer-key
check has no gitlink exclusion — so at this main, a measured run
against any unit whose tree carries submodules cannot pass the
answer-key check, on any host (the check is clone-independent:
ls-tree output for gitlinks comes from the tree object itself).

The render itself is NOT implicated: the check's snapshot step
(step 3) precedes its render comparison (step 5), and both Ghost
bundles were fully assembled before the check ran. The assembled
`fixture-description.txt` files are included in this pack
(`ghost/C5/fixture-description.txt`,
`ghost/checkpoint-bug-ghost-1/fixture-description.txt`); both
are 918 bytes with sha256
ab955a3081fc9d293d1b84fd257299069c0340c59037a5a8aa09f6ef11afe7f0
— the signed Ghost value. The render the measured path produced
for Ghost matches the signed text; what failed is the check's
snapshot comparison, one step before the dry-run stop.

## No-model evidence (all 9 units)

- The 7 completed runs' summaries state `dry_run: true` and
  `stopped_before_model_call: true`; the orchestrator returns
  that summary immediately before the model call.
- The 2 Ghost runs stopped even earlier — at the answer-key
  check, which precedes the model call in program order
  (orchestrator: bundle → `_check_answer_key` → dry-run return →
  `_author_probes`).
- `GROQ_API_KEY` was unset in every run environment, so no model
  client call was possible in any case.
- Every unit's records directory holds ZERO commits (an
  initialized but empty git repo — the dry-run and error paths
  write no evidence or score records), and every unit's workdir
  contains only the bundle artifacts (`author-bundle/`,
  `author-bundle.manifest.json`, `diff.patch`, `snapshot-tip/`):
  no provisioning directory, no model output, nothing executed.

## Redactions

None. The captured outputs were scanned for credential material
(Groq key patterns, bearer tokens, api-key/password/secret
strings): no matches. No credential was present in any run's
environment (GROQ_API_KEY unset). Files: 43 in this pack
(40 per-unit outputs, predictions.md, runs.jsonl, this README),
all < 1 MB, no trims.
