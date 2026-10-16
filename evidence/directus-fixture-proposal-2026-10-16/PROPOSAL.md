# WO-1.14 Part 2 — Directus REG-E fixture PROPOSAL (report only)

Status: PROPOSAL for the reviewer's ruling. Nothing is committed to the
contract; no PR, no dispatch. The `fixture:` block below was rendered in a
scratch copy of the contract and has NOT been added to
`readiness/contracts/directus__directus.yml`.

Basis: REG-E (merged on main `b1b16e8`, D105) items 2–4; D111 item 3.
Contract taken from PR #122's branch (`feat/directus-contract-d98`) — the
contract file is byte-identical at the accepted head `b7584c6` and at the
D51-refreshed head `811e3c0` (empty diff for that file, verified).
Registered sides (confirmed from `readiness/manifest.json` on main
`b1b16e8`): checkpoint-bug-directus-1 fix^ `7e9b03504c6094bac8dace713c11a6f909016057`,
fix `93a3f39342e171d3ae3017c2aa9a744b5e1c3f8a`; C3 `dc922b790a15949e8abfce9c1f4523d4e5f89492`;
C4 `a5da59da94bb33c8d644df93a21403eca4c1a4e7`.

## The five proposed values (verbatim)

```yaml
fixture:
  repo_name: "Directus"
  seed_note: "Directus's own bootstrap, run as the setup step, installed its system tables and created exactly one user — an administrator account whose email and password are generated fresh for each run — and exactly one role, 'Administrator', linked to one 'Administrator' policy granting full administrative access. It created no content collections, items or files, and it changed no project settings."
  auth_mechanism: "the token is presented in the Authorization header in the form 'Bearer <token>'"
  auth_scope: "an access token belonging to the single administrator account the bootstrap created, obtained by signing in as that account with its per-run generated email and password and renewed by refresh before it expires; access tokens last 15 minutes and the refresh token 7 days. The token carries the account's full administrative access; the account has no static API token"
  flags_note: "Directus has no feature-flag system; project settings are at this version's defaults and none were changed"
```

## Render

Rendered with the engine's real code at main `b1b16e8`:
`load_contract` on the scratch contract (branch contract + the block
above) → `template_filename(contract.auth is not None)` →
`fixture_template.txt` → `render_description`. The template file's sha256
`0994d1e367ddb3c679e750451dd67eab3a3d974fc1620b7f750216cdcdf0323b` equals
`REGISTERED_TEMPLATE_SHA256` (pin match). The parsed `fixture:` values
round-tripped string-for-string. Rendered text: `rendered-fixture.txt`
in this directory, **sha256
`06ee0be7676a439eea0e8281a2144df02a3187cf42b6954e421c89c3892ffa12`,
1225 bytes**.

## What `directus bootstrap` creates — verified per SHA

All citations are upstream `directus/directus` source fetched at each
registered SHA. Where a file's md5 is listed once, the file is
byte-identical at all four SHAs (verified by fetching each SHA).

| Fact | Evidence (identical at all 4 SHAs unless noted) |
|---|---|
| Bootstrap order: if not installed → install system tables, run migrations to latest, then create the admin | `api/src/cli/commands/bootstrap/index.ts` (md5 `b06bf770…`): L28–34 install + `runMigrations(..., 'latest')`; L48–49 `createAdmin(schema)` |
| The install step creates TABLES ONLY — one per YAML table seed in its directory; it inserts no rows | `api/src/database/seeds/run.ts` (md5 `e3775db5…`): L41–50 reads the seed files; L50–113 `createTable` per seed; no insert anywhere in the file |
| Migrations are schema migrations (112 files listed at fix; none named for seeding content/settings rows) | `api/src/database/migrations/` listing at `93a3f393` |
| Exactly ONE role is created: name `Administrator` | `api/src/utils/create-admin.ts` (md5 `1a868781…`): L10–14 `defaultAdminRole`; L53 `rolesService.createOne(defaultAdminRole)` |
| Exactly ONE policy is created: name `Administrator`, `admin_access: true`, `app_access: true`, linked to the role by one access row | same file: L22–28 `defaultAdminPolicy`; L54 `policiesService.createOne(...)`; L56 `accessService.createOne({ policy, role })` |
| Exactly ONE user is created: `first_name 'Admin'`, `last_name 'User'`, `status 'active'`, email = `ADMIN_EMAIL` env, password = `ADMIN_PASSWORD` env, holding the Administrator role. If either env value is absent, NO admin is created at all | same file: L16–20 `defaultAdminUser`; L42–46 env read + early return; L64–72 the single `usersService.createOne({...})` |
| The admin user's static API token is NULL: it is set from `ADMIN_TOKEN` env, which the contract never declares | same file: L60 `const token = env['ADMIN_TOKEN'] ?? null` |
| Settings writes are conditional and do NOT fire under this contract: `project_name` is upserted only if `PROJECT_NAME` env is set, owner only if `PROJECT_OWNER` is a valid email. The contract's env is only `DB_CLIENT`/`DB_FILENAME` plus `bootstrap_env: [ADMIN_EMAIL, ADMIN_PASSWORD, SECRET]` | `bootstrap/index.ts` L56–67; contract `env:`/`auth.bootstrap_env` on the #122 branch |
| Settings otherwise hold shipped column defaults (e.g. `project_name` column default `'Directus'`) | `api/src/database/seeds/13-settings.yaml` at `93a3f393` (column definitions only) |
| No content collections, items, files, flows or webhooks are created anywhere in the install/migrate/createAdmin path | by exhaustion of the three files above: the only `createOne` calls are role, policy, access, user |
| API package version per SHA | `api/package.json`: 39.2.1 at fix^ and fix; 39.1.0 at C3 and C4 |

Per-SHA differences found: NONE in bootstrap behavior. The only
differences are the API version (39.2.1 vs 39.1.0) and the bytes of
`api/src/services/authentication.ts` (fix `65773ea8…`; fix^, C3, C4
`46967dea…`) — its TTL mechanics are the same in both variants (below).

## The provider — `refresh` mode, and why the text says what it says

The contract declares `auth: {mode: refresh, path: /auth/refresh,
login_path: /auth/login, login_body: {email: "${ADMIN_EMAIL}", password:
"${ADMIN_PASSWORD}"}, token_json_path: data.access_token,
refresh_token_json_path: data.refresh_token, expires_in_json_path:
data.expires, bootstrap_env: [ADMIN_EMAIL, ADMIN_PASSWORD, SECRET]}`.

Mechanics, from our code (`proofdeploy/setup_auth.py` on main):

- The `bootstrap_env` values are generated fresh per side and per run
  (`generate_bootstrap_env`, L64–103: an `pd-<hex>@example.com` value for
  the EMAIL name, 32 hex chars for the others), injected into the side's
  install and app containers — so the bootstrap creates the admin with
  exactly these credentials — and feed the provider.
- `refresh` mode with a login path declared: **the first acquisition is
  a login**; the response's refresh token is bound as `${REFRESH_TOKEN}`
  for the refresh body; **later acquisitions refresh near expiry**
  (module docstring L35–38; `CredentialProvider` docstring L402–411).
  Refresh is proactive (60 s margin, `token_near_expiry`), never a
  reaction to a response. The provider creates no users.
- `${AUTH_TOKEN}` is the current access token of that one admin account.

Lifetimes, from upstream (no TTL override exists anywhere in the
contract): `packages/env/src/constants/defaults.ts` (md5 `42e10c43…`,
identical at all 4 SHAs) L59 `ACCESS_TOKEN_TTL: '15m'`, L62
`REFRESH_TOKEN_TTL: '7d'`. Login returns `expires:
getMilliseconds(TTL)` — `api/src/services/authentication.ts` L320 at fix,
L327 at C3 — i.e. `data.expires` = 900000 ms; the refresh token's expiry
is `now + REFRESH_TOKEN_TTL` (same file, L255 at fix, L262 at C3).

The word **"static" does not appear** in the proposed text, and the
scope sentence states the opposite facts: the token is a short-lived
login-issued access token renewed by refresh, and the account has no
static API token (its `token` column is null, create-admin.ts L60).
This corrects the superseded 2026-10-09 wording (hash `55aa188b…`,
superseded by REG-E item 7), which called the token "static".

## Fixed-sentence check (frozen template, credential variant)

The template adds these fixed sentences; each checked against the facts
above (REG-E item 2):

1. "Your token: ${AUTH_TOKEN}." — the runner injects the provider's
   current access token (setup_auth.py precedence, L13–19). TRUE.
2. "No other users, roles, or credentials exist beyond what the seed
   creates." — Users: exactly the one admin (create-admin.ts L64–72,
   the only user creation in the path). Roles: exactly the one
   Administrator role; the one policy is named in the seed note.
   Credentials: the admin's email/password are generated by the
   provisioning step itself and are the seed account's own; the tokens
   are that same account's login artifacts, whose mechanism
   `auth_scope` discloses. **One judgment point for the reviewer:**
   the per-run `SECRET` (token-signing secret, from `bootstrap_env`)
   is an instance secret generated by ProofDeploy, not a user
   credential and not created by the bootstrap; the sentence is
   accurate read over users/roles/credentials as the Ghost D94 text
   used it, but the reviewer rules on this reading.
3. "You may create users, roles, data, or change settings in setup
   steps before your probes run." — an instruction, not a claim about
   the instance; the admin token's `admin_access` makes it operable.
   TRUE as stated.

No fixed sentence was found that cannot be true for Directus; nothing
was papered over. No blocker.

## Proposed auth proof (LATER step — after the reviewer rules and the fixture is committed)

Ghost pattern (D87/D91/D106), on all 4 sides, predictions filed before
the first dispatch, D61 pack after:

- Endpoint: an auth-required admin endpoint — NOT `/server/ping`
  (public; it is the readiness path and answers unauthenticated). The
  proposal is `GET /users/me`: 200 with the token, **401** without
  (final endpoint choice is the reviewer's at proof time).
- Legs per side: (1) authenticated request → 200; (2) a second
  authenticated request carrying a FRESH credential acquisition
  (acquisition count exactly 2) → 200; (3) no Authorization header →
  401.
- Negative tests mirroring D106, each yielding a FAILED verdict, not
  PROVEN: the unauthenticated leg returning 200; the acquisition count
  being 1, and 3; an authenticated leg returning non-200. The
  reviewer's plants on the verdict logic must fail these tests.
