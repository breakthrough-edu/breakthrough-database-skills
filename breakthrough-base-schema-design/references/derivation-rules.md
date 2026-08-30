# Derivation rules

These convert an interview answer into a design decision. Apply them out loud so the user learns the reasoning.

- **R1 Table or field.** If a thing can happen more than once per spine row, or carries its own dates, amounts, or attachments, it is a table. If it is one value per row with nothing hanging off it, it is a field.
- **R2 Select or link table.** Make it a link table when you want to see it as a row in a report, count by it, or attach anything to it. Otherwise a select. In the reference architecture, Lane became a table only because they wanted live project count per lane.
- **R3 Money rule.** Every amount lives on a transaction row that also has a date, a counterparty, and a document. The spine gets a `.SUM()`.
- **R4 Grouping rule.** Any date you will group a report by needs a `TEXT([Date],"YYYY-MM")` sibling.
- **R5 Status rule.** Derivable from evidence means formula. Requires human judgment means select.
- **R6 People rule.** Link to a people table when you need workload or capacity numbers. Plain user field when you only need to record or notify.
- **R7 Proof rule.** Every point where the business would have to prove something to a client, a supplier, an auditor, or a court gets an attachment field on the row where that thing happens.
- **R8 Code rule.** Anything humans say out loud to each other ("job 214", "invoice 88") is an auto number field, not something typed.
- **R9 Pull-through rule.** If a child row will ever be read on its own (in a view, a form, an automation, a PDF), pull the parent's identity down with `[Parent].[Field]`. The link alone does not display anything useful outside the table.
- **R10 Period rule.** Build a period table when they want month over month, or budget against actual, or a stable report shape.
- **R11 One number one home.** If two fields could ever disagree, one of them must be a formula.
- **R12 Naming rule.** Give every table a suffix (` Table`, ` DB`, whatever) so no table name is ever identical to a link field name. See the naming trap in [lark-mechanics.md](lark-mechanics.md); this rule exists to avoid a silent formula bug.
- **R13 Reader rule.** Every field must have an answer to "who reads this and when." A field whose only answer is "might be useful someday" does not get built.

# Anti-patterns, refuse these

- **One giant table.** Sixty columns where six tables belong. Symptom: columns that are blank for most rows.
- **Typed totals.** Any sum a human keys in is wrong within a month.
- **Fields for reports nobody reads.** If it does not trace to Phase 6 or Phase 7, do not build it.
- **Copying the reference architecture.** Eleven tables on day one guarantees abandonment.
- **Modelling the business they wish they had.** Build for how work actually flows today, then let the base pull them forward.
- **Multi select for something they will count by.** If they will ever ask "how many by X", X is a link table.
- **Two places to record the same fact.** Pick one and derive the other.
