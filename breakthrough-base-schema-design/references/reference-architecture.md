# Reference architecture

This is a real production base for a creative production company (11 tables: Lane, Project, Task, Project Output, Quotation, Sales Invoice, AP Invoice, Client, Vendor, Employee, Dashboard Monthly View). Use it as a **pattern library**, never as a template. Each layer is listed with the reason it exists, because the reason is the transferable part. The patterns are extracted; the specific fields are not, on purpose.

| Layer | Reference tables | Why the layer exists | Skip it when |
|-|-|-|-|
| Spine | Project | One row per unit of work. Every other table points here. | Never skip. If there is no spine, you have not finished the interview. |
| Classification | Lane | A category that earns its own table because you want to count and compare by it. | The category is a closed short list with no attributes and no rollups wanted. Use a select. |
| Counterparties | Client, Vendor | The outside world. One row reused across many spine rows. Carries compliance and banking payload. | The business sells to walk-in strangers it never bills again. |
| People | Employee | The inside world. Carries HR payload plus workload rollups. | Solo operator with no delegation. |
| Money in | Quotation, Sales Invoice | One row per revenue document, with its own dates, amounts, and proof. | Never skip. Even a cash business has a revenue row. |
| Money out | AP Invoice | One row per cost that can be attributed to a spine row. | The business has no per-job variable cost. Say so explicitly and move on. |
| Execution | Task, Project Output | The doing, and the artifact that gets delivered. | Work is one step by one person. |
| Period | Dashboard Monthly View | A physical row per month so reports have stable rows even in months where nothing happened. | You only ever look at totals, never trends. |

## Seven design decisions worth stealing from the reference

**1. Money never sits on the spine.** Amounts live on transaction rows. The spine's totals are formulas:

```
Project Total In  = [Sales Invoice].[Inv Amount].SUM()
Project Total Out = [AP Invoice].[Final Payout (MYR)].SUM()
Project GP (RM)   = [Project Total In]-[Project Total Out]
Project GPM       = IFERROR(([Project Total In]-[Project Total Out])/[Project Total In],)
```

If a human can type a total, two numbers will eventually disagree. Make one of them a formula.

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

## Two things in the reference to improve rather than copy

- Tax is hardcoded into formulas (`[Inv Amount]*0.08`, `[Inv Amount]*1.08`). When the rate changes, someone has to find and edit every formula. Put the rate in a field or a settings row and reference it. Never assume a tax rate; ask.
- The primary name on cost rows is synthesised (`CONCATENATE([Project],"-",[Vendor],[INVOICE NO])`). That is a good idea, and it depends on those links being filled first. Tell the user rows will look broken until they are.
