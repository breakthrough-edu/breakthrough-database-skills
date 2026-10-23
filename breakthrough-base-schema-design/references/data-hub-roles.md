# Data hub roles

A Base that shows the whole company in one place is a data hub. Some of what it shows is born in the Base. Much of it is born somewhere else: invoices in the accounting software, customers in a CRM, staff in an HR system, orders in an online shop, spend in the ad platform. This reference is how to decide, for every kind of record, where it is born and which way it flows, so the same fact never ends up kept in two places that drift apart.

## The rule

**Each kind of record has one home and one direction.** The home is the one place where that kind of record is officially created and changed. Everywhere else holds a copy that follows the home, and nobody edits the copy.

The rule is per kind of record, not per Base. One Base can hold tables copied in from the accounting software and tables whose rows are pushed out to it, side by side. What it can never hold is one kind of record flowing both ways.

## How to find the home

Ask where the record's **number** is issued. Where the invoice number, the order number, the employee ID or the customer code comes from is where the record is born, so that is its home. Two follow ups settle the rest: when this record has to change, where does the change get made, and who makes it?

- The number comes from the accounting software: the accounting software is home for that kind.
- The number is typed into a Word or Excel template and the accountant keys it into the accounting software later: the template is where it is born, so the business issues it, not the accounting software. A template is not a system, so recommend moving the issuing into the Base (a Source table with an auto number) and treating the accounting software as downstream of it. Say this out loud as a recommendation and let the owner decide.
- Nobody issues a number (a walk in sale, a WhatsApp booking): the Base becomes the home the moment the first row is typed in it.
- Two systems both issue numbers for the same kind of record: this is the drift the rule exists to stop. Name it, and ask the owner to pick one home. Do not design around it.
- A home covers only part of a kind (the accounting software gives codes to company clients, but households who book on WhatsApp never get one): either the two parts are really different kinds and each gets its own home, or one home is picked for all of them and the other list becomes downstream of it. Ask which. Whichever list loses keeps existing in the other system for its own purposes; write it under "Deliberately not built" so nobody mistakes it for a second home.

## The four roles

Every table in the blueprint gets exactly one role.

| Role | Plain words for the owner | How to tell |
|-|-|-|
| **Source** | "Born here. This Base is the original." | The Base issues its number, or it has no number and is only ever typed here. Projects, tasks, deliverables and internal requests are almost always Source. |
| **Mirror** | "A copy of another system. Look, do not touch." | Its home is another system. Rows arrive from there; changes are made there and arrive again. |
| **Intake** | "A request form. Filled in here, sent to its real home." | The team drafts it in the Base, it is handed to another system, and that system issues the official number. The Intake row is a request. The official record is a different kind of record, with its own home, and it appears in the Base as a Mirror. When the request is just one step of a Source row (a job marked "ready to invoice"), it needs no table of its own: the Source row carries the returned number as text. |
| **Snapshot** | "A number frozen on a date." | A month end figure that must stop moving: closed month revenue, a commission that has been paid. Written once, dated, never recalculated. |

⛔ **An Intake row is never counted as money.** Money totals count the official record (the Mirror row), never the request. If both were counted, every sale would appear twice.

**Write-back means editing a copy and pushing it home. It is never allowed by default.** Intake is not write-back: an Intake row is a new request sent to the home, and the home's answer (the number) comes back. Correcting an invoice inside the Base and pushing the correction to the accounting software is write-back, and is refused.

## What every line into or out of the Base carries

For every kind of record whose home is not the Base, record:

- **Direction**: into the Base (Mirror) or out of it (Intake).
- **Source Ref**: the home's own number for the record (invoice number, debtor code), stored on the Mirror as text. It is how a re-import recognises a row it has already seen.
- **Match key**: separate from Source Ref, only when a Mirror row has to be tied to a Base row. ⛔ If a Mirror row has to be tied to a Base row (an invoice to the project it belongs to), the home system must carry the Base row's code somewhere on the record, such as a reference or description field the team types the job number into when raising the invoice. Ask whether that field exists. If the home has nowhere to carry it, the matching becomes a person's job in a small Source table, or the per project total is not a question this Base can answer. Say which.
- **Freshness**: how old the copy may be when someone reads it. Set in Phase 6 from the metrics that read it.
- **Data Owner**: the person responsible for the record being right in its home.
- **Sync Owner**: the person who notices when copies stop arriving and gets it fixed. Often the same person in a small business. Must be a named person, never "the system".
- **Who may edit in the Base**: for a Mirror, nobody. For an Intake row, the team until it is handed over, then nobody.
- **Table or column**: if the home keeps a thing as its own records with their own dates and amounts (each payment received), it is its own Mirror table. If the home only keeps a figure on another record (an outstanding balance on the invoice), it is a column on that Mirror. Look at what the home's export actually contains before deciding.
- **Request route**: when the team needs a new record that lives elsewhere (a new customer, a new invoice), where they ask for it. Usually an Intake table or form, or simply "ask the accountant to raise it in the accounting software".
- **Route**: how the copy will travel, if anyone knows: the software's own connector, a Lark automation calling the software, a third party connector, a scheduled script, or an export from the software imported by hand. ⛔ Never pick one on the owner's behalf and never promise that a given software can be connected. Write what the owner or their vendor says, or write "not known yet, ask the vendor".
- **State**, one of four, never claimed higher than proven:
  1. **Route mapped**: the home, direction, Source Ref, match key and owners are agreed on paper.
  2. **Base structure ready**: the tables exist and can hold the copy.
  3. **Mirror connected**: copies arrive by the route, not by hand.
  4. **Sync verified**: someone changed one real record in the home and saw that change arrive in the Base, with no duplicate row created and nothing written back.

Until a route reaches Mirror connected, the Mirror can be filled by an export imported by hand. Label it: `Sync Status = Imported by hand`, and `Last Synced` = the date of the export. A number built on it is as old as that date, and the blueprint says so.

## Two shapes to catch in the interview

- **The business only has spreadsheets.** Every table is Source. Do not invent a Mirror to make the design look like a hub. Say plainly that every record will be born in the Base, and move on.
- **The unit of work itself is born elsewhere.** An online shop whose orders are born in the shop platform, a clinic whose bookings are born in a booking system. The spine is then a Mirror. The Base adds what the other system does not hold (the work around each order, the follow up, the cost) as Source tables keyed to it. Say this when Phase 1 surfaces it, because it changes the shape of everything after.

## How to explain it to the owner

Use their words. The four role names (Source, Mirror, Intake, Snapshot) may appear as labels in the blueprint, always with their plain words beside them the first time. One version that works:

> 每一种资料只有一个家。发票在会计软件出生，就以会计软件为准；Base 里那份只是照过来的，看可以，改不行。要改，回会计软件改，改完它自己会再照过来。这样同一张发票永远只有一个版本，不会有一天两边对不上。

⛔ Never say system of record, source of truth or schema to them unless they used the words first. Say home, 正本, "where it is born", copy, table, row.
