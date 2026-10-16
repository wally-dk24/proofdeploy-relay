# WO-1.14 dry-run predictions — recorded BEFORE the first dry run

Run: WO-1.14 Part 1 (D103) dry-run proof, per checkpoint repo.
Code under test: proofdeploy PR branch `feat/wo114-contract-store`
(head SHA recorded in the pack README when the runs execute; the
runs execute from that exact tree, so its `readiness/contracts/`
files are the store the runs load).
Method: LOCAL runs. This dev VM cannot pass the sandbox
measurement gate (no userns-remap), so each run is
`proofdeploy measure --no-sandbox --dry-run-before-model` against a
target checkout whose origin is the repo's GitHub URL. The
predictions below say every run stops at contract load — before
any snapshot, bundle, provisioning, or model call — so nothing
untrusted executes and no model spend occurs. Records from a
refusal carry `sandboxed: false` / `measured_result: false` by
construction. Target checkouts are blobless clones
(`git clone --filter=blob:none --no-checkout`) of the real repos;
the registered clean SHAs are never resolved locally because the
refusal precedes rev resolution.

Per-repo contract facts (checked against the store at the PR
branch's base, main 3eab21a, before writing these predictions):

- vendure-ecommerce/vendure — `readiness/contracts/vendure-ecommerce__vendure.yml`,
  sha256 09eb52e47ab14e373e1916db3b10221d8481f7e493ad71853bedf86eed6f195d.
  No `auth:` block, no `fixture:` block.
- directus/directus — `readiness/contracts/directus__directus.yml`,
  sha256 db6c5f72bf7601e6c1e43e2360c15e56e3ab975919d052d5720573fe41b2f194.
  `auth:` present (refresh provider), no `fixture:` block.
- TryGhost/Ghost — `readiness/contracts/TryGhost__Ghost.yml`,
  sha256 6649dbf299ac6cfd643651f3177a81ba5c424dfd9f99d21fb992db5f9c6ff580.
  No `auth:` block, no `fixture:` block (the fixture text lives on
  PR #121, not on main).

## Predictions (identical shape for all three repos)

For each repo, the dry run (clean unit: Vendure C1
4cbbc790118786a17721954c3a4d739810bd2632; Directus C3
dc922b790a15949e8abfce9c1f4523d4e5f89492; Ghost C5
5d482965b7f5ed05ffef956d0eaed8b8a51af0ef):

1. The run produces the TYPED REFUSAL WITH A RECORD from WO-1.14
   item 4 — cause `contract_unrenderable` — and exits 0 (a summary
   was returned and recorded). It does NOT produce a bundle, a
   bundle manifest, or a rendered sha256, and it makes NO model
   call: the refusal fires at contract load, which is earlier than
   the `--dry-run-before-model` stop. A "successful render" is
   impossible for all three: none of the three store contracts
   carries a `fixture:` block, so the renderer's required fields
   are all missing.
2. The evidence record's `inconclusive_error` names the store path
   and the missing fields, and ends with `(our gap)`:
   - Vendure: template `fixture_template_nocredential.txt`
     (no `auth:`), missing fields: repo_name, seed_note,
     auth_mechanism, flags_note.
   - Directus: template `fixture_template.txt` (its `auth:`
     selects the credential template), missing fields: repo_name,
     seed_note, auth_mechanism, auth_scope, flags_note.
   - Ghost: template `fixture_template_nocredential.txt`
     (no `auth:`), missing fields: repo_name, seed_note,
     auth_mechanism, flags_note.
3. The evidence record's `contract_source` is
   `{store_path: "readiness/contracts/<owner>__<repo>.yml",
   sha256: <the store file sha256 listed above>}`, its
   `measured_result` is false, and the score record's note is
   exactly `our gap: contract_unrenderable`. Records validate
   against the committed result-record schema (1.6.0).
4. The `--dry-run-before-model` stop itself (bundle assembled,
   answer key checked, stop before the model call, rendered
   sha256 + bundle manifest reported, no records) is exercised by
   the PR's tests on a contract that DOES render
   (`test_dry_run_stops_before_the_model_call`); it is predicted
   NOT to be reached by any of these three runs, per prediction 1.

If any run instead renders, bundles, provisions, or calls the
model, the corresponding prediction is WRONG and will be reported
as wrong in the pack README — not re-run into compliance.
