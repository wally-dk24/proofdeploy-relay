# WO-6 Phase C — predictions (D149)

One line per candidate, committed and pushed BEFORE that candidate's
first start attempt. Provisioning-only readiness through the real
sandboxed runner (`readiness.yml` dispatch, attempt=2 pass: each side
once, then every non-READY side once more). No model, no probes.

- paperless-ngx/paperless-ngx @0cb2cdbfdd91a2078236f8851cd310261284591c (survivor #1, 46,745 stars, app_path "src"): expect NOT READY — the frozen `uv sync --frozen` of the walk's heaviest lock (torch, scikit-learn, sentence-transformers, tantivy) under the selected Python 3.14 image is the likeliest stopper (a locked package without a cp314 wheel falls back to a source build), with Django startup checks second; the service blockers (redis broker, database) are configured away via the app's own sqlite + in-memory settings from its HEAD config.
