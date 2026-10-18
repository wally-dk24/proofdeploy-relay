# WO-6 follow-up fact-finding (D229 item 3)

**Date:** 2026-10-18. **Type:** report only — no code changes, no model calls, no dispatches.
**Purpose:** facts for the reviewer's proposal to Master covering (a) a selection rule excluding
upgrade-path-only fixes, (b) a route map in the author's bundle, (c) an author bake-off.
**Contamination guard:** for every unit not yet measured, only the fix commit's *changed-path
list* was retrieved (GitHub API `commits/<sha>` file names). No held-out fix's diff, tests, or
commit message was opened. The paperless unit is complete and revealed by the reviewer, so its
paths are listed in full.

Sources for the unit lists (proofdeploy repo, main `e8bf530`): `docs/evaluation/dev-set-v2-2026-10-09.md`
(train), `docs/evaluation/held-out-set-complete-2026-10-09.md` + `held-out-addendum-1.md` +
`held-out-addendum-2.md` (held-out), `docs/evaluation/control-registration-v2.md` (registered
controls). Scope notes: the 2026-10-09 v1 draft lists (Payload-based held-out) were thrown out by
the v2 changelog and are not audited. The held-out target of 10 bugs + 10 cleans (Addendum 1) has
no completed list committed in the repo as of this date; the completed registered held-out list
is the 4 bugs + 4 cleans below.

---

## (a) Selection / upgrade-only audit

**Classification rule applied:** a fix is *upgrade-only* if every changed path is a
migration/upgrade-path file — any path segment `migrations`, alembic `versions`, or a file name
containing `migrat`. "Needs pre-existing DB state" is judged from the path classification plus
the unit's registered behavior metadata only.

### Held-out bugs (all nestjs/nest; NOT yet measured)

| Unit | Fix SHA | Changed paths | Upgrade-only | Needs pre-existing DB state |
|---|---|---|---|---|
| H1 | `02ee6d44` | `packages/common/pipes/parse-enum.pipe.ts`; `packages/common/test/pipes/parse-enum.pipe.spec.ts` | not | No — runtime pipe code; registered behavior (ParseEnumPipe accepts numeric strings) is observable on a fresh instance |
| H2 | `1d1dbf7b` | `packages/core/discovery/discoverable-meta-host-collection.ts`; `packages/core/injector/instance-loader.ts`; 2 spec files under `packages/core/test/discovery/` | not | No — runtime discovery/injector code; observable on a fresh instance |
| H3 | `21648746` | `packages/core/middleware/middleware-module.ts`; `packages/core/test/middleware/middleware-module.spec.ts` | not | No — runtime middleware code; observable on a fresh instance |
| H4 | `1685a435` | `packages/platform-express/adapters/express-adapter.ts`; `packages/platform-fastify/adapters/fastify-adapter.ts`; 2 adapter spec files | not | No — runtime adapter code; observable on a fresh instance |

### Held-out clean diffs (all nestjs/nest; NOT yet measured)

| Unit | Commit | Changed paths (summary) | Upgrade-only | Needs pre-existing DB state |
|---|---|---|---|---|
| HO clean 1 | `a9bf20b` | `packages/core/exceptions/base-exception-filter.ts`; 1 spec file | not | n/a — clean diff (no defect); runtime code |
| HO clean 2 | `abce26ec` | 21 exception classes under `packages/common/exceptions/`; 1 spec file | not | n/a — clean diff; runtime code |
| HO clean 3 | `45485b54` | `packages/core/injector/injector.ts`; 4 files under `integration/scopes/` (e2e spec, services, controller, module) | not | n/a — clean diff; runtime code |
| HO clean 4 | `c921a60e` | `packages/platform-fastify/adapters/fastify-adapter.ts`; 1 spec file | not | n/a — clean diff; runtime code |

### Train bugs (dev-set v2; already used for skill drafting)

| Unit | Repo | Fix SHA | Changed paths | Upgrade-only | Needs pre-existing DB state |
|---|---|---|---|---|---|
| T1 | codeigniter4/CodeIgniter4 | `f5e463b9a3e986389ce285963e51a7f1fab6559f` | `system/Database/BaseBuilder.php`; `system/Database/OCI8/Builder.php`; `system/Database/Postgre/Builder.php`; 2 test files; changelog + docs | not | No — query-builder code; registered behavior (bind substitution in generated DELETE SQL) is observable from fresh queries |
| T2 | fastify/fastify | `179619c8e99d54924309b9933402151ef9af37e0` | `lib/validation.js`; `lib/warnings.js`; `docs/Reference/Warnings.md`; 1 test file | not | No — runtime validation code |
| T3 | fastify/fastify | `93c239a380f3f2778bb7565fcdf777e91cfe1fbc` | `lib/four-oh-four.js`; 2 test files | not | No — runtime routing code |
| T4 | django/django | `ebcb13b327301f28cbc6cd5e4988a719f00575aa` | `django/contrib/gis/db/models/fields.py`; `django/contrib/gis/gdal/raster/source.py`; 3 docs files; 3 release notes; 1 test file | not | No — runtime GIS lookup code |
| T5 | LMCache/LMCache | `449b94c17f66973081308f6e050027ced3f0ab0f` | `lmcache/v1/internal_api_server/common/run_script_api.py`; `lmcache/v1/config.py`; `lmcache/v1/multiprocess/config.py`; 5 docs files; 3 test files | not | No — endpoint/config code; observable on a fresh instance |
| T6 | bottlepy/bottle | `b73bd1db5b7a915cf6a78656955c4059f58195ae` | `bottle.py`; `test/test_sendfile.py` | not | No — runtime conditional-request code |
| T7 | honojs/hono | `00ee875728e2369b98a9ab7dda0690b77afedcff` | `src/router/linear-router/router.ts`; `src/router/common.case.test.ts` | not | No — runtime router code |

### Train clean diffs (dev-set v2)

| Unit | Repo | Commit | Changed paths (summary) | Upgrade-only | Needs pre-existing DB state |
|---|---|---|---|---|---|
| C1 | django/django | `da6567da7ea0e844c9a880e02c7b34e1a184edf8` | `django/views/generic/base.py`; 2 docs files; 1 test file | not | n/a — clean diff; runtime code |
| C2 | honojs/hono | `1f707c56378a4497bc0a6a44fef5f5a8517fdf6b` | `src/middleware/method-not-allowed/index.ts` (+ test); `jsr.json`; `package.json` | not | n/a — clean diff; runtime code |
| C3 | fastify/fastify | `f376f6082b5ddfe5ea96fdd3d98a66377b6bb4c2` | `lib/route.js`, `lib/reply.js`, `lib/request.js`, `lib/context.js`, `lib/errors.js`, `lib/symbols.js`, `lib/config-validator.js`, `fastify.js`, `build/build-validation.js`, `fastify.d.ts`, 4 docs files, 5 type/test files | not | n/a — clean diff; runtime code |

### Registered controls (control-registration v2; clean units)

| Unit | Repo | Commit | Changed paths (summary) | Upgrade-only | Needs pre-existing DB state |
|---|---|---|---|---|---|
| CTL1 | vendure-ecommerce/vendure | `4cbbc790118786a17721954c3a4d739810bd2632` | `packages/core/src/service/helpers/order-calculator/order-calculator.ts` (+ spec, e2e spec); promotion action/entity; 1 docs file | not | n/a — clean diff; runtime code |
| CTL2 | vendure-ecommerce/vendure | `b19ab4dc76e4f8521e02258b8afa26f615142595` | search-strategy files under `packages/core/src/plugin/default-search-plugin/search-strategy/` (mysql/postgres/sqlite strategies, utils + spec); 1 e2e spec | not | n/a — clean diff; runtime code |
| CTL3 | directus/directus | `dc922b790a15949e8abfce9c1f4523d4e5f89492` | `api/src/services/comments.ts` (+ test); `.changeset/` note | not | n/a — clean diff; runtime code |
| CTL4 | directus/directus | `a5da59da94bb33c8d644df93a21403eca4c1a4e7` | `api/src/services/files.ts` (+ test); `.changeset/` note | not | n/a — clean diff; runtime code |
| CTL5 | TryGhost/Ghost | `5d482965b7f5ed05ffef956d0eaed8b8a51af0ef` | `ghost/core/core/server/data/exporter/exporter.js`, `table-lists.js`; `ghost/core/core/server/data/importer/importers/data/settings-importer.js`; 3 test files | not | n/a — clean diff; runtime code (import/export services, not migration files — no `migrations` segment or `*migrat*` name) |
| Backup 1 | TryGhost/Ghost | `37e161932221fe12c1c626a7238c3669c22aec2a` | `ghost/core/core/server/api/endpoints/authentication.js`, `users.js`, serializers, `services/users.js`; admin app files; 3 test files | not | n/a — reserve unit, never run |
| Backup 2 | directus/directus | `ea25ba63db11d4e25c0b1a4e5b8926a64dadbfee` | `api/src/permissions/modules/fetch-global-access/fetch-global-access.ts` (+ test); `.changeset/` note | not | n/a — reserve unit, never run |

### WO-6 measured unit (complete; reviewer-revealed)

| Unit | Repo | Fix SHA | Changed paths | Upgrade-only | Needs pre-existing DB state |
|---|---|---|---|---|---|
| WO-6 | paperless-ngx/paperless-ngx | 
...[truncated 6600 chars]