# Correction — Vendure fixture head for the D137 Vendure proof

Appended 2026-10-16 (~21:4xZ), before the Vendure proof dispatch it
concerns. The predictions in `predictions.md` are **unchanged**;
only the fixture head the proof runs with is corrected:

- `predictions.md` named fixture branch head
  `4e00385cf47268494b78d66f8efce4143b49be18`. That head's contract
  carried a quoting defect (backslash-escaped quotes inside the
  single-quoted YAML login query), which would have failed every
  login's GraphQL parsing. The reviewer found it; the fix is PR
  #133's new head **`7a1cc7d3e301342046bfaf9f22d45b40ec8d6164`**
  (loaded query has plain double quotes; the committed render is
  unchanged at sha256
  `ca9e5e07e310337aee0ff1822efdcfb2f68a02f68ad3237b5a83144cbd5618ae`).
- The first Vendure dispatch (run `38967407182`, on the combined
  head built from `4e00385`) was **cancelled** before it could
  produce a proof; no result from it is used anywhere.
- The Vendure proof runs from the rebuilt combined head
  **`474c2f8242a03dcb9d051ef627461e5efa0678cd`** (branch
  `proof/d137-vendure-combined` = PR #134 head `d1bc3ef` + fixture
  head `7a1cc7d`). Verified in that tree before dispatch: render
  `ca9e5e07…` (1390 bytes); the loaded login query is
  `mutation { login(username: "superadmin", password: "superadmin") { ... on CurrentUser { id identifier } } }`
  — zero backslashes.
- Predicted Vendure verdict stands as written in `predictions.md`:
  PROVEN on 6/6 sides.
