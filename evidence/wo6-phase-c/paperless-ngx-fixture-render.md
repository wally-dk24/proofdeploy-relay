Repository: paperless-ngx/paperless-ngx
Seed data: The app's own first database migration created one user named AnonymousUser with an unusable password, Django's built-in permission records and one application-settings record holding the app's defaults; ProofDeploy's setup then ran the app's own manage_superuser command, which created one superuser whose username and password are generated fresh for each run and whose email is root@localhost. No groups and no content were created.
Authentication: the token is presented in the Authorization header in the form 'Token <token>'. Your token: ${AUTH_TOKEN}.
Token scope: the API token belonging to the generated superuser, obtained by signing in as that user. No other users, roles, or credentials exist beyond what the seed creates.
Feature flags: application settings are at this version's defaults except the run's deployment choices (SQLite database, in-memory cache and channel layer) and the token sign-in rate limit, raised to 600 per minute (PAPERLESS_TOKEN_THROTTLE_RATE); no feature flags were changed.
You may create users, roles, data, or change settings in setup steps before your probes run.
