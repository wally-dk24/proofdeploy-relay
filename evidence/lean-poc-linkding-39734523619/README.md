# Lean PoC linkding — run 39734523619 (main 58bb1dfe; predictions relay f07522bc)

Result: 4/4 INCONCLUSIVE, 0 errors, 0 FAIL, 0 model calls, 0 tokens, 1936 s.
- linkding-bug-71d62f48: `not_observable_over_http`. 3162 "executable" changed lines, 0 reached by a request; 28 impact scenarios, 25 requests hit mapped impacted endpoints (74 DRF operations indexed). The unreached lines, by bucket (reviewer, no file names): templates 2773, non-Python (.lock/.toml/.md) 174, server-rendered UI views 145, other Python 36.
- clean e50239cd: `change_not_exercised` (36 lines, 2 reached). clean 0837b518: `not_observable_over_http` (33, 0 reached). clean 0490013e: `change_not_exercised` (49, 2 reached).
Stop rule (predictions): the bug unit was not reached. Stop and report.
Artifact: see artifacts.tsv (name, id, sha256 digest from GitHub).
