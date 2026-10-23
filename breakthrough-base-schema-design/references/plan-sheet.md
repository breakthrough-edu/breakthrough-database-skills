# The Plan Sheet

The blueprint is for the owner to read. The Plan Sheet is for a **fresh session** to follow: the owner opens a new Claude Code session, points it at the sheet, and that session builds the Base. The build session was not in the interview and must never need it, so the sheet carries everything: what to build, in what order, how to prove each step, where to stop, and what it must never do.

Write it after the blueprint is agreed, as a separate markdown file named `<Business>-Base-Plan-Sheet.md`, saved beside the blueprint. In a second-brain vault, the vault's own filing rules decide where both land.

## Writing rules

- **Everything exact.** Table names, field names, field types, select options, formula text, link directions, view filters: written out in full, exactly as they will be created. The build session copies; it does not interpret.
- **Blanks stay blank, loudly.** Any figure the owner did not give (a tax rate, a currency, a commission percentage) is written `⬜ ASK OWNER: <question>`. The build session stops at that step and asks. ⛔ Never fill a blank to make the sheet look finished.
- **The blueprint's Phase 1 only** (its first build phase, not an interview phase), unless the owner asked for more. Later phases are listed at the bottom as "not in this build".
- **The source map is copied in whole.** It is the law of the build: it decides which tables are Mirrors and therefore what nobody may type into.
- Written in English for the build session, with the owner's own names for tables and fields. A Chinese table name stays Chinese.

## Template

````markdown
# <Business> Base Plan Sheet

Made: <date> · From: <Business> Base Blueprint · Owner: <name>

## How to use this sheet
Open a new Claude Code session in this folder and say:
"Build my Lark Base from <this file's name>. Follow the rules in it."

## Rules for the build session (read before anything else)
1. This sheet is the whole job. Build exactly what is listed, in the order listed. Add nothing that is not on it, and touch no other Base.
2. Use the Lark skills installed in this session for every Lark command. If the right command does not exist or refuses, say so plainly and stop that step. Do not improvise a workaround.
3. After every step, read the result back from Lark with a list command (list tables, list fields) and compare it with this sheet. A command saying "ok" proves nothing; only the read back counts.
4. Never create an automation, a workflow, a sync, or anything that writes to another system. Routes in the source map are built later, as a separate job.
5. Never type into a Mirror table, except the hand import in the checkpoint that says so.
6. Never invent a figure. A step marked ⬜ ASK OWNER stops until the owner answers.
7. Never delete or change the type of an existing field or table without the owner's yes in this session. Changing a field's type can silently rewrite its values.
8. Stop and wait for the owner's yes at the three points marked ✋. At every other checkpoint, tell the owner in two plain sentences what now exists, then continue.
9. Keep the Status block at the bottom up to date as you go, so a later session can pick up where this one stopped.

## Before you start (preflight)
- [ ] The Lark CLI answers and is logged in, to the right Lark (international Lark and Feishu are different services; check which one the owner uses).
- [ ] The Lark Base skill is installed in this session.
- [ ] The owner's Lark admin has approved the app's permissions, and the CLI was logged in again after approval.
If any box fails, tell the owner in one sentence what to install or ask for, then stop.

✋ **Confirm the target.** Create a new Base named "<name>", or build inside this existing Base: <URL>. If it is an existing Base, list what is already in it and show the owner before changing anything. A new Base may come with a default table already in it: rename it into the first table of CP1 rather than leaving it or deleting it.

## Source map
<copied in whole from the blueprint>

## Steps

### CP1 · Parent tables only (people, counterparties, classification), with their own plain fields
1. Create table `<Name> Table` (role: Source). Fields:
   | Field | Type | Options / format |
   |---|---|---|
   Read back: list fields, compare.
...

### CP2 · Create the spine table, with only its own plain fields
### CP3 · Create the child tables, with only their own plain fields (Mirror tables get their four receipt fields here: Source Ref, Sync Status, Last Synced, Sync Note, plus their match key if they have one)
### CP4 · Links (create from the child pointing at the parent; rename the reverse field on the parent immediately, before anything references it)
### CP5 · Formulas (pull-throughs on children first, then totals on the spine; totals over Mirror rows use the match key, written out in full), then the period table if it is in this build
### CP6 · Views (one line each: name, type, grouping, filter, who reads it), and forms only if the blueprint puts them in this build
### CP7 · Hand import into Mirrors (only if the source map lists one)
✋ Ask the owner for the export file from the home system, and make sure it covers the two real records CP8 will use, or CP8 cannot check what is owed on them. Import it, set `Sync Status = Imported by hand` and `Last Synced` = the export's date on every row, then count the rows and compare with the file.
### CP8 · Acceptance
✋ Ask the owner for two real past records, one that went smoothly and one that went wrong. Enter them into the Source tables. Then fill the table below from what the Base shows, and check each number by hand with the owner.

| Monday question | Where it comes from | Expected (owner says) | Base shows |
|---|---|---|---|

Any mismatch means the design or the build is wrong. Write it in the Status block and stop.

## Not in this build
- <later phases, each one line>
- Routes: <each route, its current state; none of these are built by this session>

## Status
| Step | Done | Read back matched | Note |
|---|---|---|---|

Routes: <kind> · <state>
Last session: <date>, stopped at <step>, because <reason>
````

## How the build session resumes

A second session given the same sheet reads the Status block first, then lists what exists in the Base and compares it with the sheet up to the last finished step. ⛔ If the Base no longer matches (someone renamed a field, deleted a table, added columns by hand), it reports the differences to the owner and waits. It never "repairs" toward the sheet on its own, because the owner may have changed the design on purpose.

## What done means

The build is done only when every step is marked done with its read back matched, the acceptance table is filled from the owner's two real records and every number agrees, and the Status block says so. A Base that has never had a real record through it is a drawing, not a system.

Routes are not part of done. Each stays at the state it has reached, written honestly in the Status block: a hand import is "Base structure ready", not "Mirror connected".
