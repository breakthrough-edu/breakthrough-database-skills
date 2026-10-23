# Derivation rules

These convert an interview answer into a design decision. Apply them out loud so the user learns the reasoning.

- **R1 Table or field.** If a thing can happen more than once per spine row, or carries its own dates, amounts, or attachments, it is a table. If it is one value per row with nothing hanging off it, it is a field.
- **R2 Select or link table.** Make it a link table when you want to see it as a row in a report, count by it, or attach anything to it. Otherwise a select. In the reference architecture, Lane became a table only because they wanted live project count per lane.
- **R3 Money rule.** Every amount lives on a transaction row that also has a date, a counterparty, and a document. The spine gets a `.SUM()`. When those rows are copies from another system, the spine's total goes through the match key instead (R18).
- **R4 Grouping rule.** Any date you will group a report by needs a `TEXT([Date],"YYYY-MM")` sibling.
- **R5 Status rule.** Derivable from evidence means formula. Requires human judgment means select.
- **R6 People rule.** Link to a people table when you need workload or capacity numbers. Plain user field when you only need to record or notify.
- **R7 Proof rule.** Every point where the business would have to prove something to a client, a supplier, an auditor, or a court gets an attachment field on the row where that thing happens, when that row is a Source row. A mirrored document's proof lives in its home and the Base carries its reference number; if the owner wants the file in the Base too, it goes on a Source table keyed to the Mirror row.
- **R8 Code rule.** Anything humans say out loud to each other ("job 214", "invoice 88") is an auto number field, not something typed, but only on a Source table. A copy keeps the number its home issued, as text. An Intake row gets its own internal reference, and the official number comes back from the home.
- **R9 Pull-through rule.** If a child row will ever be read on its own (in a view, a form, an automation, a PDF), pull the parent's identity down with `[Parent].[Field]`. The link alone does not display anything useful outside the table.
- **R10 Period rule.** Build a period table when they want month over month, or budget against actual, or a stable report shape.
- **R11 One number one home.** If two fields could ever disagree, one of them must be a formula. Across systems the same holds: if the Base and another system could ever disagree about a fact, one of them is the home and the other is a copy nobody edits.
- **R12 Naming rule.** Give every table a suffix (` Table`, ` DB`, whatever) so no table name is ever identical to a link field name. See the naming trap in [lark-mechanics.md](lark-mechanics.md); this rule exists to avoid a silent formula bug.
- **R13 Reader rule.** Every field must have an answer to "who reads this and when." A field whose only answer is "might be useful someday" does not get built.

The rules below decide where each kind of record lives. The roles and states they use are defined in [data-hub-roles.md](data-hub-roles.md).

- **R14 Home rule.** Every kind of record has one home: where its number is issued and where it is changed. The home wins every disagreement.
- **R15 Role rule.** Every table is exactly one of Source, Mirror, Intake or Snapshot, and its header in the blueprint says which.
- **R16 Mirror rule.** A Mirror table has no field a person types into. It carries `Source Ref` (the home's own number, text), `Sync Status`, `Last Synced` and `Sync Note`, and every field's "who writes" is the route. Anything the team wants to add about a mirrored record (a note, an owner, a follow up date) goes on a Source table keyed to it, never as an editable column on the Mirror. A cancellation arrives as a status from the home; rows are never deleted by hand.
- **R17 Intake rule.** An Intake row is a request: drafted in the Base, handed to the home, given its official number there. That number comes back and is stored on the Intake row as text; from then on the row is closed and nobody edits it (a convention the team keeps, helped by a view that hides closed rows, since a field cannot lock itself per row). The official record arrives as a row in a Mirror. ⛔ Money totals never count Intake rows, only the Mirror, or every sale is counted twice.
- **R18 Key rule.** A Base row and a mirrored row are matched by a text key both sides store (the match key: the Base's own code, carried by the home record), not by a link field alone: rows written into the Base by a sync do not get links attached unless the route is built to set them, and a design never assumes it is. Totals over Mirror rows filter on the key and add up stored amounts, never a Mirror's formula field. If the home cannot store the Base row's code anywhere on the record, matching becomes a person's job in a small Source table, or the question cannot be answered per row; say which. When one home record covers many Base rows (one weekly transfer paying a team for twenty jobs), match at the level the home record exists, and compare: the Base works out what should be paid per job as Source, the Mirror shows what was paid per transfer. Formula patterns in [lark-mechanics.md](lark-mechanics.md).
- **R19 Snapshot rule.** A figure that must stop moving (a closed month, a paid commission) is written once as a dated row and never recalculated, because the home may still correct history after it was reported.
- **R20 Freshness rule.** A metric is only as fresh as the slowest copy it reads. The traceability table shows that age next to each metric, so nobody reads last week's import as today.
- **R21 Honest state rule.** Every line between the Base and another system is reported at the state it has proven: Route mapped, Base structure ready, Mirror connected, Sync verified. Sync verified needs one real change made in the home and seen arriving in the Base, with no duplicate row and nothing written back.

# Anti-patterns, refuse these

- **One giant table.** Sixty columns where six tables belong. Symptom: columns that are blank for most rows.
- **Typed totals.** Any sum a human keys in is wrong within a month.
- **Fields for reports nobody reads.** If it does not trace to Phase 6 or Phase 7, do not build it.
- **Copying the reference architecture.** Eleven tables on day one guarantees abandonment.
- **Modelling the business they wish they had.** Build for how work actually flows today, then let the base pull them forward.
- **Multi select for something they will count by.** If they will ever ask "how many by X", X is a link table.
- **Two places to record the same fact.** Pick one and derive the other.
- **Editing a copy "just this once".** The next sync overwrites it, or worse, does not, and the two now disagree for good.
- **A two way sync.** Each kind of record flows one way. Writing a correction into a copy and pushing it home is the drift this design exists to prevent.
- **A typed invoice table beside an accounting system that issues the numbers.** If the accounting software issues invoice numbers, the Base's invoice table is a Mirror.
- **Promising a connection nobody has checked.** A route stays at Route mapped until it has been seen working.
- **Forcing a Mirror onto a business with only spreadsheets.** Everything is Source there, and that is fine.
