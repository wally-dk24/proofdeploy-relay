# Predictions — D137 auth proofs (Directus + Vendure)

Committed **2026-10-16T21:1xZ, before any dispatch** of either proof
(D137 / session-6 task 5: predictions first, in the relay).

Driver: the generic contract-driven auth-proof driver of PR
wally-dk24/proofdeploy#134 (`readiness/auth_proof.py`, D137 part 2),
dispatched via `.github/workflows/readiness.yml` with
`mode=auth-proof`, `attempt=1`. Proof shape (REG-E item 4): per side,
two authenticated requests — each with its OWN fresh acquisition,
counted by kind from the provider's own detail lines — then one
unauthenticated request expecting the registered refusal status.

## Directus — 4 sides, from PR #134 head `d1bc3efa9c0e94cc43d83b6300916135b0c4552b` (branch `feat/expires-unit-generic-proof`)

Endpoint: `GET /users/me`. Registered sides (readiness/manifest.json):

- `checkpoint-bug-directus-1` fix^ — `7e9b03504c6094bac8dace713c11a6f909016057`
- `checkpoint-bug-directus-1` fix — `93a3f39342e171d3ae3017c2aa9a744b5e1c3f8a`
- `C3` — `dc922b790a15949e8abfce9c1f4523d4e5f89492`
- `C4` — `a5da59da94bb33c8d644df93a21403eca4c1a4e7`

**Predicted, per side:** the side provisions READY; authenticated
request 1 → 200 with acquisitions `{"login": 1}`; authenticated
request 2 → 200 with acquisitions `{"login": 1}` (2 logins,
0 refreshes per side — fresh SetupContext per probe, D137's
withdrawal of the forced-refresh leg); unauthenticated request →
**401**.

**Predicted verdict: PROVEN on 4/4 sides.**

## Vendure — 6 sides, from combined head `5608980062f0c36c9ef5f0d415b34e963ee791f3` (branch `proof/d137-vendure-combined` = PR #134 head + fixture branch head `4e00385cf47268494b78d66f8efce4143b49be18`)

The combined tree carries the D131 fixture contract; its committed
render was verified before dispatch: sha256
`ca9e5e07e310337aee0ff1822efdcfb2f68a02f68ad3237b5a83144cbd5618ae`,
1390 bytes.

Endpoint: `POST /admin-api` with body `{"query": "query { me { id identifier } }"}`.
Registered sides:

- `C1` — `4cbbc790118786a17721954c3a4d739810bd2632`
- `C2` — `b19ab4dc76e4f8521e02258b8afa26f615142595`
- `checkpoint-bug-vendure-1` fix^ — `859619c4c289f7773619704f9cd1e5c1256dcfce`
- `checkpoint-bug-vendure-1` fix — `4393199c077e5cd9399696d25b69c620b36c5cc2`
- `checkpoint-bug-vendure-2` fix^ — `8fb22a8d882bb8b8cb2828c19f91e7c7d58f8aff`
- `checkpoint-bug-vendure-2` fix — `02c9d81e83bebebe8b3384b2f789800b14ed0146`

**Predicted, per side:** the side provisions READY; authenticated
request 1 → 200 (the `me` query naming the superadmin) with
acquisitions `{"login": 1}`; authenticated request 2 → 200 with
acquisitions `{"login": 1}` (login mode: 1 login per acquisition,
0 refreshes); unauthenticated request → **401** (the status
registered for Vendure in the driver's `PROOF_SPECS`).

**Predicted verdict: PROVEN on 6/6 sides.**
