# Plan 1.14 rendered-text dry run — predictions, recorded BEFORE the first dispatch

Run: plan 1.14 dry run of all three checkpoint repos through the
MEASURED path, stopping before the model call (reviewer's order
after train 3 and the Ghost re-proof).
Code under test: proofdeploy main
`98e9c21dad1dcddeebf23d5351d0ec471f7f631b` (the runs execute from
that exact tree, so its `readiness/contracts/` files are the store
the runs load).
Method: LOCAL runs, same shape as the WO-1.14 dry-run precedent
(evidence/wo114-dryrun). This dev VM cannot pass the sandbox
measurement gate (no userns-remap), so each run is
`proofdeploy measure --no-sandbox --dry-run-before-model` against a
blobless clone (`git clone --filter=blob:none --no-checkout`) of
the real repo, whose origin is the repo's real GitHub URL so the
contract slug is derived exactly as the measured workflow derives
it. `--no-sandbox` runs are marked unsandboxed and never count as
measured results; the dry-run stop fires after bundle assembly and
the answer-key check, before any provisioning or model call, so no
untrusted code executes. GROQ_API_KEY is not set in the run
environment. Arm: `--skill docs/evaluation/skill/skill-author.md`
(sha256 e42f12d71ea9f5ceee0f6d13b553f7cb13426954053db684ae4e2a6a8d73f5f9,
= the registered EXPECTED_SKILL_HASH).

Units (registered checkpoint units, readiness/manifest.json at
main 98e9c21d; bug-commit SHAs from the orchestrator's
Master-approved record, Addendum 65):

- TryGhost/Ghost
  - C5 (checkpoint-control): C 5d482965b7f5ed05ffef956d0eaed8b8a51af0ef
  - checkpoint-bug-ghost-1: B 1de328d8203cbfc81beadfea966982b049293fa2
    (base = B^, resolved in the target clone), fix^
    93e0084723cdaf22d656bcd4811b87433e6b3875, fix
    f67640b2c18c76abd9cd3f6d19d6ae93327b42fa
- directus/directus
  - C3 (checkpoint-control): C dc922b790a15949e8abfce9c1f4523d4e5f89492
  - C4 (checkpoint-control): C a5da59da94bb33c8d644df93a21403eca4c1a4e7
  - checkpoint-bug-directus-1: B beef5488550c27f135c3679b0fbc2752dff98852
    (base = B^), fix^ 7e9b03504c6094bac8dace713c11a6f909016057, fix
    93a3f39342e171d3ae3017c2aa9a744b5e1c3f8a
- vendure-ecommerce/vendure
  - C1 (checkpoint-control): C 4cbbc790118786a17721954c3a4d739810bd2632
  - C2 (checkpoint-control): C b19ab4dc76e4f8521e02258b8afa26f615142595
  - checkpoint-bug-vendure-1: B 44bad119bcbce87465f7eb67c143408037e75928
    (base = B^), fix^ 859619c4c289f7773619704f9cd1e5c1256dcfce, fix
    4393199c077e5cd9399696d25b69c620b36c5cc2
  - checkpoint-bug-vendure-2: B c4e0d9928a426773f54940953094eb322ec20a3d
    (base = B^), fix^ 8fb22a8d882bb8b8cb2828c19f91e7c7d58f8aff, fix
    02c9d81e83bebebe8b3384b2f789800b14ed0146

Contract store at main 98e9c21d (sha256 of the stored files):

- readiness/contracts/TryGhost__Ghost.yml —
  30788f8dbc99d529d6605e7b1fbc54300f8c8156f20fca0cf7e20a5feb4dbe37
- readiness/contracts/directus__directus.yml —
  b6bdd177d1e8c2483451ffe19d052d789183b13da8dec551da2539ebd6740a84
- readiness/contracts/vendure-ecommerce__vendure.yml —
  7d62949db5a4f66e8863b44b09d4d14f4e9f8e215d576d5c9a675eab47b8279f

Signed rendered author texts (Master-signed):

- Ghost: ab955a3081fc9d293d1b84fd257299069c0340c59037a5a8aa09f6ef11afe7f0 (918 bytes)
- Directus: dbbdcfd4b8287be91918d2c2aaade11a4c9c7b8496ac16f0f9409b1f1656b610 (1370 bytes)
- Vendure: ca9e5e07e310337aee0ff1822efdcfb2f68a02f68ad3237b5a83144cbd5618ae (1390 bytes)

## Predictions

For EVERY unit above (all 9: 5 controls run as clean units, 4 bug
units), the dry run will:

1. Exit 0 and print a summary with `dry_run: true` and
   `stopped_before_model_call: true`, with NO evidence or score
   records written (the dry-run path writes none).
2. Report `contract_source.store_path` =
   `readiness/contracts/<owner>__<repo>.yml` for the unit's repo
   and `contract_source.sha256` = the store file sha256 listed
   above for that repo — recorded per unit, identical for every
   unit and side of the repo (REG-E item 1: one contract serves
   the author bundle and every side).
3. Report `rendered_fixture_sha256` = that repo's SIGNED value
   above (Ghost ab955a30…, Directus dbbdcfd4…, Vendure ca9e5e07…),
   for every unit and side — the render is a function of the
   unit's one store contract alone.
4. Make ZERO model requests: the run stops immediately before the
   model call (the summary is the dry-run summary, produced before
   `_author_probes` can run), no provisioning occurs, and
   GROQ_API_KEY is absent from the environment, so no model client
   call is possible.

If any run instead renders a different sha256, records a different
contract_source, writes records, provisions, or calls the model,
the corresponding prediction is WRONG and will be reported as
wrong in the pack README — not re-run into compliance.
