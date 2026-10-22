# Lean selection — linkding (R-LEAN-SEL + A1), walk 3

- Run from main `991153b0353875ea58caf27874d1658699a7519f` (merge of PR #181), checkout HEAD = main; script sha256 in the journal header. App sissbruecker/linkding @ `27b7303b`, app path `.`. Layout files (A1): `bookmarks/settings/prod.py`, `pyproject.toml`, `uv.lock`.
- Outcome: **selected** fix `71d62f48`, fix^ `8c2afb7e`, B `ffc1a690` (2026-01-01, tag v1.45.0 created 2026-01-06), B^ `38d450a9`. Check (d): B pair READY in readiness run `39726005781`, fix pair READY in `39726605839`.
- Walk 3: 51 commits walked; rejected a=42, b=3, c=2, L2=1, L3=2.
- Earlier walks (same deterministic order), evidence in `evidence/linkding-walk-1-2026-10-21/` and `evidence/linkding-walk-2-2026-10-21/`. Walk 1 stopped at (d): B pair predates the contract layout → R-LEAN-SEL A1 signed. Walk 2 stopped at (d): python 3.14 image, no cp314 wheel in the 2026-01 lock → contract `runtime_line: "3.13"` (PR #181).
- Blindness: the record and walk journal here have the `subject` field stripped. The reviewer read only SHAs, dates, check letters, counts and readiness classes.
