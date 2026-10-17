# Plan 1.14 rendered-text dry run — Ghost re-run after D143 (evidence pack)

The two Ghost units of the plan 1.14 dry run, re-run from the
merged D143 fix (reviewer's order: PR #136 merged under D139,
then re-run Ghost C5 and checkpoint-bug-ghost-1 from the merged
main, same method as the 7 units that passed). Code under test:
proofdeploy main
`6dc1a4df3230578c88549a05f93070d1ab487f60` (executed from the
worktree `~/workspace/proofdeploy-dryrun` at that commit;
post-merge CI green).

**Result in one line: both Ghost units now complete the dry
run — exit 0, `dry_run: true`, `stopped_before_model_call:
true` — with the rendered author text byte-equal to the signed
text, the contract_source as predicted, and the bundle
manifests recording exactly the per-unit gitlink pins derived
before dispatch. ZERO model requests were made. Every
prediction matched. Together with the first pack
(`evidence/plan-114-dry-run/`: Directus 3/3, Vendure 4/4), the
plan 1.14 dry run now stands 9/9.**

Predictions were committed BEFORE the first dispatch:
`predictions.md` in this directory, relay commit
`f9741aede4c316de8508d33a87cea000ec4923af` (pushed
2026-10-17T02:19:34Z). First dispatch: 2026-10-17T02:19:42Z.
Last run finished 2026-10-17T02:24:14Z.

## Method (per D61; local runs, no GitHub run IDs exist)

Identical to the first dry run (and to the WO-1.14 precedent,
evidence/wo114-dryrun/): this dev VM cannot pass the sandbox
measurement gate (no userns-remap), so each unit ran locally as
`proofdeploy measure --no-sandbox --dry-run-before-model` (the
exact CLI the `measure` workflow's measure step invokes)
against the blobless clone of the real repo at
`/home/hatch/plan114-dryrun-targets/ghost` (origin
`https://github.com/TryGhost/Ghost.git`, so the contract slug
is derived from the real origin URL). The pipeline's own stop
is `--dry-run-before-model`: the orchestrator assembles the
author bundle, runs the answer-key check, and returns the
dry-run summary immediately before the model call — no spend,
no provisioning, no records. There are no workflow artifacts
or artifact zips for these runs; this pack is the artifact
(per-unit `command.txt`, `stdout.txt`, `stderr.txt`,
`author-bundle.manifest.json`, plus `runs.jsonl` and the
ls-tree evidence). Arm: `--skill
docs/evaluation/skill/skill-author.md` (sha256
e42f12d71ea9f5ceee0f6d13b553f7cb13426954053db684ae4e2a6a8d73f5f9
= EXPECTED_SKILL_HASH). GROQ_API_KEY was unset in every run's
environment (`env -u GROQ_API_KEY` in each command.txt).

Unit definitions (same as the first run; readiness/manifest.json
+ Addendum 65): C5 clean at
5d482965b7f5ed05ffef956d0eaed8b8a51af0ef (snapshot tip = C);
checkpoint-bug-ghost-1 with base
515c39b3abebe8d77941a6e7168ee74c0dce4933 (= B^), B
1de328d8203cbfc81beadfea966982b049293fa2 (snapshot tip = B),
fix f67640b2c18c76abd9cd3f6d19d6ae93327b42fa.

## Per-unit results vs predictions

Signed render (Ghost):
ab955a3081fc9d293d1b84fd257299069c0340c59037a5a8aa09f6ef11afe7f0
(918 B). Store contract at merged main 6dc1a4d (verified in the
worktree at that commit; the file is unchanged by PR #136):
readiness/contracts/TryGhost__Ghost.yml sha256
30788f8dbc99d529d6605e7b1fbc54300f8c8156f20fca0cf7e20a5feb4dbe37.

| unit | exit | dry_run / stopped_before_model_call | contract_source | rendered sha256 vs signed | bundle_manifest_sha256 |
|---|---|---|---|---|---|
| ghost C5 | 0 | true / true | readiness/contracts/TryGhost__Ghost.yml, 30788f8d… | ab955a30… MATCH | cada0c3b38b97aa77b88ae329d925768200588cb199c0386f61c6acec6912181 |
| ghost checkpoint-bug-ghost-1 | 0 | true / true | readiness/contracts/TryGhost__Ghost.yml, 30788f8d… | ab955a30… MATCH | f3b933f6af2c2cf160bf91c169d8209348c342cf31a642be89fb7d812f2f4a00 |

Both stderrs are empty (0 bytes). The standalone
`author-bundle.manifest.json` files in this pack hash to
exactly the summaries' `bundle_manifest_sha256` values, and
each bundle's own `fixture-description.txt` independently
hashes to ab955a30… (918 bytes) in both workdirs.

### omitted_gitlinks (verbatim from each unit's bundle manifest)

Derived before dispatch from `git ls-tree` in the Ghost clone
at each unit's snapshot tip (raw output in
`ls-tree-themes-at-C5.txt` / `ls-tree-themes-at-B.txt`); the
manifest record matches the derivation exactly, per unit — the
two units do NOT share pins:

C5 (tip 5d482965):

```json
[{"path": "ghost/core/content/themes/casper", "commit": "7f4adb690c40850382fed4312d90e21ff0313fdc"}, {"path": "ghost/core/content/themes/source", "commit": "219dc7dbe62bb6dfa8db9fd651528571eae6cfd3"}]
```

checkpoint-bug-ghost-1 (tip B 1de328d8):

```json
[{"path": "ghost/core/content/themes/casper", "commit": "bb3d0de3c7065677bb18b38d8185a8a980b163bd"}, {"path": "ghost/core/content/themes/source", "commit": "3a5643b42a4700e961e65ebda92f6a6c56bf8c86"}]
```

In both snapshots the two gitlink paths are present only as
EMPTY directories (the `git archive` representation): zero
files at or under either path in either snapshot-tip tree, so
nothing of the submodule contents reached the author, exactly
as D143 rules.

In the first run both units stopped at this point — the
answer-key check (leakcheck.py step 3) counted the gitlinks as
expected files and raised `snapshot file list != git archive
B: … missing=['ghost/core/content/themes/casper',
'ghost/core/content/themes/source']`, exit 2, no summary. With
D143 the check collects mode-160000 entries as expected
gitlinks, the assembler records them as `omitted_gitlinks`,
and step 5 cross-verifies the record against B — both units
pass the check and reach the dry-run stop.

## No-model evidence (both units)

- Both summaries state `dry_run: true` and
  `stopped_before_model_call: true`; the orchestrator returns
  that summary immediately before the model call.
- `GROQ_API_KEY` was unset in every run environment, so no
  model client call was possible in any case.
- Every unit's records directory holds ZERO commits (an
  initialized but empty git repo — the dry-run path writes no
  evidence or score records), and every unit's workdir contains
  only the bundle artifacts (`author-bundle/`,
  `author-bundle.manifest.json`, `diff.patch`,
  `snapshot-tip/`): no provisioning directory, no model
  output, nothing executed.

## Redactions

None. The captured outputs were scanned for credential material
(Groq key patterns, bearer tokens, api-key/password/secret
strings): no matches. No credential was present in any run's
environment (GROQ_API_KEY unset). Files: 13 in this pack
(predictions.md, README.md, runs.jsonl, 2 ls-tree evidence
files, 8 per-unit outputs), all < 1 MB, no trims.
