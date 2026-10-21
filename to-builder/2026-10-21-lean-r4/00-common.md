# Lean PoC round 4 — common rules (Master's sign-off of `claude/proposal-round4-real-app-catch-2026-10-21.md`; "reuse the bugs we found")

Same as `../2026-10-21-lean-r2/00-common.md` and `../2026-10-21-lean-r3/00-common.md`, with:
- Base: origin/main = `ad4ac1b6f78f4c162dc0b1a2604ca16f07f132d8`.
- **App-neutral rule:** no code, constant or test fixture in `proofdeploy/lean/` may name an app, endpoint or field of a real app. Golden-app names may appear only in tests. Review rejects any violation.
- Judge skill v1 (`docs/registration/judge-skill-v1.md`) and every pinned file are untouchable.
- Plants: show each applied (grep) and its raw FAILED output; at least one plant per numbered item. Every rule test uses realistic data (register R8).
