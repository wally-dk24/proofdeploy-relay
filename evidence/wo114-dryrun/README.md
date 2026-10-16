# WO-1.14 Part 1 dry-run pack (D103)

**What this is:** the dry-run proof for WO-1.14 Part 1 (PR
https://github.com/wally-dk24/proofdeploy/pull/126, head
`fdb89c1`). Per checkpoint repo, a measured-path run with
`--dry-run-before-model` that must stop before the model call (no
spend).

**Predictions first:** `predictions.md` in this directory was
committed to this repo at `c913189` (2026-10-16T17:29:19Z),
BEFORE the first dry run executed. Read it first; the results
below are scored against it.

**Local runs, stated plainly (D61):** these runs executed
LOCALLY, not in Actions. This dev VM cannot pass the sandbox
measurement gate (no userns-remap), so each run used
`--no-sandbox`; every run stopped at contract load — before any
snapshot, bundle, provisioning, or model call — so no untrusted
code executed and the records carry `sandboxed: false` /
`measured_result: false` by construction. The code tree the runs
executed from is the PR branch tree at head `fdb89c1` (its
`readiness/contracts/` files are the store the runs loaded).
Targets were real blobless clones (`git clone
--filter=blob:none --no-checkout`) of the three repos, so each
target's origin is its real GitHub URL and the contract slug was
derived from it exactly as the measured workflow derives it.
Full command lines: per-repo `command.txt`.

## Results vs predictions — all three predictions CORRECT

Every run: exit code 0; summary verdict `inconclusive`,
`reason_class` "our gap", `cause` `contract_unrenderable`,
`measured_result` false. The evidence record's
`inconclusive_error` names the store path and the missing
fixture fields and ends with `(our gap)`; the score record's
note is exactly `our gap: contract_unrenderable`. No bundle,
no bundle manifest, no rendered sha256, no model call — the
refusal fires at contract load, earlier than the dry-run stop
(the stop itself is exercised by the PR's tests on a contract
that renders). Work dirs contain only the empty unit dir.

| repo (unit) | template the error proves | missing fields (verbatim from the record) | contract_source sha256 (= predicted) |
|---|---|---|---|
| vendure-ecommerce/vendure (C1) | fixture_template_nocredential.txt (no `auth:`) | repo_name, seed_note, auth_mechanism, flags_note | 09eb52e47ab14e373e1916db3b10221d8481f7e493ad71853bedf86eed6f195d |
| directus/directus (C3) | fixture_template.txt (its `auth:` selects the credential template) | repo_name, seed_note, auth_mechanism, auth_scope, flags_note | db6c5f72bf7601e6c1e43e2360c15e56e3ab975919d052d5720573fe41b2f194 |
| TryGhost/Ghost (C5) | fixture_template_nocredential.txt (no `auth:`) | repo_name, seed_note, auth_mechanism, flags_note | 6649dbf299ac6cfd643651f3177a81ba5c424dfd9f99d21fb992db5f9c6ff580 |

Records are schema 1.6.0 and read back through the engine's own
`read_records`.

## Files and sha256

```
2725a16a4e7539a495137aaa317097178e3a44ae1d1ff867c1fdb83087451209  vendure/command.txt
69e526b5d09843ab3e303b22196dfd04f2fb99ea04b7743fab0bbc697a5f2b59  vendure/runs.jsonl
e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855  vendure/stderr.txt (empty)
0a6d6f6117377cf92a19b81a629a88a0bb13f5aa77e66c831e644eb310e8197b  vendure/stdout.txt
a74bf2b50f06ffa518ff0f4c3dc43d8bd9e483ee43300f60875686435f868998  directus/command.txt
c9686a34f191edc1449c258ac795bf06ab2cb2efdd7810c9deaf1d4ef76fb025  directus/runs.jsonl
e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855  directus/stderr.txt (empty)
9674570d6afa9ee19bb9e552a077c4490b39d9d324b7e24f78291781910e583c  directus/stdout.txt
c3d4a5f180f54fdcae135835dd93dd8692f70551d66464823c2363c0f8a7523b  ghost/command.txt
f9344a340a67a05ab9ab515f464383b9494fa9664783adeed96eaaa2b7439bc5  ghost/runs.jsonl
e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855  ghost/stderr.txt (empty)
dfd6d31c48d4bce742be6df94789d5884de23724e9f5b9fcacf368b813f614f1  ghost/stdout.txt
b3bb9a95e34f4236b8e4021ee26878ca453f2f95c2a809e872d1682c600a7683  predictions.md
```

## Scrub

Scanned all run outputs for credential-shaped strings
(api key / password / secret / bearer / Groq key prefix): none
present. Zero redactions applied — the records contain only the
refusal text, the store paths, and their sha256s. Each file is
well under 1 MB (largest: runs.jsonl at 2,753 bytes); nothing
trimmed.
