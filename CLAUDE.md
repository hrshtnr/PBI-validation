# CLAUDE.md

Standing instructions for this repository. Read this before any work in this
folder, every session.

---

## Required reading

These two files are part of the standing instructions, not background material.
The lines below are Claude Code import directives — each pulls the file into
context automatically at session start, so a new session has them without being
told.

@imp_docs/powerbi-validation-prompt.md

@imp_docs/source-notes.md

The first is the build spec. The second is the live answer sheet for source
type, servers, databases, tables and columns — it is the only sanctioned place
for those answers, and it is where new answers go.

If a session reaches this line and has not seen the contents of both files, the
import did not fire. Read them before doing anything else, then say so.

---

## What this project is

A **Power BI validation harness**, not a reporting or analytics product.

It is distributed as one local copy per person. One canonical copy lives here in
the repo. Local copies are never merged back. Everyone already has their own
credentials to every data source and refreshes under their own identity.

The whole point of the design is that a user can **repoint a page at a different
table, database or server without opening Power Query and without editing the
file**. Every decision should protect that property.

---

## Structure

- One report page maps to exactly one data source. Page 1 → Source 1, Page 2 →
  Source 2, and so on.
- **No overlap between pages.** A source belongs to one page only. One page
  breaking must never affect another page.
- Within a single page's source family, table and column naming conventions are
  stable — tables in that family share the same column names and types.
- Table names differ between sources and must be swappable from settings.

---

## Hard rules

1. **Never hardcode** a server, database, or table name in any query step.
   Reference parameters: `Source{[Name = P1_Table]}[Data]`.
2. **Never use static column lists.** No click-to-rename steps, no
   `Remove Other Columns` with a literal list, no hardcoded type-transform pairs.
   Derive from `Table.ColumnNames(Source)` instead.
3. **Fail loudly.** Do not wrap steps in `try ... otherwise null` in a way that
   hides a broken or changed source. A wrong number is worse than a failed
   refresh.
4. **No row-level security.** It provides no protection — anyone holding the file
   can open the model and remove it.
5. **No star schema, no semantic layer, no auto-detected relationships.** Keys
   will not match across sources. Leave tables unrelated unless a page genuinely
   needs a join.
6. **No credentials anywhere** in this repo. Not in M, not in TMDL, not in docs,
   not in the log below.
7. **Save as `.pbip`**, never `.pbix`. Model as TMDL, report as JSON. Binary files
   cannot be diffed or reviewed.

---

## Per-page parameters

Every page gets these three, exposed via Transform data → Edit Parameters:

- `P<N>_Server`
- `P<N>_Database`
- `P<N>_Table`

Every page must display its live parameter values, the last refresh timestamp,
and the project version as card or text visuals. The expected failure mode is
someone validating against a stale copy or the wrong table and reporting a
discrepancy that does not exist. Each page must make its own provenance obvious.

---

## Smoke test — run after any query change

Point a page's `P<N>_Table` at a second table in the same source family and
refresh. It must complete with no query errors and no broken visuals.

This is the critical assumption in the entire design. If it fails, stop and say
so rather than working around it.

---

## Working agreement

- **Power BI Desktop must be closed** before editing project files. Desktop
  overwrites external edits on save. Ask me to close it if unsure.
- Ask before assuming source type (SQL Server, Fabric, Excel, folder, API). The M
  differs substantially. Current answers live in `imp_docs/source-notes.md`.
- Ask before scaffolding a number of pages. Do not guess.
- Prefer editing existing queries over regenerating them.
- **Do not build unless I tell you to build.** Reading, reviewing, answering
  questions, and pointing out problems are always fine. Creating the `.pbip`,
  writing M or TMDL, or scaffolding pages waits for an explicit instruction.
  A request to read or analyse is not permission to start generating.
- **Do not `git commit` or `git push` unless I tell you to.** Leave changes in
  the working tree and say what is uncommitted. This overrides the "commit the
  log update in the same commit" rule below: write the log entry, then stop.

---

## Change log protocol — REQUIRED

**Append to the log below after every piece of work. Do not skip this and do not
rewrite earlier entries.**

This log is the chain of reasoning for the project. Local copies are distributed
to people who were not in the room; the log is the only record of why the file
looks the way it does. Treat it as a deliverable, not bookkeeping.

For each entry record:

- **Date**
- **Prompt** — what I asked, in my words, quoted or closely paraphrased. Not your
  summary of what you decided to do.
- **Changed** — files touched and what actually changed in them.
- **Why** — the reason, especially if a rule above was bent or an alternative was
  rejected.
- **Verified** — did refresh succeed, did the smoke test pass, or was it not run.
- **Open** — anything left unresolved, assumed, or needing my input.

Rules for the log:

- Append at the **bottom**, newest last. Never edit or tidy prior entries — a
  wrong decision that was later reversed is the most useful thing in here.
- If I give a correction or reverse an earlier instruction, log it as its own
  entry that names what it supersedes. Do not silently amend the original.
- If a change was abandoned or rolled back, log that too, with the reason.
- If a session produced no file changes but a decision was made, log the
  decision.
- **Do not log noise.** The trail records work and decisions, not chatter.
  Skip: acknowledgements, "ok", "go on", "read this", clarifying questions that
  changed nothing, retries of the same request, and anything that produced
  neither a file change nor a decision. One real entry beats five filler ones.
  When in doubt, ask whether a reader six months from now needs it.
- **Never transcribe profanity.** Expect it in the prompts; keep it out of the
  log. Record the technical content of what was asked, in neutral wording. Do
  not quote the swearing, do not asterisk it, do not note that it happened.
- When I do ask for a commit, put the log update in the same commit as the
  change it describes.

---

# Change log

<!-- Append new entries below. Newest last. Never edit entries above. -->

## YYYY-MM-DD — Project initialized

- **Prompt:** Set up folder, prompt, and standing rules for a parameter-driven
  Power BI validation project.
- **Changed:** Created `CLAUDE.md`, `docs/source-notes.md`, `.gitignore`.
- **Why:** Establish rules before any query work, so parameterization is designed
  in rather than retrofitted.
- **Verified:** N/A — no model yet.
- **Open:** Source type, page count, and table/column details not yet supplied.
  See `docs/source-notes.md`.

## 2026-09-30 — Repo hygiene: doc path fixed, root duplicates removed

- **Prompt:** "create .gitignore" (with pasted content), then "read the imp_docs.
  don't start building", then "fix the docs path and delete the duplicate root
  files. keep imp_docs folder, claude.md in root. these first".
- **Changed:**
  - Created `.gitignore` from content supplied verbatim (Power BI local
    artifacts, credentials/env, OS files).
  - `CLAUDE.md` line 86: working-agreement reference `docs/source-notes.md` →
    `imp_docs/source-notes.md`. The `docs/` folder does not exist.
  - Deleted root-level `powerbi-validation-prompt.md` and `source-notes.md`.
    Both were byte-identical to the copies in `imp_docs/` (verified with
    `diff -q`, no output). `imp_docs/` is now the single home for both.
- **Why:** Two copies of the same spec drift apart silently and there is no way
  to tell which one a later reader trusted. A stale path in the standing rules
  sends the next session to a folder that is not there. Fixed before any query
  work so the build starts from one unambiguous source of truth.
  The `YYYY-MM-DD — Project initialized` entry above still says
  `docs/source-notes.md` and still claims it created `.gitignore`. Left as
  written — the log rule forbids editing earlier entries, and the discrepancy is
  itself a record. `.gitignore` was in fact created in this session, not that one.
- **Verified:** No model exists yet, so no refresh and no smoke test. Verified
  only that the duplicates were identical before deletion and that `imp_docs/`
  retains both files.
- **Open:** All of `imp_docs/source-notes.md` is still blank. Blocking for any
  build: source type, page count, storage mode confirmation (Import assumed),
  and per page the server, database, schema, current table, second table in the
  same family for the smoke test, auth method, and column list with types.

## 2026-09-30 — Standing rules added: build gate, commit gate, log hygiene

- **Prompt:** Add to CLAUDE.md — do not build and do not push or commit unless
  told to; do not add filler prompts to the change log; profanity will appear in
  prompts and must not be carried into the log.
- **Changed:** `CLAUDE.md` only.
  - Working agreement: two rules added — no building without an explicit
    instruction, and no `git commit` / `git push` without one.
  - Change log protocol: two rules added — skip noise entries, never transcribe
    profanity. Existing rule "Commit the log update in the same commit" reworded
    to "When I do ask for a commit, ..." so it no longer contradicts the commit
    gate.
- **Why:** Reading a spec was being treated as licence to start generating, and
  the log was filling with entries nobody will need. Both rules stated at the
  point of use rather than in a preamble, so a session that only reads the
  Working agreement still sees them. Profanity rule exists because the log is a
  distributed deliverable read by people outside this room.
- **Verified:** N/A — docs only. No model, no refresh, no smoke test.
- **Open:** Unchanged. `imp_docs/source-notes.md` still blank; source type, page
  count, storage mode, and per-page table and column details still needed before
  any build. Repo still has zero commits and all work is uncommitted.

## 2026-10-01 — imp_docs now auto-loaded via CLAUDE.md imports

- **Prompt:** New sessions are not reading `imp_docs/`. What should be done
  about it.
- **Changed:** `CLAUDE.md` only. Added a "Required reading" section directly
  under the header, containing two Claude Code import directives:
  `@imp_docs/powerbi-validation-prompt.md` and `@imp_docs/source-notes.md`,
  plus a fallback instruction to read both manually if the import did not fire.
- **Why:** Only `CLAUDE.md` is loaded automatically at session start. Everything
  in `imp_docs/` was invisible unless someone asked for it by name, so each new
  session started without the build spec or the source answers and had to be
  told again. The import directive pulls both files in at session start, which
  fixes it without copying their contents into `CLAUDE.md` — copying would
  recreate exactly the duplication removed on 2026-09-30. Imports must sit on
  their own line and outside code fences or they are ignored, hence the layout.
  A `SessionStart` hook in `settings.json` would also work and was rejected as
  heavier and invisible to anyone reading the repo.
- **Verified:** Not verified. Whether the import fires can only be confirmed by
  opening a fresh session and checking it already knows the source-notes
  contents. The fallback paragraph exists because this is unverified.
- **Open:** Confirm on next fresh session. `imp_docs/source-notes.md` is still
  blank, so the import currently delivers an empty form — it stops the "which
  files do I read" loss, not the missing answers. Still needed before any build:
  source type, page count, storage mode, and per page the server, database,
  schema, current table, second table in the same family, auth method, columns
  with types, approx row count. Nothing committed; repo still has zero commits.

## 2026-10-22 — Git tracking removed; repo is now plain folder

- **Prompt:** Asked which repo this was linked to, then "delete everything git".
  Clarified as two readings — remove git tracking only, or delete the folder
  contents as well — and chose the first.
- **Changed:** Deleted `.git/` (61 KB). No working files touched. `.gitignore`,
  `CLAUDE.md` and `imp_docs/` remain on disk exactly as they were.
- **Why:** The repo had no remote, no commits, no stash, no reflog and no
  dangling objects — `git init` had run and nothing else. Nothing was lost
  because nothing had ever been saved. The three files were untracked, so they
  live on disk independent of git. Confirmed the reading before deleting rather
  than assuming, because the second reading would have destroyed the standing
  rules and the build spec with no way to recover them.
- **Verified:** `ls -a` shows the three working items present and `.git` gone;
  `git status` returns `fatal: not a git repository`. No model exists, so no
  refresh and no smoke test.
- **Open:** The project now has no version control at all. Rule 7 (`.pbip` as
  text so it can be diffed and reviewed) and the change-log protocol both assume
  a repo; both are weaker without one. Decide whether to re-init later and
  whether a remote is wanted. The commit gate in the Working agreement stands
  regardless. `imp_docs/source-notes.md` is still blank — source type, page
  count, storage mode, and per-page table and column details still block any
  build.
