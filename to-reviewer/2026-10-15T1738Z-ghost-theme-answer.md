Re: 2026-10-15T1731Z-pr101-merge-check.md

Two items: (1) the owed CI results, (2) the Ghost theme answer (plan 1.11 evidence), which you flagged as critical-path.

## CI results owed

- **On `516df9d` (post-#101-merge main):** ci `38777770754` IN PROGRESS and sandbox-tests `38777770799` QUEUED at my last direct read (13:37 EDT) — the runner queue is deep today; final results fold into my next message. Neither has failed.
- **On `5726c96`:** sandbox-tests `38769445558` **SUCCESS** (re-stating from my merge-check post, as requested).

## Ghost theme answer (plan 1.11, report-only — no code or contract changes)

# Plan 1.11 evidence — Ghost: does `GET /` serve 200 when the theme submodule dirs are empty?

**Report only. No code changed, no contract touched, no workflow dispatched.**

**Question (reviewer's order):** Ghost's contract readiness path is `GET /`. In every ProofDeploy
snapshot, `ghost/core/content/themes/casper` and `ghost/core/content/themes/source` are absent
(empty directories — submodules are never fetched). Does `ghost dev` at the three registered
SHAs serve `GET /` with 200 when the active theme directory is missing/empty?

**SHAs examined** (TryGhost/Ghost, fetched fresh for this report):

| Side | SHA | Ghost version | gscan pin |
|---|---|---|---|
| C5 | `5d482965b7f5ed05ffef956d0eaed8b8a51af0ef` | 6.64.0-rc.0 | 6.6.0 |
| fix^ | `93e0084723cdaf22d656bcd4811b87433e6b3875` | 6.69.1-rc.0 | 6.6.1 |
| fix | `f67640b2c18c76abd9cd3f6d19d6ae93327b42fa` | 6.69.1-rc.0 | 6.6.1 |

## Verdict (all three SHAs): NO — `GET /` returns HTTP 500, never 200

| SHA | Verdict | Mechanism |
|---|---|---|
| C5 `5d482965` | **FAIL — 500** | Active theme `source` is an empty dir → fatal theme validation → theme never serves; site requests error out (chain below) |
| fix^ `93e00847` | **FAIL — 500** | Identical — every file in the theme chain is byte-identical to fix |
| fix `f67640b2` | **FAIL — 500** | Identical to fix^ |

**Prediction:** with the current snapshot construction (submodule contents never fetched) and the
current contract readiness path (`GET /`), **no Ghost side can reach READY at any of the three
SHAs**. The server itself boots and stays up (boot deliberately tolerates a missing active theme
so the admin panel works) — the failure is at request time, on the frontend only.

## The chain, with citations

All paths below are under `ghost/core/` in the checkout. Line numbers are C5's unless marked;
the fix^/fix files are identical or differ only cosmetically (see "Per-SHA differences").

### 1. The snapshot state is exactly "empty theme dirs"

`.gitmodules` (repo root, C5) declares both themes as submodules
(`ghost/core/content/themes/casper` → `../../TryGhost/Casper.git`;
`ghost/core/content/themes/source` → `../../TryGhost/Source.git`; both `ignore = all`).
A depth-1 fetch of each SHA reproduces the snapshot state: both directories exist and contain
zero files.

### 2. A fresh instance's active theme is `source` — one of the empty dirs

`core/server/data/schema/default-settings/default-settings.json` — `"active_theme"` has
`"defaultValue": "source"` (C5: lines 298–303; fix^/fix: lines 314–318; `flags: "RO"`).
Settings are seeded from these defaults on a fresh database, so the readiness path's fresh
instance boots with `active_theme = source`.

### 3. Boot: the theme load runs, validation is fatal, and boot continues anyway

- `core/server/services/themes/index.js:14-21` — `init()` reads `settingsCache.get('active_theme')`
  and calls `activate.loadAndActivate(themeName)`.
- `core/server/services/themes/activate.js:18-51` — `loadAndActivate`:
  - loads the theme (`loader.js:13-17` → `packageJSON.readPackage`). For an **existing but
    empty** directory this does *not* throw: `core/server/lib/package-json/package-json.js`
    `processPackage` (lines 32–52) swallows the missing-`package.json` parse failure and sets
    `pkg['package.json'] = null`. (The `NotFoundError` "Package not found" at line 118–119 fires
    only when the directory itself is absent.)
  - validates via gscan: `core/server/services/themes/validate.js:47-74` (`check()` →
    `gscan.check(theme.path, …)` → `gscan.format(…)`), and
    `canActivate` is `!checkedTheme.results.hasFatalErrors` (validate.js:36–38).
  - **Empirical anchor:** running the pinned gscan's `check` + `format` (the exact calls
    validate.js makes) against the empty `source/` directory gives
    `hasFatalErrors: true` — fatal errors **GS020-INDEX-REQ** (no `index.hbs`) and
    **GS020-POST-REQ** (no `post.hbs`), plus 19 non-fatal GS010 package.json errors;
    `templates.all = []`, `partials = []`. Reproduced with gscan **6.6.0** (C5's pin) and
    **6.6.1** (fix^/fix pin), same result. Control run against a real theme fixture:
    `hasFatalErrors: false`, 0 errors.
  - So `canActivate` is false; activate.js:29–32 logs "The currently active theme "source" has
    fatal errors." Line 40 nonetheless calls `activator.activateFromBoot(...)`, and
    `core/bridge.js:79-99` (`activateTheme`) wraps the actual activation in try/catch and only
    logs a failure. The catch-all at activate.js:41–50 logs any other error and boot proceeds —
    per the in-code comment (activate.js:43–47): *"CASE: active theme is missing, we don't want
    to exit because the admin panel will still work."*
  - Every activation step readable from source is tolerant of the empty theme (e.g.
    `core/frontend/services/theme-engine/config/index.js:7-13` handles a null package.json;
    theme i18n init tolerates missing locale files), so the end state is one of the two in §4 —
    both end at a 500 for `/`. What is certain from source: **no code path makes the empty
    theme servable, and no code path stops the server from booting.**

### 4. Request time: `GET /` cannot render

The site app installs the theme middleware for the whole frontend before mounting the site
routes: `core/frontend/web/site.js:149` (`siteApp.use(themeMiddleware)`; first element is
`ensureActiveTheme`, per `core/frontend/services/theme-engine/middleware/index.js:1-5`).

- **End state A — no active theme object** (any activation step failed):
  `core/frontend/services/theme-engine/middleware/ensure-active-theme.js:15-26` — if
  `activeTheme.get()` is falsy, the request is passed
  `new errors.InternalServerError({message: 'The currently active theme "source" is missing.'})`.
  In-code comment (lines 16–18): *"This is the one place we ACTUALLY throw an error for a
  missing theme as it's a request we cannot serve."* → **500**.
- **End state B — a hollow active theme** (activation "succeeded" with zero templates):
  the middleware passes and mounts the empty dir as the views root
  (`core/frontend/services/theme-engine/active.js` `mount()`, lines 122–134). Template
  selection for `/` goes through `core/frontend/services/rendering/templates.js:123-150`
  (`pickTemplate`): no candidate matches `hasTemplate()` (templates list is empty), so it
  falls back to `'index'`; express-hbs then fails to look up `index.hbs` in the empty views
  directory and the error propagates → **500**.
- Either way the 500 is actually renderable without a theme: the site error handler
  (`core/frontend/web/middleware/error-handler.js`, `handleThemeResponse`) falls back to
  Ghost's built-in `core/server/views/error.hbs` when no theme is mounted, and to a plain
  `res.status(500)` body if even that fails. So the observed response is a real HTTP 500
  error page — not a hang, not a connection failure.

### 5. Setup state changes nothing for `/`

The readiness path boots a **fresh** instance (no owner created). Nothing in the site app
gates frontend requests on setup state: `core/frontend/web/site.js` and the frontend routes
contain no setup check or redirect (the only setup endpoints live in the admin API,
`core/server/web/api/endpoints/admin/routes.js`). Owner creation happens in the admin app and
is irrelevant to `/`. A fresh instance therefore hits the §4 wall on its very first request,
before and after admin setup alike.

## Per-SHA differences

- **fix^ → fix:** `git diff` over the entire theme-relevant path set (themes service,
  package-json lib, theme-engine, site app, rendering, error handler, default settings, API
  routes/endpoints, bridge, custom-theme-settings) is **empty** — byte-identical behaviour.
- **C5 → fix^:** the same path set differs only in cosmetic lint-style edits
  (`Object.prototype.hasOwnProperty.call` → `Object.hasOwn`, `let` → `const`,
  `_.isArray` → `Array.isArray`, an unused-import removal) in peripheral files, plus the
  gscan pin (6.6.0 → 6.6.1). The decisive files — `themes/activate.js`, `themes/index.js`,
  `ensure-active-theme.js`, `package-json.js`, `api/endpoints/authentication.js`, and the
  `active_theme` default — are unchanged, and both gscan pins give the identical fatal
  result on the empty directory (empirically verified, §3).

## If `/` needs the theme — a readiness path that doesn't

**Recommended alternative: `GET /ghost/api/admin/authentication/setup/`**

- Route: `core/server/web/api/endpoints/admin/routes.js` — C5:446
  `router.get('/authentication/setup', http(api.authentication.isSetup));`
  (fix^/fix: line 451). Note the GET carries **no auth middleware** — contrast the PUT on the
  adjacent line, which has `mw.authAdminApi`.
- Endpoint: `core/server/api/endpoints/authentication.js:120-139` — `isSetup` declares
  `permissions: false`; it returns HTTP 200 JSON in both states: `{status: true}` once an
  owner exists, or `{status: false, title, name, email}` on a fresh instance. Its query
  touches only config and the users table (`auth.setup.checkIsSetup()`).
- Theme independence, from code: the API app (`core/server/web/api/`) contains **no**
  theme-engine or theme-middleware reference at all; `themeMiddleware` exists only inside
  the site app (`core/frontend/web/site.js:149`). This route is served by a sibling app and
  never passes through `ensureActiveTheme`.
- Secondary, weaker options: `/ghost/` (the admin app is likewise theme-free in code, but
  its client assets are build artifacts — `apps/admin` — not committed source, so a 200
  depends on the dev build having produced them); `/favicon.ico` and other public files are
  served before the theme middleware (`site.js:66`, `:69` precede `:149`) and would return
  200, but they attest only that static file serving is up.

**What changing the readiness path would mean (no change made — for the reviewer to rule):**
the Ghost contract's readiness check would move from the public frontend to an admin-API
status route. Readiness would then attest "the process boots and the API/DB layer answers"
rather than "the site serves pages". Under the current snapshot construction no
theme-dependent path can pass at these SHAs, because the theme content is absent from the
snapshot itself — that absence is a property of our snapshots (submodules never fetched),
not an upstream Ghost defect.

## Method notes

- Checkouts: depth-1 fetches of the three SHAs into scratch worktrees; scratch deleted after
  this report was written. No repository files, contracts, or workflows were touched.
- gscan experiment: `gscan.check(<empty source dir>, {checkVersion: 'v6'})` +
  `gscan.format(…, {onlyFatalErrors: false})` — the exact calls in
  `core/server/services/themes/validate.js:50-79` — run with gscan 6.6.0 and 6.6.1 (the
  versions pinned in `ghost/core/package.json` at the respective SHAs). Ghost major version
  at all three SHAs is 6, hence `checkVersion: 'v6'`.
