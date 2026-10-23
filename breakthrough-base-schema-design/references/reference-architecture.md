# Reference architecture

This is a real production base for a creative production company (11 tables: Lane, Project, Task, Project Output, Quotation, Sales Invoice, AP Invoice, Client, Vendor, Employee, Dashboard Monthly View). Use it as a **pattern library**, never as a template. Each layer is listed with the reason it exists, because the reason is the transferable part. The patterns are extracted; the specific fields are not, on purpose.

| Layer | Reference tables | Why the layer exists | Usual role ([data-hub-roles.md](data-hub-roles.md)) | Skip it when |
|-|-|-|-|-|
| Spine | Project | One row per unit of work. Every other table points here. | Source. Mirror when the unit is born in another system (online shop orders, a booking system). | Never skip. If there is no spine, you have not finished the interview. |
| Classification | Lane | A category that earns its own table because you want to count and compare by it. | Source. | The category is a closed short list with no attributes and no rollups wanted. Use a select. |
| Counterparties | Client, Vendor | The outside world. One row reused across many spine rows. Carries compliance and banking payload. | Source, or Mirror when a CRM or the accounting software's customer list is the master. | The business sells to walk-in strangers it never bills again. |
| People | Employee | The inside world. Carries HR payload plus workload rollups. | Source, or Mirror when HR or payroll software holds the staff list. | Solo operator with no delegation. |
| Money in | Quotation, Sales Invoice | One row per revenue document, with its own dates, amounts, and proof. | Source when the Base issues the number. Mirror when accounting software issues it. A quotation drafted here and issued there is Intake. | Never skip. Even a cash business has a revenue row. |
| Money out | AP Invoice | One row per cost that can be attributed to a spine row. | Source, or Mirror when supplier bills are keyed into accounting software. | The business has no per-job variable cost. Say so explicitly and move on. |
| Execution | Task, Project Output | The doing, and the artifact that gets delivered. | Source, always. | Work is one step by one person. |
| Period | Dashboard Monthly View | A physical row per month so reports have stable rows even in months where nothing happened. | Source for the live monthly view; Snapshot rows for any month that has to stay closed. | You only ever look at totals, never trends. |

## Eight design decisions worth stealing from the reference

**1. Money never sits on the spine.** Amounts live on transaction rows. The spine's totals are formulas:

```
Project Total In  = [Sales Invoice].[Inv Amount].SUM()
Project Total Out = [AP Invoice].[Final Payout (MYR)].SUM()
Project GP (RM)   = [Project Total In]-[Project Total Out]
Project GPM       = IFERROR(([Project Total In]-[Project Total Out])/[Project Total In],)
```

If a human can type a total, two numbers will eventually disagree. Make one of them a formula.

When the invoice tables are Mirrors, the spine total does not ride on the link, because rows a sync writes into the Base arrive without links unless the route is built to set them, and a design never assumes that. It filters on the match key instead, which is the project code the team typed into the invoice's reference field in the accounting software, copied in as `Project Code`:

```
Project Total In  = [Sales Invoice Table].FILTER(CurrentValue.[Project Code]=[Project Code]).[Inv Amount].LISTCOMBINE().SUM()
```

`Inv Amount` must be a stored number on the Mirror, not a formula there. Exclude cancelled rows in the same filter once the home sends a cancelled status.

**2. Status gets derived from evidence where possible.**

```
Payment Received Updates = IF([Payment Received Date]="","Pending","Done")
```

Nobody has to remember to flip a switch. The date being filled in *is* the switch. Only make a status a human-editable select when a human genuinely makes a judgment call.

**3. Accrual and cash are two different questions, and the reference answers both deliberately.** Project profit sums every invoice raised, whether or not the money arrived (accrual, "did this job make money"). The monthly dashboard sums by `Payment Received Month` and `ACTUAL PAYMENT MONTH` (cash, "what actually moved this month"). Ask the user which one they manage on, then build both buckets and label them so the two numbers never get confused for each other.

**4. Every date you want to group by gets a text sibling.**

```
Month Created         = TEXT([Date Created],"YYYY-MM")
Payment Received Month= TEXT([Payment Received Date],"YYYY-MM")
Inv Due Month         = TEXT([Inv Due],"YYYY-MM")
```

**5. The period table.** A plain table whose only real content is a text column of month strings ("2026-01", "2026-02", ...). Each row then pulls a filtered sum from the money tables:

```
In  = lookup from Sales Invoice Table, sum of [Inv Amount],  where [Payment Received Month] = this row's [Text]
Out = lookup from AP Invoice Table,   sum of [Final Payout], where [ACTUAL PAYMENT MONTH]   = this row's [Text]
```

This gives you a monthly P&L with a row for every month whether or not anything happened, and it gives you somewhere to put a budget column next to the actual.

**6. Link when you need to count, plain user field when you only need to notify.** The Task table links Responsible and Accountable to the Employee table, because it rolls workload up:

```
Current Active Responsible Tasks = [Responsible].COUNTIF(CurrentValue.[Task Status]="In Progress")
```

Consult and Inform are ordinary user fields. Nobody needs a Consult workload report. The asymmetry is the point: a link is expensive to maintain, so only pay for it where a rollup earns it.

**7. Counterparty tables are the back end of a form.** The reference Client table has 33 fields and the Vendor table has 40, almost all compliance payload (registration number, tax number, tax classification, banking, billing contacts, respondent name and designation, consent). They look bloated until you realise they are filled once, by the counterparty, through a Lark form, at onboarding. Design them that way or they will never get filled.

**8. A Mirror carries its own receipt.** Every copied table gets the same four fields, so anyone can see where a row came from and how old it is:

| Field | Type | Who writes |
|---|---|---|
| Source Ref | text | the route: the home's own number for this row |
| Sync Status | select: Imported by hand, Synced, Error | the route |
| Last Synced | datetime (provisional: some routes cannot write dates, so the route builder may change it to text read by a formula) | the route |
| Sync Note | text | the route, only when something went wrong |

Nobody types into a Mirror, so the "who writes" column for every field on it says the route. A team note about a mirrored invoice lives in a Source table keyed by `Source Ref`.

## Three things in the reference to improve rather than copy

- Tax is hardcoded into formulas (`[Inv Amount]*0.08`, `[Inv Amount]*1.08`). When the rate changes, someone has to find and edit every formula. Put the rate in a field or a settings row and reference it. Never assume a tax rate; ask.
- In the reference, invoice rows are typed into the Base. Under the home rule that is right only if the Base is where the invoice number is issued. When accounting software issues it, those tables become Mirrors (pattern 8) and the spine totals switch to the match key (pattern 1).
- The primary name on cost rows is synthesised (`CONCATENATE([Project],"-",[Vendor],[INVOICE NO])`). That is a good idea on a Source table, and it depends on those links being filled first. Tell the user rows will look broken until they are. On a Mirror the primary field is simply the home's own number.
