# Task: Build a parameter-driven Power BI validation project

Build a Power BI Project (`.pbip`) that serves as a **data validation tool**. The
model and queries must be authored as text (TMDL + M) so they live in Git and can
be reviewed, not as a binary `.pbix`.

You cannot run Power BI Desktop. Produce the project files, the M and TMDL source,
and the operating docs. I will open the result in Desktop and verify.

---

## Context

- The file is used for **validating data**, not for presentation or dashboarding.
- It is distributed as **one local copy per person**. One canonical copy stays in
  the repo, unedited.
- Every user already has their own credentials to every data source. They refresh
  locally under their own identity.
- **No co-authoring, no merging.** Copies are never merged back. Nobody edits the
  canonical copy except the owner.
- Sources change often. The report must absorb those changes **without anyone
  opening Power Query or editing the file**.

## Structure

- Each **report page maps to exactly one data source**. Page 1 → Source 1,
  Page 2 → Source 2, and so on.
- **No overlap between pages.** A source belongs to one page only. One page
  breaking must never affect another.
- Within a single page's source family, **table and column naming conventions are
  stable**. Different tables in that family share the same column names and types.
- Table names differ between sources and **must be swappable from settings**.

---

## Requirements

### 1. Parameterize everything that changes

For each page, expose these as Power Query parameters (`Manage Parameters`), so
they are editable via **Transform data → Edit Parameters** without touching the
query editor:

- `P<N>_Server`
- `P<N>_Database`
- `P<N>_Table`

Queries must reference the table dynamically:

```m
Source{[Name = P1_Table]}[Data]
```

Do **not** hardcode server, database, or table names in any step.

### 2. Make queries resilient to column drift

Column names get baked into Power Query steps by default. Avoid that:

- No click-to-rename steps.
- Never use `Remove Other Columns` with a static list.
- Build the type-transform list programmatically rather than as a literal, e.g.
  derive it from `Table.ColumnNames(Source)` rather than hardcoding pairs.
- Schema mismatch should **fail loudly on refresh**, not silently drop columns or
  return wrong numbers. Do not wrap steps in `try ... otherwise null` in a way
  that hides a broken source.

### 3. Validation output per page

For each page, produce the standard validation surface:

- Row count
- Null count and null % per column
- Distinct count per column
- Min / max / blank checks for numeric and date columns

Prefer a generic implementation using `Table.UnpivotOtherColumns` into
`SourceTable | RowID | ColumnName | Value` where it reduces per-page bespoke work.
Note in the docs that this casts values to text, so numeric and date checks need
explicit conversion, and that it is slow on large tables.

### 4. Visible state on every page

Each page must show, as card or text visuals:

- The live parameter values driving that page (server, database, table)
- Last refresh timestamp
- A version number pulled from the repo

Rationale: the expected failure mode is someone validating against a stale local
copy or the wrong table and reporting a discrepancy that does not exist. The page
must make its own provenance obvious.

### 5. Relationships

Do not force relationships between the page models. Keys will not match across
sources and auto-detect will guess wrong. Leave the tables unrelated unless a page
genuinely needs a join.

### 6. Storage mode

Default to Import unless I say otherwise. Flag clearly in the docs that Import
caches a full data snapshot inside every distributed local copy, so those files
carry the data's sensitivity regardless of database permissions.

---

## Deliverables

1. A `.pbip` project folder: `.Report/` and `.SemanticModel/`, TMDL for the model,
   report definition as JSON.
2. All M queries as readable text, one per page, following the parameter pattern.
3. `.gitignore` and `.gitattributes` appropriate for a `.pbip` repo (exclude
   caches and local settings).
4. `README.md` covering:
   - How a user changes a table or database without opening Power Query
   - How to add a new page for a new source
   - What breaks if column names change, and how it will surface
   - The stale-copy risk and how the version card mitigates it
5. A **smoke test procedure**: point a page's `P<N>_Table` parameter at a second
   table in the same family and confirm refresh completes with no query errors
   and no broken visuals. This is the critical assumption in the whole design —
   call it out explicitly.

## Constraints

- Ask before assuming the source type (SQL Server, Fabric warehouse, Excel,
  folder, API). The M differs substantially and I have not specified it.
- Ask how many pages/sources to scaffold before generating them.
- Do not add row-level security. It provides no protection here — anyone with the
  file can open the model and remove it.
- Do not build a star schema or a semantic layer. This is a validation harness.
