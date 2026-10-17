# WO-6 Phase C — predictions (D149)

One line per candidate, committed and pushed BEFORE that candidate's
first start attempt. Provisioning-only readiness through the real
sandboxed runner (`readiness.yml` dispatch, attempt=2 pass: each side
once, then every non-READY side once more). No model, no probes.

- paperless-ngx/paperless-ngx @0cb2cdbfdd91a2078236f8851cd310261284591c (survivor #1, 46,745 stars, app_path "src"): expect NOT READY — the frozen `uv sync --frozen` of the walk's heaviest lock (torch, scikit-learn, sentence-transformers, tantivy) under the selected Python 3.14 image is the likeliest stopper (a locked package without a cp314 wheel falls back to a source build), with Django startup checks second; the service blockers (redis broker, database) are configured away via the app's own sqlite + in-memory settings from its HEAD config.
- paperless-ngx/paperless-ngx ROUND 2 (D151 folder-step fix; D152 fix budget round 2 of 3): expect READY — round 1's frozen install already completed in full on this image and its only stopper was the migrate-step system check (PAPERLESS_CONSUMPTION_DIR /checkout/consume and PAPERLESS_MEDIA_ROOT /checkout/media missing); the D151 build step now creates data/media/consume before migrate, so install + migrate + start should proceed and the readiness poll should see the app's login redirect.
