# Phase 4: Building the dashboard

One self-contained page: an artifact if this environment renders artifacts, otherwise a single `.html` file the user can double-click. No external libraries either way.

## Embedding the data

Embed the data the page needs. If the source is large (over roughly 2,000 rows), precompute the aggregates and embed those plus only the row-level slices the page displays (for example: open invoices, not the full history). Never paste an entire large dataset into the page.

## The page is an argument, ordered by urgency, not a grid of charts

1. **The headline**: the chosen question and its actual number, computed (Iron rules R2 and R3).
2. **Alerts, ranked by money at risk.** Every alert names the specific record (invoice number, customer, vendor, task) and the action ("chase", "pay", "get it signed", "fix this row"). Check at least these classes:
   - Overdue as of the data date.
   - Due within 7 days.
   - Committed but not yet real (issued or promised, not yet paid or delivered).
   - Contradictions in the data (paid date without paid status, and the reverse).
   - Items stuck in a waiting or blocked state, and the money behind them.
   - Revenue concentration in one customer.
   - Anything under the owner's stated margin or threshold.
   - Orphan records that fall outside every total.
3. **Supporting views.** Every section is titled with the question it answers ("Who owes me, and for how long?"), never with a chart type. A chart that does not change a decision gets deleted. No pie charts of counts. Remember R8: tables under about 20 records get no trend charts; alerts and record-level facts carry the page instead.
4. **One complete detail table**, so the owner can verify any summary number by eye.
5. **A footer**: where the data came from, the as-of date, the "cannot answer" list (R6), and every assumption you made (status meanings, joins by name, defaults chosen).

## Visual rules

- Neutral, calm palette; red appears ONLY where a decision is needed.
- Visually separate real money from committed money (for example solid fill versus hatched).
- Readable in light and dark.
- Numbers in a monospace font with aligned digits.
- Works on a phone screen.

## The as-of date

Hard-code the as-of date to the export date and print it on the page. This page is a snapshot. Ageing and "overdue" are measured against that date, not against whenever the file happens to be opened.
