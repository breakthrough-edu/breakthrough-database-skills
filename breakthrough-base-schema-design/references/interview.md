# The interview

Run these phases in order. **One question at a time. Wait for the answer.** Do not batch questions into a list, and do not move to the next phase until the exit condition is met. Restate what you heard in their own words at the end of each phase and get a yes.

⛔ **Do not propose a single table until Phase 6 is done.** They will push you to start designing early. Hold the line once, explain that the tables fall out of the numbers they want, then continue.

## Phase 0, Orientation

- What does the business do, in the words you would use with a friend?
- Walk me through a normal week.
- Where does the information live right now? Which WhatsApp groups, which spreadsheets, whose head?

Exit: you can state the business in one sentence and name the pain in one sentence.

## Phase 1, Find the spine

- Finish this sentence: last year we did about \_\_\_ of these.
- Can you point at one and tell me when it started and when it finished?
- Does money come in per one of these?
- Does more than one person touch one?

Common spines by industry: project, job, case, engagement, order, booking, event, shipment, patient episode, cohort, campaign, listing, retainer month, production batch.

Two failure modes to catch. If their candidate is something a single person finishes in an hour, it is a task, so go one level up. If their candidate is a company they have worked with for six years, it is a client, so go one level down. When they run a retainer, name the fork out loud: the spine is either the retainer relationship with monthly invoice rows under it, or the retainer month itself. Pick based on whether work scope changes month to month.

Exit: a named unit, a plural noun, a rough annual volume, and one real example named out loud.

## Phase 2, Lifecycle

- From the very first contact to money in the bank, what are the states one of these passes through?
- Who moves it from each state to the next?
- What has to be true before it can move?

Exit: an ordered list of four to eight stages, each with the trigger that advances it. Fewer than four means they are compressing. More than eight means some of those are tasks, not stages.

## Phase 3, Money in

- How do you charge? One price up front, milestones, monthly retainer, per unit, subscription?
- Do you quote before you invoice? Does the quote need approval or a purchase order?
- Is there tax, and at what rate?
- Which currencies?
- When do you invoice versus when does the money actually arrive?

Exit: you can draw the revenue document chain (for example enquiry, quotation, purchase order, invoice, payment) and you know whether they manage the business on cash or accrual.

## Phase 4, Money out

- For one of these, who do you pay? Freelancers, suppliers, ad platforms, subcontractors, nobody?
- Do you need to know profit on a single one of these, or only for the month?
- Do you pay in a different currency from the one you bill in?

This is a genuine fork. Plenty of businesses (coaching, consulting, software) have no per-unit variable cost worth tracking. If so, say plainly that you are not building a cost table and that gross profit per unit is not a question their base will answer, then move on.

Exit: either the shape of the cost side, or an explicit decision not to build one.

## Phase 5, People and parties

- Who inside the business touches one of these, and what are their roles?
- Do you need to see who is overloaded?
- Who outside the business is involved, and do you bill them, pay them, or both?
- Do you collect anything from clients or suppliers at onboarding: bank details, tax numbers, registration documents?

Exit: which counterparty and people tables are needed, and roughly what each carries.

## Phase 6, The weekly number

- What number do you want to see on Monday morning?
- What question about the business takes you more than ten minutes to answer today?
- Last time a number surprised you, what was it?
- At month end, what report do you or your accountant assemble by hand?

Exit: three to six named metrics. Write each one down, because this list is the acceptance test for the entire design. Any table or field that does not feed one of these metrics or one of Phase 7's proofs has to justify itself under derivation rule R13.

## Phase 7, Proof and paperwork

- If a client disputes an invoice a year from now, what do you need to pull up?
- If the tax office asks, what do you have to show?
- What do you currently screenshot and send over WhatsApp?

Exit: the attachment field list, each one placed on the row where that document is actually produced.

# Design, then challenge

After Phase 7, present the blueprint (format in [blueprint-and-build.md](blueprint-and-build.md)). Then run two mandatory checks before the user builds anything.

**Walk two real jobs through it.** Ask them to name one past unit of work that went smoothly and one that went wrong: scope creep, a late payment, a vendor dispute, a cancellation. Walk each one through the design row by row, out loud. Anything they cannot record is a missing field. Anything they can record but would never look at is a field to delete. The one that went wrong is worth more than the one that went well.

**Run the kill list.** Go field by field and ask who reads this and when. Cut anything without an answer. Expect to remove fifteen to thirty percent of what you first drafted. Tell them that is normal and is the point.
