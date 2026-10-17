# Ghost auth RE-PROOF — predictions (post-train-3, generic driver)

Committed BEFORE any dispatch of the run below.

## Why a re-proof

Ghost's original auth proof (D96 era) ran under the old driver,
whose bare leg still acquired through the provider's proactive
ensure (the logged mint on the unauthenticated leg). Train 3
(main `98e9c21dad1dcddeebf23d5351d0ec471f7f631b`, merging PR #133
+ PR #134) landed the generic per-leg driver (D137): each
authenticated leg must show exactly 1 acquisition of the
registered kind, and the bare leg must show 0 acquisitions AND no
Authorization header, running through a credential-less copy of
the executor. Directus (run `38976234757`, PROVEN 4/4) and
Vendure (run `38978137844`, PROVEN 6/6) were proven under these
rules; Ghost is now shown under the same rules.

## Configuration under proof

- Workflow `readiness.yml`, `mode=auth-proof`,
  `repo=TryGhost/Ghost`, `attempt=1`, dispatched from main at
  `98e9c21dad1dcddeebf23d5351d0ec471f7f631b` (the merged generic
  driver; in-tree `readiness/auth_proof.py` PROOF_SPECS:
  `GET /ghost/api/admin/tags/`, unauthenticated status 403,
  acquisition kind `mint`, no body checks).
- Committed Ghost contract render, recomputed locally from this
  exact tree via the harness's own `fixture_render_fields`:
  `ab955a3081fc9d293d1b84fd257299069c0340c59037a5a8aa09f6ef11afe7f0`
  (= the Master-signed render, D117).

## Ghost — predicted PROVEN 3/3

Sides (SHAs as registered in `readiness/manifest.json`):
C5 `5d482965b7f5ed05ffef956d0eaed8b8a51af0ef` (role C),
checkpoint-bug-ghost-1 fix^
`93e0084723cdaf22d656bcd4811b87433e6b3875`,
checkpoint-bug-ghost-1 fix
`f67640b2c18c76abd9cd3f6d19d6ae93327b42fa`.

Per side: READY; fixture render
`ab955a3081fc9d293d1b84fd257299069c0340c59037a5a8aa09f6ef11afe7f0`;
leg 1 `GET /ghost/api/admin/tags/` → 200 with acquisitions
`{"mint": 1}` and a Bearer header; leg 2 the same (its own fresh
mint); leg 3 → **403 with 0 acquisitions and no Authorization
header** (the fixed credential-less bare leg). Verdict PROVEN on
all 3 sides.
