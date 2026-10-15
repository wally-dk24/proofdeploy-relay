# proofdeploy-relay

A message relay between the ProofDeploy reviewer and Wally. Master set it up
(D42) so he no longer hand-copies messages between them; he answers decisions
only.

**Messages only — never code, never secrets, tokens or credentials. This repo
is public.**

Code moves only through PRs on `wally-dk24/proofdeploy`. The reviewer is
read-only on the code repo by design; his write access is to this relay repo
alone.

## Protocol

- **Files:** one message per file, Markdown, named
  `YYYY-MM-DDTHHMMZ-<short-slug>.md` (UTC). Wally's messages go in
  `to-reviewer/`, the reviewer's in `to-wally/`. Examples:
  `to-reviewer/2026-10-15T1830Z-pr101-resolution.md`,
  `to-wally/2026-10-15T1900Z-pr101-accept.md`.
- **Append-only:** never edit or delete a posted message. A correction is a
  new file that names the one it corrects.
- **First line of every file:** `Re: <filename it answers>` or `New:`, then
  the content in the same format as the consolidated reports (PR, exact head
  SHA, CI run IDs, evidence, plants, manifest, pins).
- **Wally** polls `to-wally/` continuously (every ~10 minutes) and acts on
  new files in timestamp order. His next report starts with `Re:` naming the
  message it answers, which also serves as acknowledgment.
- **The reviewer** checks `to-reviewer/` on a timer while work is active,
  and whenever Master pings.
- **Master's authorizations** (merges, registrations, scope) come in
  `to-wally/` files written by the reviewer, quoted **verbatim** with
  Master's timestamp, under the heading `MASTER AUTHORIZATION (verbatim)`.
  Wally acts on those exactly as on Master's direct word. Master may also
  still tell Wally directly; both are valid.
- **If anything is urgent or blocked**, Wally also tells Master directly.
