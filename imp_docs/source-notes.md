# Source Notes

Everything Claude Code needs to build the queries. Fill this in before the first
session — unanswered items will stall the work.

**No passwords, keys, tokens, or connection strings containing credentials.**
Server and database *names* are fine. Anything secret goes in Power BI Desktop's
credential store, never here.

---

## Global

| Item | Value |
|---|---|
| Number of pages/sources to scaffold now | |
| Storage mode (Import / DirectQuery) | |
| Power BI Desktop version | |
| Gateway needed for scheduled refresh? | |
| Who owns the canonical copy | |

**Why Import vs DirectQuery matters:** Import caches a full snapshot of the data
inside every distributed local copy. Those files then carry the data's
sensitivity regardless of database permissions. DirectQuery caches nothing but
breaks immediately without source access.

---

## Page 1

| Item | Value |
|---|---|
| Source type (SQL Server / Fabric / Synapse / Excel / SharePoint / folder / API) | |
| Server | |
| Database | |
| Schema | |
| Current table name | |
| **Second table in same family (for smoke test)** | |
| Authentication method (Windows / Entra ID / SQL / OAuth) | |
| Approx row count | |
| How often the table is swapped | |

### Columns

Applies to every table in this family — same names, same types.

| Column | Type | Notes |
|---|---|---|
| | | |
| | | |
| | | |

### Validation checks wanted on this page

- [ ] Row count
- [ ] Null count and null % per column
- [ ] Distinct count per column
- [ ] Min / max on numeric and date columns
- [ ] Blank / whitespace check on text columns
- [ ] Other:

### Known quirks

Anything that will trip up a refresh — mixed types in a column, dates stored as
text, trailing spaces, nulls that arrive as the string "NULL", regional decimal
separators, columns that only appear in some tables.

---

## Page 2

| Item | Value |
|---|---|
| Source type | |
| Server | |
| Database | |
| Schema | |
| Current table name | |
| **Second table in same family (for smoke test)** | |
| Authentication method | |
| Approx row count | |
| How often the table is swapped | |

### Columns

| Column | Type | Notes |
|---|---|---|
| | | |
| | | |
| | | |

### Validation checks wanted on this page

- [ ] Row count
- [ ] Null count and null % per column
- [ ] Distinct count per column
- [ ] Min / max on numeric and date columns
- [ ] Blank / whitespace check on text columns
- [ ] Other:

### Known quirks

---

<!-- Copy a page block for each additional source. Keep pages independent. -->

---

## Reference M

If an existing .pbix already connects to any of these sources, paste the M from
Power Query → Advanced Editor here. Real connection syntax beats a guess.

```m

```

---

## Open questions

Things not yet decided. Claude Code should ask rather than assume.

-
-
