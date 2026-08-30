# Phase 2: Understanding the data

Do this work without narrating every step. The user sees the result, not the process.

## Build your own picture

Every table, every column, and what each column REALLY contains: **sample the values, do not trust the header.** Detect how tables refer to each other, through explicit links or through matching values (codes, names, invoice numbers). Where you join by matching values, state that assumption later on the page, because name-matching breaks on duplicates.

## Read what the owner wrote, not only the data

Column descriptions and notes, and the views someone built (kanban, calendar, timeline, saved filters, and what those views are named), carry business logic that the columns alone can never show you: why a field was moved from one table to another, which code is the real identity when two customers share a name, what an odd status value actually means in practice, what the team looks at every morning. These notes are often the single richest thing in the whole database, and they are also the first thing an export destroys.

- Reading through a connector: capture them.
- Working from an export: say so, and ask for them cheaply, for the two or three tables that matter most: a screenshot of the column settings, or simply "is there anything about how these columns work that you would have to explain to a new hire?"

## Find the spine of the business

What is the unit of value (an order, a job, a booking, a student, a patient, a table of diners, a shipment), and how money and time attach to it: money in, money out, the dates things were quoted, promised, issued, delivered, and paid, the statuses, and the people.

## Deliver the account (deliverable one)

Present a short account the user can read in two minutes:

- One plain sentence per table.
- The spine described simply ("everything hangs off one row per job").
- The 3 to 8 most important gaps and data-quality problems you found.

## Then ask your questions

- **At most 5, all in ONE batch, all business questions, never technical ones.**
- Only ask what changes the dashboard: which status values mean money actually arrived, what profit margin they consider acceptable, what "active" means to them, which currency applies if it is ambiguous, and what date the export was taken if you cannot tell.
- For everything else, choose a sensible default and say what you chose.
