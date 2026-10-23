# breakthrough-database-skills

Two Claude Code skills for a business owner who has data but no system that answers questions with it.

| Skill | Job |
|---|---|
| `breakthrough-base-schema-design` | Before the data exists. Interviews you, finds the one unit of work your business repeats, settles where every kind of record is born, and hands you a Lark Base blueprint with a phased build order, an acceptance test, and a Plan Sheet a fresh session builds the Base from. |
| `breakthrough-database-to-dashboard` | After the data exists. Reads whatever you already have (Airtable, Notion, Sheets, Lark Base, Excel, CSV) and returns a plain-language account of it, a findings list, and one page answering your most urgent money question. |

They sit either side of the same gap. Design first if you have nothing yet; go straight to the dashboard if you already have five years of rows in a spreadsheet nobody trusts.

## Install

```
npx skills add -g breakthrough-edu/breakthrough-database-skills
```

One command installs both. The `-g` installs them for your user account; without it they land in whatever folder you happened to be standing in, which is the most common reason a skill "did not install".

## One home per kind of record

A Base that pulls together what lives in your accounting software, your CRM, your HR system and your own spreadsheets is a data hub. It only stays trustworthy if every kind of record has one home: invoices are born in the accounting software, so the Base holds a copy that flows one way, in, and nobody edits it there. Some records are born in the Base itself (projects, tasks). Some are requested in the Base and issued elsewhere (an order the accountant turns into an invoice). Each kind gets exactly one home and one direction, and the blueprint shows which is which, who owns each copy, and how far each connection has actually been proven.

## Why these are skills and not prompts

Both started life as long prompts students pasted into a chat. That worked until it did not:

- A wall of pasted text is a bad first experience for someone who has used Claude Code for two days.
- The schema prompt opened by declaring an identity for the whole session. Pasted into a session that already had a second brain running, it took the session over, and the assistant stopped recognising the user's own vault.

A skill loads when it is needed, does its job, and leaves everything else in the session alone. Both skills here say so explicitly in their own bodies, and each keeps its long procedure in `references/` files read only at the stage that needs them, so nothing is carried in context that the current step does not use.

## Coexisting with a second brain

Neither skill governs anything outside the job it was invoked for. If you have a vault built by [my-second-brain](https://github.com/breakthrough-edu/my-second-brain), that vault's own rules keep deciding where files land; these skills offer to save their output and let the vault's filing law choose the destination.

Neither skill requires a vault. They work on their own.

## What they do not do

- `breakthrough-base-schema-design` designs; it does not build the Base in the design session. It hands over a Plan Sheet, and a new session builds from it step by step with the Lark skills you have installed. It does not pick connector tools or promise that your software can be connected: each connection is written down at the state it has been proven to.
- `breakthrough-database-to-dashboard` produces a snapshot, not a live dashboard. It says so on the page, with the as-of date printed, so nobody reads a three-week-old number as today's.
- Neither invents figures. What cannot be found is written as "not tracked", never as zero, and each dashboard ends with what that data cannot answer.

## Origin

Distilled from two handouts written for the 2nd Brain Intensive, the two days that open a Breakthrough Circle membership at Breakthrough EDU. The craft in them came from designing real operating bases and real dashboards; the specifics of those businesses are not in here.

MIT licensed.
