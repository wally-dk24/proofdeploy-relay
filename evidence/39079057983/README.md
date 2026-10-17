# D61 evidence pack — D195 A2 hand check (d) for WO-6 selection candidate 9

- **Run ID:** 39079057983 (conclusion: success) — https://github.com/wally-dk24/proofdeploy/actions/runs/39079057983
- **Workflow:** readiness.yml (mode provision, attempt 1), dispatched BY HAND per D195 A2 after the reviewer's D198 OK on the pair branch.
- **Dispatch ref:** `feat/d195-readiness-pair-inputs` @ head `bfae84394edb46f2500506ed60fd0621a0e9aeb7`
- **Merge-base of the dispatch ref with main (D155):** `63fdb628015395ce224ad38030c734c59a1b8f3a` (the branch is based directly on that main tip)
- **Inputs:** repo `paperless-ngx/paperless-ngx`; fix_sha `371c4f57d3ab208fc261e159fa86ca1dc80cb334`; fix_parent_sha `5569447ea00c50de8c0c1c75136418bb3343dad6` (the fix's first parent, read from the raw commit object in the stopped selection run's local app clone)
- **Unit:** WO6-BUGSEL-371c4f57 (synthetic pair unit, roles fix^ and fix)
- **Artifact:** `readiness-paperless-ngx-paperless-ngx-1` (artifact id 12118655808), zip sha256 as downloaded: `dc5ff51686c39d6c6eff6249ae879b5e3c48e7e2220da4f5c737ee8d3662b897`

## Result — BOTH SIDES READY (first dispatch; no re-dispatch needed)

| side | registered sha | status | typed_reason | engine | isolation_mode | network_mode | contract_store_sha256 |
|---|---|---|---|---|---|---|---|
| fix^ | 5569447ea00c50de8c0c1c75136418bb3343dad6 | READY | null | podman podman version 4.9.3 | userns-remap | none+socket | eb60559b9fdd879e5cb8ca30d16c7cbfb1cea356e9385b8e367a249ab7c19e6a |
| fix | 371c4f57d3ab208fc261e159fa86ca1dc80cb334 | READY | null | podman podman version 4.9.3 | userns-remap | none+socket | eb60559b9fdd879e5cb8ca30d16c7cbfb1cea356e9385b8e367a249ab7c19e6a |

Both rows also carry `fixture_render_sha256` `17edd5611e866bfb824aff6b3693671fc106cd75bd49e2d5db8fac47203c26db` (the D164-final paperless fixture render) and identical `install_plan_sha256` `1dadc6a2a10259ec4bbbe46ee78e7c242f8dfd3086ac7b4ed3f25d354a8c6261`.

## Files

- `rows-attempt1.json` — the rows artifact file, byte-identical as produced by the run.
- `pd-log-fix-parent-decisive.txt`, `pd-log-fix-decisive.txt` — decisive provisioning lines from each side's pd-log (migrations header, first applications, the login-page 200, and the closing "sandbox app READY" line), with trims marked.

## Redactions (D61)

- The harness redacts the generated admin credentials in its own logs (`PAPERLESS_ADMIN_USER=***REDACTED***`, `PAPERLESS_ADMIN_PASSWORD=<redacted>`, as produced upstream).
- The excerpts omit the sandbox command lines that carry environment values. The only literal env value on those lines, `PAPERLESS_SECRET_KEY=wo6-phase-c-readiness-throwaway`, is the registered contract's fixed throwaway string, not a per-run secret.
- The rows JSON contains no credentials. Nothing else required redaction.
