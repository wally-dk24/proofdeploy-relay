# linkding contract proposal (WO-R5-L, REPORT ONLY)

Author: builder-sonnet (D237). Date: 2026-10-21.
Source: https://github.com/sissbruecker/linkding, default-branch HEAD **27b7303baf41bb28babc610ac8eaa486e1ddfab5** (shallow clone, `/home/claude/r5-linkding`). Every `file:line` below is at that SHA. Source files only were read: no commit messages, log, issues or diffs. Citations into ProofDeploy (`proofdeploy/...`) are at the current `/home/claude/proofdeploy` checkout (read only).
Nothing was installed, run or changed. Every runtime behaviour below is read from source; items that need a live run to confirm are marked UNVERIFIED and repeated in "Open questions".

## 0. Verdict

linkding is a Django app (manage.py at the repo root, SQLite by default, uv.lock committed). It can be provisioned in one process with no npm build. **It has no endpoint that exchanges credentials for an API token**, so the lean tool's `mode: login` cannot sign in to it as is. API tokens are created only through the logged-in settings UI (an HTML form post), the admin site, or the ORM. I propose a small generic lean addition (`mode: static_token`, the token coming from a generated bootstrap env value that the seed also writes into the app) and give the alternative.

## 1. Contract

### Runtime and install
- Python `>=3.13` (`pyproject.toml:6`); CI uses 3.13 (`.github/workflows/main.yaml:18`). Compare paperless-ngx at `>=3.11`; the provisioner must offer 3.13 (open question 1).
- uv project with a committed `uv.lock` (repo root) and `[tool.uv]` (`pyproject.toml:46-48`). There is no `[build-system]` table in `pyproject.toml` (grep: none), so uv treats it as a virtual project (dependencies only).
- The project's own install: `uv sync` (`Makefile:6`); the production image uses `uv sync --no-dev --group postgres` (`docker/default.Dockerfile:27`). The frozen, minimal equivalent is `uv sync --frozen --no-dev` (no dev group, no postgres group; SQLite is the default engine, `bookmarks/settings/base.py:263`, see below). Same convention as the paperless contract, where the harness does the frozen install and the contract's `build` holds only the app's own extra steps.
- Risk: `uwsgi>=2.0.31` and `supervisor>=4.3.0` are in the main dependencies (`pyproject.toml:17-18`), so `uv sync --no-dev` still builds uwsgi from source (needs a C compiler and Python headers in the install container). Neither is used by the proposed start command.

### Build steps the API needs
- Frontend assets are **not needed for the API or for `/health`**:
  - The JS/CSS bundles come from `npm run build` (`package.json:4-7`) and are gitignored build outputs (`.gitignore:186`, `.gitignore:188`).
  - They are referenced only from HTML templates (`bookmarks/templates/shared/head.html:63`).
  - API views return DRF `Response` objects (for example `bookmarks/api/routes.py:258-261`) and `/health` returns `JsonResponse` (`bookmarks/views/health.py:7-17`); none renders a template.
  - `settings/base.py` sets no `STORAGES`/`STATICFILES_STORAGE` (grep: none), so Django's default non-manifest static storage applies and a missing bundle cannot raise on `{% static %}`. Only HTML pages (for example `/login/`) would render without their bundle.
  - `collectstatic` (`Makefile:33`) is not needed either.
- A `data/` directory must exist before migrate:
  - SQLite lives at `BASE_DIR/data/db.sqlite3` (`bookmarks/settings/base.py:284`).
  - The task queue file is `BASE_DIR/data/tasks.sqlite3` (`base.py:178-180`).
  - The upstream container does `mkdir -p data`, `data/favicons`, `data/previews`, `data/assets` (`bootstrap.sh:8-14`). The favicon, preview and asset folders are configured at `base.py:302,311,323` and only used by features that are off by default (section 6).
- So `build` = `mkdir -p data/favicons data/previews data/assets`.

### migrate
- `python manage.py migrate --noinput` (upstream: `bootstrap.sh:19`). Data migrations that exist (`0007`, `0013`, `0029`, `0032`, `0044`, `0047`, `0050`, `0053` under `bookmarks/migrations/`) iterate existing users, tokens or bookmarks, so on an empty database they create nothing (for example `0013_web_archive_optin_toast.py:12`, `0053_migrate_api_tokens.py:9`).
- Not needed for a throwaway single-process run:
  - `enable_wal` (`bootstrap.sh:20`, `bookmarks/management/commands/enable_wal.py`): journal-mode tuning.
  - `generate_secret_key` (`bootstrap.sh:17`): see env below.
  - `migrate_tasks` (`bootstrap.sh:25`): "Migrate tasks from django-background-tasks to Huey", a legacy upgrade path (`bookmarks/management/commands/migrate_tasks.py:11`).

### seed
- Creates one user and one API token; see section 3.

### start (single process, no watcher, `$PORT`)
- `python manage.py runserver 0.0.0.0:$PORT --noreload`. Upstream production uses `uwsgi` plus a `supervisord` task consumer (`bootstrap.sh:30-39`, `uwsgi.ini`), which is two long-lived processes; the proposal deliberately uses neither. The Huey consumer is not started (section 6 shows no task is enqueued by the default API flow).
- Settings module: `manage.py:8` does `os.environ.setdefault("DJANGO_SETTINGS_MODULE", "bookmarks.settings")`. `bookmarks/settings/__init__.py:1-6` imports `.dev` and falls back to `.prod` only if the import fails.
  - `dev.py` ships in the repo, so the default is **dev**: `DEBUG = True` (`settings/dev.py:12`), which also adds a `live_reload` route (`bookmarks/urls.py:122-124`).
  - Recommendation: set `DJANGO_SETTINGS_MODULE=bookmarks.settings.prod` in `env`. Then `DEBUG = False` (`settings/prod.py:15`), error responses are not debug pages, and the behaviour matches a real deployment.
  - `prod.py` reads the secret key from `data/secretkey.txt` and otherwise uses a random per-process key (`prod.py:17-22`), `ALLOWED_HOSTS` is `["*"]` unless `HOST_NAME` is set (`prod.py:26-30`), and it imports the empty `custom.py` (`prod.py:63`, `settings/custom.py:1`). Token auth needs no session key, and one process is used, so no `generate_secret_key` step is needed.
  - Under prod settings `runserver` does not serve `/static/`; the API does not need it.

### readiness path
- `/health` (`bookmarks/urls.py:110-111`): unauthenticated JSON `{"version":..., "status":"healthy"}` with status 200 once the DB connection works, 500 otherwise (`bookmarks/views/health.py:7-17`). Upstream's own Docker HEALTHCHECK curls it (`docker/default.Dockerfile:80`). `/` is only a redirect to `/bookmarks` (`bookmarks/views/root.py:7-18`), which in turn requires login, so `/health` is the better choice.

### env
| Name | Value | Why |
|---|---|---|
| `DJANGO_SETTINGS_MODULE` | `bookmarks.settings.prod` | `manage.py:8` honours a preset value; avoids DEBUG (above). |
| `LD_SUPERUSER_NAME`, `LD_API_TOKEN` | generated by the harness, declared in `auth.bootstrap_env` | seed input (sections 2, 3). |

- SQLite needs no env: `LD_DB_ENGINE` defaults to `sqlite` (`base.py:263`), and the path is `BASE_DIR/data/db.sqlite3` (`base.py:284`). That matches `database: {kind: sqlite, path: data/db.sqlite3}`.
- Secret key: needs no env. `base.py:25` has a hardcoded `SECRET_KEY`; `prod.py` overrides it (above). There is no `ImproperlyConfigured` path like paperless's.
- Sandbox with no network: no setting is required for the app to start or serve; see section 6 for the one outbound call.
- Optional: `LD_DISABLE_BACKGROUND_TASKS=True` (`base.py:170`) stops task scheduling (`bookmarks/services/tasks.py:44,110,117,162,222,247`). **Not recommended**, because with the default per-user flags no task is enqueued anyway (section 6), and leaving it unset keeps the fixture's flags_note shorter. It is the fallback if a probe is ever seen to enqueue a task.
- Fidelity note: the Docker image compiles a SQLite ICU extension for case-insensitive search and ordering (`docker/default.Dockerfile:38`). It is loaded only if `./libicu.so` exists (`base.py:295-297`, `bookmarks/signals.py:8-21`), so a pip-style run orders and searches case-sensitively. This is a known deployment difference, not a blocker.

### app.path
- `"."` (`manage.py` is at the repo root; same as the Ghost and Directus contracts).

### Draft contract
```yaml
app:
  path: "."
build: "mkdir -p data/favicons data/previews data/assets"
migrate: "python manage.py migrate --noinput"
seed: "python manage.py create_initial_superuser && python manage.py shell -c \"import os; from django.contrib.auth import get_user_model; from bookmarks.models import ApiToken; ApiToken.objects.create(user=get_user_model().objects.get(username=os.environ['LD_SUPERUSER_NAME']), name='ProofDeploy', key=os.environ['LD_API_TOKEN'])\""
start: "python manage.py runserver 0.0.0.0:$PORT --noreload"
readiness: "/health"
database:
  kind: sqlite
  path: data/db.sqlite3
env:
  DJANGO_SETTINGS_MODULE: bookmarks.settings.prod
auth:
  mode: static_token          # NEW lean mode (section 2); not in yml.py today
  token_env: LD_API_TOKEN
  bootstrap_env: [LD_SUPERUSER_NAME, LD_API_TOKEN]
fixture:
  # see section 4
```

## 2. Sign-in

### What linkding offers (cited)
- Auth classes: `LinkdingTokenAuthentication`, then DRF `SessionAuthentication` (`bookmarks/settings/base.py:145-149`); default permission `IsAuthenticated` (`base.py:150`).
- The token class accepts `Authorization: Token <key>` **or** `Bearer <key>` (keywords at `bookmarks/api/auth.py:15`; parsing at `auth.py:17-38`). The API docs show `Authorization: Token <Token>` (`docs/src/content/docs/api.md:13-16`).
- A token is an `ApiToken` row: 40-char unique `key`, `user`, `name` (`bookmarks/models.py:518-526`). `save()` keeps a preset key and only generates one (20 random bytes in hex) when `key` is empty (`models.py:528-535`).
- **No credentials-to-token endpoint exists.** The API routes are `api/` (default router), `api/bookmarks/`, `api/tags/`, `api/bundles/`, `api/user/` (`bookmarks/urls.py:92-100`), and none issues tokens. `rest_framework.authtoken` is installed (`base.py:50`), but `obtain_auth_token` is referenced nowhere (grep of `bookmarks/` for `obtain_auth_token` and `authtoken` finds only `base.py:50` and a docstring at `models.py:494`).
- Tokens are created in only three ways:
  - the logged-in settings page, `POST settings/integrations/create-api-token` (`urls.py:77-81`, `bookmarks/views/settings.py:203-221`; session plus CSRF HTML form, token shown via the session, not JSON);
  - the admin site, where `ApiToken` is registered (`bookmarks/admin.py:328-342`);
  - the ORM (the seed below).
- The form login at `/login/` (`urls.py:131-134`) is a Django form post that sets a session cookie; it returns no token. So `mode: login` is not usable. `auth_from_contract` in `proofdeploy/lean/http.py:408-437` would drive `POST <path>` with a JSON body and capture a token from JSON or a header (`http.py:424-427`), and anything else raises `UnsupportedAuth` (`http.py:421-423`).

### Proposed route: a generic `static_token` mode (recommended)
The token value is **generated by ProofDeploy** (D8), exactly like the paperless admin credentials, and then written into the app by the seed.
- Generation already exists: `generate_bootstrap_env` gives every declared name 32 random hex characters (`proofdeploy/setup_auth.py:64-95`, "fresh per side and per run"). 32 chars fits the `key` column (`max_length=40`, `models.py:519`). Both sides get their own value; values join the redaction set (`setup_auth.py:70-77`).
- The lean ScenarioRunner already receives the side's generated values as `env` (`proofdeploy/lean/pipeline.py:379-390`, `lean/run.py:737-739`, passed at `pipeline.py:450,561,741`).
- What the lean layer needs (all in `proofdeploy/`, none in the app); the exact design is the reviewer's call:
  1. `proofdeploy/yml.py`: add a mode, for example `"static_token": {"mode", "token_env"}`, to `_AUTH_MODE_KEYS` (`yml.py:74-88`), and require `token_env` to be listed in `bootstrap_env` (`_validate_bootstrap_env`, `yml.py:370-392`).
  2. `proofdeploy/lean/http.py`, `auth_from_contract` (`408-437`): accept the mode (today `mode != "login"` raises at `421-423`). `AuthSpec` (`399-402`) has a required `login: Step`, so give it an optional `static_token_env` (login absent) and keep `scheme` from `presentation_scheme(fixture)` (`382-396`).
  3. `proofdeploy/lean/generate.py`, `ScenarioRunner`: seed `self._token` from `env[token_env]` at construction (`generate.py:1734`, `1816-1817` already feed `_token` into `TOKEN_VAR`, and `1771-1772` builds `Authorization: <scheme> <token>`); never emit a login step (`_reuses_login`, `1740-1745`; login branch `1825`); and scrub the value (`1795-1797`, already covers env secrets).
  4. `proofdeploy/lean/pipeline.py`, `prove_auth` (`727-750`): when static, run `Scenario([proof])` without `auth.login`.
  5. The non-lean orchestrator/executor provider registration (`Orchestrator._credential_config`, `proofdeploy/orchestrator.py:2757-2804`) would need to refuse or implement the mode if the same contract is ever loaded by `verify`/`measure`. Confirm that this contract is lean-only (open question 3).
- Why this is least invasive: no new generation code, no host-side file read, no change to which values are secret, and the same bootstrap-env mechanism the paperless contract uses (`paperless-ngx__paperless-ngx.yml:72-80`).

### Alternative: Ghost-style `key_file`
The seed writes a generated token to `.pd-auth/api_token`, and a host-side read like `_read_key_file` (`proofdeploy/orchestrator.py:2806-2830`, symlink and size hardening) feeds the lean layer. It needs the same lean changes (1 to 4 above) plus host-side file plumbing into the lean `bootstrap_env` (`lean/run.py:737`), plus joining the value to the redaction set (`orchestrator.py:2793`). Not recommended; the token is already a generated value that ProofDeploy knows.

### Not proposed: no-code workaround through `mode: login`
None exists. No endpoint returns a token from credentials, and the app source may not be changed.

## 3. Seed

- User: the app's own `create_initial_superuser` command (`bookmarks/management/commands/create_initial_superuser.py`):
  - reads `LD_SUPERUSER_NAME` and `LD_SUPERUSER_PASSWORD` (`:15-16`);
  - skips when the name is unset (`:19-23`) or the user exists (`:26-29`);
  - otherwise creates a superuser with `is_staff` (`:31`);
  - with no password set it creates a user with an **unusable password** (`:33-36`). That is fine here: the API client authenticates by token only, and the token is the only credential. Declaring `LD_SUPERUSER_PASSWORD` too would add a credential nobody uses.
- Alternative: `ensure_superuser --username --email --password` (`ensure_superuser.py:13-21`); it needs all three flags and adds an email, so I prefer the first.
- The user's profile row is created by the app's own `post_save` receiver (`bookmarks/models.py:474-477`).
- Token: no linkding command creates a token (grep of `bookmarks/management/commands/`: backup, create_initial_superuser, enable_wal, ensure_superuser, full_backup, generate_secret_key, import_netscape, migrate_tasks). The least invasive way to create one with the app's own model is Django's `manage.py shell -c` calling `ApiToken.objects.create(user=..., name="ProofDeploy", key=<generated>)` (the model keeps a preset key, `models.py:528-531`). This is the seed one-liner in section 1. If a Django built-in is not accepted as "the app's own command", the fallback is a ProofDeploy-owned seed script, as Ghost uses `ghost-seed.js` (open question 2).
- GlobalSettings row: created lazily on the first request by `GlobalSettings.get()` (`models.py:560-565`, called from `bookmarks/middlewares.py:31-35`), so it appears after the first HTTP request, not after seed.

## 4. Fixture values (draft)
```yaml
fixture:
  repo_name: "sissbruecker/linkding"
  seed_note: "The first migrate run on an empty database created the schema and no application records beyond what Django itself generates for every installed model (permission and content-type records). ProofDeploy's setup then ran the app's own create_initial_superuser command, which created one superuser whose username is generated fresh for each run and which has no usable password, and created one API token for that user, named 'ProofDeploy', whose value is generated fresh for each run. The app's per-user settings record for that user was created by the app on user creation, and its global settings record is created by the app on the first request, both with default values. No bookmarks, tags or bundles were created."
  auth_mechanism: "the token is presented in the Authorization header in the form 'Token <token>'"
  auth_scope: "the API token belonging to the generated superuser; ProofDeploy created it during setup and uses the same value for every request, there is no sign-in step"
  flags_note: "application settings are at this version's defaults except the run's deployment choices (SQLite database, production settings module, no background task worker); per-user options such as favicons, preview images, web archive integration and sharing are at their defaults (off); no feature flags were changed"
```
Truth checks:
- Per-user defaults are all off: `enable_favicons`, `enable_preview_images`, `enable_sharing`, `enable_public_sharing` (`models.py:439-442`) and `web_archive_integration` disabled (`models.py:421-426`). HTML snapshots are controlled by the env option `LD_ENABLE_SNAPSHOTS`, default False (`base.py:325`). I have not claimed which defaults are "on".
- "No background task worker": true because the proposed `start` runs no Huey consumer (upstream starts it via supervisord, `bootstrap.sh:30-39`).
- Open question 4: is "generated, no sign-in step" acceptable wording for `auth_scope` given the fixed `auth_mechanism` form? The mechanism sentence is unchanged and still literally true.
- The words "feature list" are avoided; the note names defaults only.

## 5. Sign-in proof
- Authenticated GET that needs a token (expect **200**): `GET /api/user/profile/`. Routing: `bookmarks/urls.py:100` includes `user_router`, registered at `routes.py:297-298`; the view is `UserViewSet.profile` (`routes.py:258-261`); permission is the default `IsAuthenticated` (`base.py:150`, no override in `UserViewSet`). It returns the user's own profile JSON and touches no network and no bookmark data. The trailing slash is DRF's default router behaviour (UNVERIFIED live).
- Alternative proof path: `GET /api/bookmarks/` (`urls.py:93`; default permission, `get_permissions` only relaxes the `shared` action, `routes.py:45-54`); 200 with an empty page on a fresh seed.
- Unauthenticated status: expected **401**. Reasoning: the first configured authentication class is the token class (`base.py:146-149`), and DRF answers 401 with a `WWW-Authenticate` header when the first authenticator defines one; `LinkdingTokenAuthentication` subclasses DRF's `TokenAuthentication`, which defines one. DRF's code is outside this repo, so this is **UNVERIFIED** until the first readiness run. A wrong or malformed token also gives 401 (`api/auth.py:23-28`, `authenticate_credentials` via the parent).

## 6. Network
What reaches the internet on normal API calls, with the setting that turns it off:

1. **`POST /api/bookmarks/` fetches the bookmarked URL itself.**
   - `BookmarkSerializer.create` calls `bookmarks.enhance_with_website_metadata` unless `disable_scraping` is set (`bookmarks/api/serializers.py:136,148-149`).
   - That calls `website_loader.load_website_metadata` (`bookmarks/services/bookmarks.py:76-77`), which GETs the URL with a 10 s timeout (`bookmarks/services/website_loader.py:97-103` via `http_client.get`, `bookmarks/services/http_client.py:305-306`).
   - Failures are swallowed (`website_loader.py:85-86`), and the title or description is only filled when the request left it empty (`bookmarks/services/bookmarks.py:77-81`).
   - **The only off switch is per request**: the query parameter `?disable_scraping` (`routes.py:75`, passed into serializer context at `routes.py:80`; documented at `docs/src/content/docs/api.md:142`). **No environment or settings option disables it** (checked `settings/base.py`; the `LD_*` options are listed there).
   - Guard: `http_client` resolves the host and refuses non-public addresses unless allow-listed (`http_client.py:203-212`; allow-list setting `LD_ALLOWED_INTERNAL_HOSTS`, `base.py:167`). So in a no-network sandbox the call fails fast and the title stays empty; with real network, titles from live pages would make runs nondeterministic.
   - The same service is reached from `refresh_bookmarks_metadata` (`bookmarks/services/bookmarks.py:195-205`, a UI bulk action, not the API).
2. **Favicons** (default provider is a Google endpoint, `base.py:300-301`): gated by the per-user `enable_favicons` (default False, `models.py:441`) and background tasks (`tasks.py:110-114,161-163`). Off by default; and the task is only enqueued (no consumer runs).
3. **Preview images**: per-user `enable_preview_images` default False (`models.py:442`; `tasks.py:116-118`).
4. **Web archive snapshots** (`waybackpy`): per-user `web_archive_integration` default disabled (`models.py:421-426`; `tasks.py:43-50`).
5. **HTML snapshots**: `LD_ENABLE_SNAPSHOTS` default False (`base.py:325`; `tasks.py:246-247`).
6. Global kill switch for 2 to 5: `LD_DISABLE_BACKGROUND_TASKS=True` (`base.py:170`). Not needed with defaults; see section 1.

Consequence for probes: any probe that creates a bookmark triggers item 1 unless it sends `?disable_scraping`. That is a probe-authoring and lean-generator question (open question 5), not a contract field.

## Open questions
1. **Python 3.13 provisioning**: `requires-python >=3.13` (`pyproject.toml:6`). Can the sandbox image provision 3.13 and compile uwsgi (a main dependency, `pyproject.toml:17`) during `uv sync --frozen --no-dev`? Or should the contract give a build step that excludes it? (I cannot say how the harness picks the interpreter; I did not read `provisioning.py` beyond grep.)
2. **Seed via `manage.py shell -c`**: Is a Django built-in running the app's own `ApiToken` model acceptable as "the app's own command", or do you want a ProofDeploy-owned seed script (as Ghost's `ghost-seed.js`)? No linkding command creates a token.
3. **Lean-only?** The new `static_token` mode needs lean changes (`yml.py`, `lean/http.py`, `lean/generate.py`, `lean/pipeline.py`). Is the contract only ever loaded by the lean tool? If not, the non-lean paths (`orchestrator.py:2757-2804`) must refuse or implement it. Also a Master decision: a new auth mode and edits to those files need a work order and (if any of them is pinned) a signed registration; I did not check pins beyond the CLAUDE.md list.
4. **`auth_scope` wording** for a static (never signed-in) token; the fixed `auth_mechanism` sentence is unaffected.
5. **`?disable_scraping`**: should the lean generator append it to bookmark-create requests, or should the sandbox be network-less so the default scrape fails identically on both sides? If the sandbox has real network, title/description of created bookmarks is nondeterministic across runs.
6. **Settings module**: I recommend `bookmarks.settings.prod` over the default dev (DEBUG on). Confirm, or choose dev for closeness to upstream's own `make run`-style development.
7. **Unauthenticated status 401** and the `/api/user/profile/` trailing-slash route are DRF behaviour and need the first live run to confirm.
8. **No password declared** for the superuser (unusable password). Confirm the lean tool never needs a form login for linkding; if it did, a second declared name `LD_SUPERUSER_PASSWORD` is all the seed needs (`create_initial_superuser.py:33-36`).
9. **Fidelity**: no ICU extension in a pip-style run (case-insensitive search/ordering differs from the Docker image, `base.py:295-297`). Accept for a differential before/after run?
