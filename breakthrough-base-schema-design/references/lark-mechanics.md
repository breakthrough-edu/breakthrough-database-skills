# Lark mechanics

## Field types

text, number, currency, select, multi select, datetime, checkbox, user, phone, email, url, attachment, link, lookup, formula, auto number, barcode, rating, progress, location, created time, created by, last modified time, last modified by.

## Formula patterns

```
[Link].[Field].SUM()                              rollup a number across linked rows
[Link].COUNTIF(CurrentValue.[Field]="Value")      count linked rows matching a condition
[Link].[Field]                                    pull one value down from the parent
[LinkA].[LinkB].[Field]                           two hop pull-through
SUM([Link].[FormulaField])                        roll up a formula from linked rows
TEXT([Date],"YYYY-MM")                            month bucket for grouping
IFERROR([A]/[B],)                                 safe divide, blank instead of error
IFBLANK([Field],0)                                treat empty as zero before arithmetic
IF([Date]="","Pending","Done")                    derive status from evidence
CONCATENATE([A],"-",[B],[C])                      synthetic row name
RECORD_ID()                                       stable id for automations and webhooks
[Table].FILTER(CurrentValue.[Key]=[Key]).[Field].LISTCOMBINE().SUM()
                                                  total over rows matched by a text key, not a link
```

## Traps, all of which fail silently

- **A bare `[Name]` resolves to a table before it resolves to a field.** If a link field is called `Client` and a table is also called `Client`, then `[Client].[Something]` may aggregate the entire table instead of the linked rows. Symptom: every row shows the same value, and it looks like plausible data. This is why derivation rule R12 exists: suffix your table names. To test, find a state where the linked scope and the whole table would give different answers, and check that the formula reports the linked one. If both scopes happen to give the same number, the test proves nothing.
- **Two-way links auto-create a reverse field on the target table, named after the source table.** That is exactly the collision above. Rename it the moment it appears.
- **Changing a number field to a date reinterprets existing values as epoch milliseconds.** A `14` becomes 1 January 1970. No error is raised. Export the values before changing any field type, and re-read a few records after.
- **Filtered lookups compare values, not types.** The period-table dashboard pattern works because both sides are text in the form "2026-01". Make sure the period table's key and the month bucket formula produce identical strings.
- **Formulas that reference deleted or renamed fields go quiet rather than loud.** After any rename, re-read one record from each affected table.

## Copies from other systems (Mirror and Intake)

Known behaviour, from testing and from Lark's own documentation:

- **Rows written by a sync arrive without links.** A sync writes text, numbers and dates; it does not attach a link to another table unless the route is built to look the parent row up and set it. A total written as `[Link].[Field].SUM()` then quietly leaves new rows out. Match on a text match key with the `FILTER` pattern above, which works for rows written by any route, including a hand import.
- **A total across tables is safest over a stored value at the far end.** A `FILTER` total pointed at a formula or lookup field has come back as zero or blank without an error in some cases and worked in others. Point it at a stored number on the Mirror, and test it on real rows either way.
- **Lark's own synced tables are locked by Lark.** A table Lark itself keeps in sync from another source cannot have rows added or deleted by hand, its fields cannot be given edit rights in a role, and some view settings (which fields are visible) refuse changes through the CLI while filters and grouping still work. That lock is the behaviour you want for a Mirror.
- **A Mirror filled by a program or a hand import is an ordinary table.** Nothing stops a person typing into it except the team's discipline and, where the Lark plan includes advanced permissions, a role that makes the table read only. Whether the owner's plan includes advanced permissions is a thing to check, not to assume.
- **Lark automations cannot delete rows, and a plain "add row" step adds a duplicate every time it runs.** A route that re-pulls the same data without checking a key multiplies every total silently. This is the route builder's problem, not the design's, but it is why every Mirror carries `Source Ref` and why cancellations must arrive as a status.
- **A Mirror built before its route exists may need reshaping when the route is built.** Some routes cannot write certain field types directly (dates are a known case for Lark automations). Treat the Mirror's field types as provisional until the route reaches Mirror connected.

**When something looks empty or wrong,** read the raw response before concluding the data is missing. Most "the base is empty" moments are a wrong field name or a wrong assumption about the response shape.

## Build order

Order matters in Lark. A link field cannot be created before its target table exists, and a formula cannot be created before the fields it references. Always hand over this sequence:

1. Tables with no outgoing links first: people, counterparties, classification.
2. The spine table with only its own plain fields (title, dates, stage, status, code).
3. Transaction and execution tables with only their own plain fields. Mirror tables get their four receipt fields here (`Source Ref`, `Sync Status`, `Last Synced`, `Sync Note`), and their key field.
4. Link fields, created from the child pointing at the parent. The reverse field appears automatically on the parent. Rename it immediately, before anything references it.
5. Pull-through formulas on children (`[Parent].[Field]`).
6. Rollup formulas on the spine (`.SUM()`, `.COUNTIF()` over links to Source tables; match key `FILTER` totals over Mirrors).
7. Second-order rollups on counterparty and people tables (lifetime value, active load).
8. The period table and its filtered lookups.
9. Views, then forms for anything a counterparty fills in.
10. If any Mirror is in Phase 1: import one export from its home by hand, label every row `Imported by hand` with the export's date, and count rows against the file.
11. Enter two real past records end to end and check every metric on the traceability table produces the right number by hand.

⛔ Step 11 is not optional. A base that has never had a real record through it is a drawing, not a system.

Routes are never a build step. Each one is its own job afterwards, finished only when one real change made at the home is seen arriving in the Base.
