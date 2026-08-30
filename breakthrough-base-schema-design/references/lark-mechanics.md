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
```

## Traps, all of which fail silently

- **A bare `[Name]` resolves to a table before it resolves to a field.** If a link field is called `Client` and a table is also called `Client`, then `[Client].[Something]` may aggregate the entire table instead of the linked rows. Symptom: every row shows the same value, and it looks like plausible data. This is why derivation rule R12 exists: suffix your table names. To test, find a state where the linked scope and the whole table would give different answers, and check that the formula reports the linked one. If both scopes happen to give the same number, the test proves nothing.
- **Two-way links auto-create a reverse field on the target table, named after the source table.** That is exactly the collision above. Rename it the moment it appears.
- **Changing a number field to a date reinterprets existing values as epoch milliseconds.** A `14` becomes 1 January 1970. No error is raised. Export the values before changing any field type, and re-read a few records after.
- **Filtered lookups compare values, not types.** The period-table dashboard pattern works because both sides are text in the form "2026-01". Make sure the period table's key and the month bucket formula produce identical strings.
- **Formulas that reference deleted or renamed fields go quiet rather than loud.** After any rename, re-read one record from each affected table.

**When something looks empty or wrong,** read the raw response before concluding the data is missing. Most "the base is empty" moments are a wrong field name or a wrong assumption about the response shape.

## Build order

Order matters in Lark. A link field cannot be created before its target table exists, and a formula cannot be created before the fields it references. Always hand over this sequence:

1. Tables with no outgoing links first: people, counterparties, classification.
2. The spine table with only its own plain fields (title, dates, stage, status, code).
3. Transaction and execution tables with only their own plain fields.
4. Link fields, created from the child pointing at the parent. The reverse field appears automatically on the parent. Rename it immediately, before anything references it.
5. Pull-through formulas on children (`[Parent].[Field]`).
6. Rollup formulas on the spine (`.SUM()`, `.COUNTIF()`).
7. Second-order rollups on counterparty and people tables (lifetime value, active load).
8. The period table and its filtered lookups.
9. Views, then forms for anything a counterparty fills in.
10. Enter two real past records end to end and check every metric on the traceability table produces the right number by hand.

⛔ Step 10 is not optional. A base that has never had a real record through it is a drawing, not a system.
