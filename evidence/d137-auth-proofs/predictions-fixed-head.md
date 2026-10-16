# D137 auth proofs — predictions for the FIXED driver head (superseding)

Committed BEFORE any dispatch of the runs below.

## What this supersedes

`predictions.md` (relay commit `6b92370`, 2026-10-16T21:09:06Z) covered
the ORIGINAL D137 driver at PR #134 head `d1bc3ef`. The reviewer's
changes then landed on PR #134 as head
`733cbdb6cabf9acbbed2b042d8bc2a4bf298259e`:

- per-leg verdicts (each authenticated leg: exactly 1 acquisition of
  the registered kind; the bare leg: 0 acquisitions AND no
  Authorization header — the old summed check is gone);
- the bare leg runs through a credential-less copy of the executor
  (the old driver's bare leg still acquired via the provider's
  proactive ensure — the recorded Directus rows of run `38967351907`
  show 1 login on leg 3 of every side, and the fixed `_proof_passed`
  run offline over those rows returns False on all 4 sides);
- generic ProofSpec body checks (Vendure: authenticated legs must
  show non-null `data.me.id`; the bare leg's
  `errors[0].extensions.code` must equal `"FORBIDDEN"`).

For Vendure leg 3 this CHANGES the expectation: `predictions.md`
predicted 401 (the old spec's registration). Vendure serves its
ForbiddenError as HTTP 200 with the FORBIDDEN error body, so the
prediction below is status **200 + the FORBIDDEN body check**. All
other per-side expectations from `predictions.md` carry over.

## Configuration under proof

- Directus re-run: PR #134 branch `feat/expires-unit-generic-proof`
  at `733cbdb6cabf9acbbed2b042d8bc2a4bf298259e` (the fixture contract
  is in-tree on that branch).
- Vendure rehearsal + full run: combined branch
  `proof/d137-vendure-combined` at
  `6ce7e1cbc34d9c00b100bea25cccb5faa9e17d13` = fixed #134 head
  `733cbdb` + PR #133 fixture head
  `7a1cc7d3e301342046bfaf9f22d45b40ec8d6164` (both
  `git merge-base --is-ancestor` checks exit 0; combined-tree
  Vendure render `ca9e5e07e310337aee0ff1822efdcfb2f68a02f68ad3237b5a83144cbd5618ae`;
  loaded login query contains no backslash).

## Directus — predicted PROVEN 4/4

Sides: C3 `dc922b79`, C4 `a5da59da`, checkpoint-bug-directus-1 fix^
`7e9b0350`, fix `93a3f393` (SHAs as registered in
`readiness/manifest.json`). Per side: READY; leg 1 `GET /users/me`
→ 200 with acquisitions `{"login": 1}` and a Bearer Authorization
header; leg 2 the same (its own fresh login); leg 3 → **401 with 0
acquisitions and no Authorization header** (the fixed bare leg).
Verdict PROVEN on all 4 sides.

## Vendure rehearsal — one side, predicted PROVEN

Side: checkpoint-bug-vendure-1, role fix (`4393199c`). Per the
generic driver with the registered spec: READY; leg 1
`POST /admin-api` `{ me { id identifier } }` → 200 with
acquisitions `{"login": 1}`, Bearer header, body `data.me.id`
non-null; leg 2 the same (its own fresh login); leg 3 → **200
with body `errors[0].extensions.code == "FORBIDDEN"`, 0
acquisitions, no Authorization header**. The rehearsal's recorded
leg-3 status and body are the evidence for the registered bare-leg
expectation; they are reported before the full run is dispatched.

## Vendure full run — predicted PROVEN 6/6

Sides: C1 `4cbbc790`, C2 `b19ab4dc`, checkpoint-bug-vendure-1 fix^
`859619c4` / fix `4393199c`, checkpoint-bug-vendure-2 fix^
`8fb22a8d` / fix `02c9d81e`. Per side: exactly the rehearsal shape
above; verdict PROVEN on all 6 sides.
