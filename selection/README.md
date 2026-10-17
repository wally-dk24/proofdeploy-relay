# WO-6 bug-selection record (paperless-ngx)

`wo6-bug-selection-record.json` — the output record of the single
live bug-selection run (REG-C item 7 as implemented by D169;
script `scripts/wo6/regc_bug_select.py` in wally-dk24/proofdeploy).

Provenance:

- Run executed from main `63fdb628015395ce224ad38030c734c59a1b8f3a`
  (merge commit of PR #141); dispatch ref's merge-base with main is
  main itself. Script sha256
  `c6cb1548837be3a5c3c1451fd37b03fd75201af02af68d4ef9eb77c1fa16bb4b`
  (journal header). App: paperless-ngx/paperless-ngx @
  `0cb2cdbfdd91a2078236f8851cd310261284591c`, app.path `src`.
- Journal: the run's D74 journal (header/walk/attempts/events)
  recorded 8 rejected commits, then stopped at check (d) on the 9th
  (environment class: the local VM cannot create the
  userns-remapped sandbox; the measurement gate refused
  fail-closed, no opt-out used). The stop was ruled correct (D194);
  the commit was not rejected.
- Disclosure: the journal persisted the 8 rejections and the (d)
  stop event, but the stopped candidate's (a)–(c) entry was never
  written (the stop interrupts the evaluation before its walk line
  is appended). The selected entry in this record was therefore
  re-derived after the fact by running the run script's own
  `evaluate_commit` — the script file byte-identical to the journal
  header's sha256 — against the run's own app clone; its walk order
  reproduced the journal's 8 entries exactly before the candidate
  was evaluated. No other code path was used.
- Check (d) was decided by the verified manual GitHub dispatch
  (D195 A2, ruled D194/D198): readiness run `39079057983` from ref
  `feat/d195-readiness-pair-inputs` @ `bfae843`, both sides READY
  (podman 4.9.3, userns-remap, network none+socket, contract-store
  sha256 `eb60559b…`); D61 pack at `evidence/39079057983/` in this
  repo. Rows verified first-hand by the reviewer and accepted
  (D200); this record was then written and committed under D200,
  before any author (blind) run.
- Contamination guard: the record carries hashes, dates, check
  outcomes and provisioning evidence only — no diff, no test
  content, no message bodies were opened or recorded at any point.
