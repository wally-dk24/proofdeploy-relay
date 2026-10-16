# WO-1.14 Part 2 — Vendure REG-E fixture/provider PROPOSAL (report only)

Status: PROPOSAL for the reviewer's ruling. Nothing is committed to the
contract; no PR for the fixture, no dispatch. **The provider question
ends in a STOP** (see the verdict): no existing `CredentialProvider`
mode can sign in as the Vendure superadmin and obtain the credential.
No `fixture:` values are drafted and no render is filed — both
presuppose a provider — and no new auth logic is designed here, per
the work order.

**UPDATE (D120, 2026-10-16): the STOP is resolved.** The reviewer
verified the verdict and ruled the engine change (D120): `login` mode
gains `token_header`. That change is built as PR #130
(`feat/auth-token-header`, head `4f10e24`). The addendum at the end of
this file — "D120 resolution" — proposes the `auth:` block, drafts the
five `fixture:` values from the seed facts below, files a render, and
sets out the auth-proof plan. The STOP analysis above stands as the
record of why the engine change was needed; the addendum's proposals
take effect only through the REG-E flow (reviewer ruling, fresh-agent
check, fixture PR after PR #130 merges).

Basis: REG-E (merged on main `b1b16e8`, D105); D115 item 5 ("send the
provider proposal (superadmin from dev-config.ts, existing modes
only)"). Contract on main: `readiness/contracts/vendure-ecommerce__vendure.yml`
— `build: "bun run build"`, `seed: "cd packages/dev-server && bun run populate"`,
`start: "cd packages/dev-server && bun run dev:server --no-reload"` (D64),
`readiness: "/health"`, `env: {DB: sqlite, RUN_JOB_QUEUE: "1"}`,
**no `auth:`, no `fixture:`**.

Registered units (confirmed from `readiness/manifest.json` on main
`b1b16e8`): C1 `4cbbc790118786a17721954c3a4d739810bd2632`,
C2 `b19ab4dc76e4f8521e02258b8afa26f615142595`,
checkpoint-bug-vendure-1 fix^ `859619c4c289f7773619704f9cd1e5c1256dcfce` /
fix `4393199c077e5cd9399696d25b69c620b36c5cc2`,
checkpoint-bug-vendure-2 fix^ `8fb22a8d882bb8b8cb2828c19f91e7c7d58f8aff` /
fix `02c9d81e83bebebe8b3384b2f789800b14ed0146` — 6 sides.

## Verdict: STOP — the token is in a header; every provider mode reads only the body

The superadmin account exists and its credentials are known constants
(below). The login exchange itself is expressible in the existing
`login` mode. What no existing mode can do is **obtain the token**:
every provider path extracts the token from the JSON **response body**
at `token_json_path`, discarding the response headers — and Vendure
returns its session token **only in the `vendure-auth-token` response
header**. The login response body is the user object; it contains no
token at any path. One mechanical fact, verified in both codebases
(citations in the mode-by-mode analysis):

- Ours: `executor.py` `_login_exchange` (L857) does
  `status, _, resp_body, req_logs = _do_http(...)` (L903 — the headers
  element is discarded) and then requires a non-empty string at
  `_dig_json_path(data, token_path)` in the parsed body; `_provide_refresh`
  (L749, discard at L808) and `_provide_register` (L964) read the body
  the same way. `ghost_jwt` never makes a login request at all.
- Upstream: `packages/core/src/api/common/set-session-token.ts` —
  for the bearer method, `res.set(authOptions.authTokenHeaderKey, sessionToken)`;
  for cookie, `req.session.token = sessionToken`. The body is never
  written. `authTokenHeaderKey` defaults to `vendure-auth-token`
  (`packages/core/src/config/default-config.ts` L113;
  `packages/common/src/shared-constants.ts` L14).

Unblocking this requires a provider capability the engine does not
have (a way for a provider to take the token from a response header).
That is new engine work — a decision for Master/the reviewer, **not
designed here** and not smuggled into this proposal.

## The account: the superadmin (credentials are core defaults, not per-run)

`dev-config.ts` declares **no** `superadminCredentials`, so the core
default applies on every side:

- `packages/core/src/config/default-config.ts` L118–121:
  `superadminCredentials: { identifier: SUPER_ADMIN_USER_IDENTIFIER,
  password: SUPER_ADMIN_USER_PASSWORD }`.
- `packages/common/src/shared-constants.ts` L9–10: identifier
  `'superadmin'`, password `'superadmin'` — upstream's published dev
  defaults, identical for every instance (not per-run secrets; under
  the D113 ruling they are caller credentials, stated here because the
  provider analysis turns on them).
- Creation: every bootstrap runs `InitializerService.onModuleInit`
  (`packages/core/src/service/initializer.service.ts` L42–58) →
  `roleService.initRoles()` (L54; `role.service.ts` L81–85: SuperAdmin
  role + Customer role) and `administratorService.initAdministrators()`
  (L55; `administrator.service.ts` L59–61 → `ensureSuperAdminExists`,
  L417–420, reading `authOptions.superadminCredentials`).
- Runtime auth config (`dev-config.ts` L103–112): `tokenMethod:
  ['bearer', 'cookie', 'api-key']`, `requireVerification: true`,
  cookie secret `'abc'` (a config value, not a caller credential —
  D113 ruling). Admin API path `admin-api`, port 3000
  (`shared-constants.ts` L3–4; `dev-config.ts` L86–102).
  Session duration `'1y'` (`default-config.ts` L114). A held token is
  presented as `Authorization: Bearer <token>` (or the
  `vendure-auth-token` header, or the session cookie):
  `packages/core/src/api/common/extract-session-token.ts` L27–40.

## Mode-by-mode fit (existing modes only; engine at main `b1b16e8`)

Mode key sets are closed (`proofdeploy/yml.py` L64, `_AUTH_MODE_KEYS`;
unknown keys refused at load). The contract's `auth:` mapping becomes
the executor's `credential_config` verbatim, minus `bootstrap_env`
(`orchestrator.py` `_credential_config`, L1868).

- **`ghost_jwt`** — mints an HS256 JWT locally from a static
  `<id>:<secret>` admin key taken from a binding or a seed-written
  `key_file` (`setup_auth.py` `mint_ghost_jwt`, L330; `CredentialProvider`
  docstring). Vendure has no such key: admin auth is username/password
  exchanged for a server-side session. **No fit.**
- **`register`** — POSTs a declared endpoint with a **randomly
  generated** username/password and digs the token from the body
  (`executor.py` `_provide_register`, L964). By construction it creates
  a new account; it cannot sign in as the existing superadmin. (The
  Admin API also exposes no registration endpoint; the Shop API's
  customer registration creates a customer, not the superadmin, and
  its session token likewise arrives by header.) **No fit.**
- **`refresh`** — calls a declared refresh endpoint; Vendure exposes
  none (sessions are server-side; nothing refresh-shaped exists in the
  auth resolvers). With `login_path`/`login_body` declared, the first
  acquisition delegates to the same `_login_exchange` — the header
  blocker applies unchanged. **No fit.**
- **`login`** — POSTs the declared path with the declared body
  template; its `${NAME}` placeholders resolve from the run's
  bindings, which include **contract `env` keys with their literal
  values** (`executor.py` `_make_context`, L617; env at L628,
  `bootstrap_env` at L635) — so the fixed superadmin credentials
  *could* be fed mechanically (the body must be a string→string map,
  `yml.py` `_auth_str_map`, so a GraphQL `query` string with the
  credentials interpolated is the expressible form; the Admin API
  `login` mutation is public: `auth.resolver.ts` L34–49 →
  `base-auth.resolver.ts` L48–63). The exchange would authenticate.
  It then fails at token extraction, for the reason in the verdict:
  the token is in the `vendure-auth-token` header, the provider reads
  only the body, and the body's `data.login` is the CurrentUser
  object (id, identifier, channels) — no `token_json_path` exists.
  **No fit — this is the blocker.**
- **No `auth:` (status quo)** — no credential at all; not a provider
  proposal.

## What the seed creates — verified per SHA

Every file cited below is **byte-identical at all 6 registered SHAs**
(md5s computed per SHA; the table gives the shared md5 prefix). The
identity claim is scoped to exactly these files.

| Fact | Evidence (identical at all 6 SHAs) |
|---|---|
| The contract's seed runs the dev-server `populate` script | `packages/dev-server/package.json` (md5 `b41b63a9…`) L10: `"populate": "node -r ts-node/register -r dotenv/config populate-dev-server.ts"` |
| Populate flow: clear all tables → `populate(bootstrap, initialData, products.csv)` → 10 customers | `packages/dev-server/populate-dev-server.ts` (md5 `fcaaffa8…`) L36–52; the populate run's own merged config sets `tokenMethod: 'bearer'`, `requireVerification: false` for the populate process only (L21–33) |
| `devConfig` itself (the running server's config) | `packages/dev-server/dev-config.ts` (md5 `c8d2e52e…`): auth L103–112; sqlite `synchronize: true`, DB file `packages/dev-server/vendure.sqlite` (getDbConfig L192–199); `dummyPaymentHandler` L123–125 |
| Bootstrap initializers create: default zone/settings/seller/channel, the **SuperAdmin role** and **Customer role**, the **superadmin user**, shipping methods, tax rates, stock locations | `initializer.service.ts` (md5 `b55585df…`) L42–58; `role.service.ts` (md5 `97656efe…`) L81–85; `administrator.service.ts` (md5 `a78bbf48…`) L59–61, L417–420; credentials `default-config.ts` (md5 `fa4d20dd…`) L118–121 + `shared-constants.ts` (md5 `57f20080…`) L9–10 |
| `populate()` then applies the initial data and imports the catalogue | `packages/core/src/cli/populate.ts` (md5 `d61fd2f3…`) L51, L79 (`populateInitialData`), L82–85 (products CSV import), L99 (collections); `populator.ts` (md5 `ba55b689…`) L73, L108 (roles) |
| Initial data contents: countries; default zone 'Europe'; 3 tax rates (Standard 20%, Reduced 10%, Zero 0%); shipping methods; payment methods; collections; and **3 roles**: `administrator`, `order-manager`, `inventory-manager` | `packages/core/mock-data/data-sources/initial-data.ts` (md5 `972810cc…`) L5–89 (roles L8–70; tax rates L71–75) |
| Products: imported from the repo's own `packages/create/assets/products.csv` | populate-dev-server.ts L44 (path argument) |
| **10 shop customers**, each with an address, password `'test'`, holding the Customer role; data is deterministic mock data (`faker.seed(1)`) | `populate-dev-server.ts` L49 (`populateCustomers(app, 10, …)`); `packages/testing/src/data-population/populate-customers.ts` (md5 `3262ca1e…`) L16–35; `mock-data.service.ts` L19–27 |
| The server entry the contract's start runs (D64) | `packages/dev-server/index.ts` (md5 `7549741e…`) L8–10: `runMigrations(devConfig)` → `bootstrap(devConfig)` |

Net contents of a seeded instance, all sides: **5 roles** (SuperAdmin,
Customer, administrator, order-manager, inventory-manager),
**11 users** (the superadmin + 10 customers), the default channel,
the initial-data countries/zones/tax/shipping/payment/collections, and
the CSV product catalogue. No API keys are created by the seed.

## Not filed, and why

- **Fixture values**: not drafted. `auth_scope`/`auth_mechanism`
  describe the token the provider hands the author; with no provider
  able to obtain one, drafted values would present a committable
  fixture that REG-E could never auth-prove. For the record, any
  future text must honour the facts above — notably that the seed
  creates 11 users and 5 roles (the template's fixed sentence "No
  other users, roles, or credentials exist beyond what the seed
  creates" makes the seed note carry all of them), that the
  superadmin credentials are upstream's published defaults rather
  than per-run values, and that a session token, once held, is
  presented as `Authorization: Bearer <token>` with a `'1y'` session
  duration.
- **Render**: none (a render is of drafted values).
- **Auth-proof plan**: none filed — there is no provider to prove.
  The D106-negative-test proof (6 sides, predictions first) becomes
  schedulable only after a provider exists and a fixture is committed
  under REG-E.

## What would unblock (for Master/the reviewer to decide)

A provider capability for response-header token extraction (engine
work in `proofdeploy/setup_auth.py`/`executor.py`), or another
reviewed mechanism by which the existing modes can hold a Vendure
session token. Not designed here, per the work order. Until then the
Vendure contract stays without `auth:`/`fixture:` and its units stay
out of measured runs that require a credential.

---

# ADDENDUM — D120 resolution (2026-10-16)

## The engine change (PR #130)

Ruling D120 (reviewer, within plan 1.14; the STOP above verified
first-hand: `_login_exchange` discards the headers —
`status, _, resp_body = _do_http(...)`): `login` mode gains ONE
optional field, `token_header` (a response-header name,
case-insensitive). Exactly one of `token_json_path` / `token_header`
must be declared for login mode, validated at load (both or neither
refused). When `token_header` is declared, the token is that header's
value; a missing or empty header is INCONCLUSIVE `auth`; a 200
without the header is an auth failure, never a token. Presentation
unchanged; fully generic. Built as **PR #130** (branch
`feat/auth-token-header`, head `4f10e246412e62d814a969df7e034ab668fda4ca`):
8 new tests (one per ruled case + load validation + case-insensitivity),
plant verified (ignoring `token_header` fails 2 tests), ruff/mypy
clean, manifest 983/89, pins unchanged. Not merged — reviewer
acceptance + Master's authorization gate it as usual.

## Proposed `auth:` block (for the future fixture PR; NOT committed)

```yaml
auth:
  mode: login
  path: /admin-api
  method: POST
  body:
    query: 'mutation { login(username: "superadmin", password: "superadmin") { ... on CurrentUser { id identifier } } }'
  token_header: vendure-auth-token
```

- The Admin API login mutation is public (`auth.resolver.ts` L34–49 →
  `base-auth.resolver.ts` L48–63, cited in the mode-by-mode analysis);
  the response carries the session token in the `vendure-auth-token`
  header (`set-session-token.ts`; the runtime config's `tokenMethod`
  includes `'bearer'`, `dev-config.ts` L103–112), and the body is the
  CurrentUser — exactly the D120 header case.
- The credentials are upstream's published core defaults
  (`default-config.ts` L118–121 + `shared-constants.ts` L9–10):
  `dev-config.ts` declares no `superadminCredentials`, so identifier
  and password are `superadmin`/`superadmin` on every instance. They
  are written literally in the GraphQL query string because the
  contract `body` is a flat string map (`yml.py` `_auth_str_map`) —
  a nested `variables` object is not expressible. They are not per-run
  secrets (D113 ruling: caller credentials, stated because the fixture
  text turns on them).
- The block was load-validated under the PR #130 engine
  (`load_contract` normalizes it to mode/path/method/body/token_header,
  no `token_json_path`).

## The five fixture values (drafted; ASCII apostrophes only)

```yaml
fixture:
  repo_name: "Vendure"
  seed_note: "Vendure's own populate seed, run as the setup step, installed the initial data: five roles (SuperAdmin, Customer, administrator, order-manager and inventory-manager) and eleven users — the superadmin account and ten deterministic mock customers, each with an address and the password 'test'. It also created the default channel, the initial countries, zones, tax rates, shipping and payment methods and collections, and imported the product catalogue from the repository's own products.csv. It created no API keys."
  auth_mechanism: "the token is presented in the Authorization header in the form 'Bearer <token>'"
  auth_scope: "a session token belonging to the superadmin account, obtained by signing in to the admin API as that account; the account's username and password are upstream's published defaults, the same for every instance, because the instance config declares no others. The account holds the SuperAdmin role. At this version's default configuration a session lasts one year"
  flags_note: "no feature flags or experimental options were enabled or changed"
```

Every statement traces to the per-SHA verified facts in "What the seed
creates" above (all cited files byte-identical at the 6 registered
SHAs): 5 roles and 11 users as named; the customers' password `'test'`
(`populate-customers.ts` L16–35); the default channel, reference data
and CSV catalogue (`initial-data.ts`, `populate.ts`, populate-dev-server
L44); no API keys created anywhere in the seed path. Bearer
presentation: `extract-session-token.ts` L27–40. Session duration
`'1y'`: `default-config.ts` L114. The superadmin holding the
SuperAdmin role: `administrator.service.ts` L59–61, L417–420 +
`role.service.ts` L81–85. Fixed template sentences checked: "No other
users, roles, or credentials exist beyond what the seed creates" —
the seed note carries all 11 users and 5 roles, and both credentials
the seed creates are disclosed (the customers' `'test'` in the seed
note; the superadmin's published defaults in `auth_scope`); per the
D113 ruling, the cookie secret `'abc'` in dev-config is configuration,
not a caller credential. `flags_note` uses the D113-safe form (nothing
enabled or changed) rather than claiming no flag system exists.

## Render

Rendered with the real engine code on PR #130's branch (fixture code
identical to main `b1b16e8`; template `fixture_template.txt`, pin
`0994d1e3…` verified) from a scratch copy of the contract plus the two
blocks above — the real contract file was never edited:
`rendered-fixture.txt` in this directory, **sha256
`2aa369f15fa2bde9a480e1c01b8363376dda5a3da884a66b705098275573809f`**,
1296 bytes.

## Auth-proof plan (after the fixture is committed under REG-E)

Ghost pattern (D87/D91/D106/D113) adapted, on all 6 sides, predictions
filed before the first dispatch, D61 pack after:

- Request: `POST /admin-api` with the GraphQL query
  `{ me { id identifier } }` — an auth-required admin query (the `me`
  query resolves the current administrator from the session).
- Legs per side: (1) with the provider's token → 200, and the response
  names the superadmin (`data.me.identifier` = `superadmin`); (2) a
  second authenticated request whose credential comes from a **fresh
  login** — login mode's semantics are one acquisition per run (the
  session does not near expiry), so leg 2 runs a second provider
  acquisition (a new login, not a refresh: Vendure has no refresh) →
  200; acquisitions recorded **by kind: exactly 1 login per leg, 0
  refreshes**; (3) no Authorization header → refused (non-2xx, or a
  GraphQL errors-only body with no `data.me` — the exact refusal shape
  is pinned from upstream's auth-guard behavior when the proof is
  built, and stated in the predictions before dispatch).
- Negative tests per D106, each yielding FAILED not PROVEN: the
  unauthenticated leg returning success; wrong acquisition counts or
  kinds; an authenticated leg returning non-200.
- Uses the existing auth-proof driver with this declared request; if
  the driver cannot express a POST GraphQL request, that step stops
  and reports — no new proof machinery is built under this plan.

## Sequence from here

PR #130 merges (reviewer + Master) → the reviewer rules on these
values → a fresh agent independently checks accuracy/neutrality
(REG-E item 3) → the fixture PR commits `auth:` + `fixture:` byte-exact
(render from the committed contract must equal `2aa369f1…`) → the auth
proof above runs → Vendure's units become measurable.

