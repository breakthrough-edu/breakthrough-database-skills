# The blueprint, the phasing, and the close

## Output format

Deliver a single document called the Base Blueprint.

```
# <Business name> Base Blueprint

## The spine
One row per <unit>. Roughly <N> per year.

## Stage flow
<stage> -> <stage> -> <stage>   (trigger for each transition)

## Where each kind of record is born
| Kind of record | Home | Role in this Base | Direction | Source Ref | Match key | Freshness | Data Owner | Sync Owner | Who may edit here | Request route | State |
|---|---|---|---|---|---|---|---|---|---|---|---|
| ... | ... | Source (born here) / Mirror (a copy, look do not touch) / Intake (a request sent to its home) / Snapshot (frozen on a date) | born here / copied in / requested here | ... | ... | ... | ... | ... | ... | ... | Route mapped / Base structure ready / Mirror connected / Sync verified |

Rows for kinds born in the Base need only the first four columns and "Who may edit here".

## Tables

### <Table Name>
Role: Source / Mirror / Intake / Snapshot
Purpose: one row per <thing>.
Primary field: <what the row is called>
Links: to <Table> (many <this> to one <that>), or Match key: <field> matched to <Table>.<field>

| Field | Type | Who writes | Notes / formula |
|---|---|---|---|
| ... | ... | a person (who) / formula / Lark (auto number, created time) / the route | ... |

Views:
- <view name>: <type>, grouped by <x>, filtered to <y>, for <who and when>

## Metric traceability
| Monday morning question | Where the answer comes from | How old it can be |
|---|---|---|
| ... | ... | live / as of last import <date> / as of month end |

## Routes
- <kind of record>: <home> -> Base, by <route if known, else "not known yet, ask the vendor">. State: <state>. Proof still owed: one real change made at the home, seen arriving here.

## Build order
1. ...

## Deliberately not built
- <thing>: <why, and what would have to change to justify it>

## Phase 2 backlog
- ...
```

Every figure in the blueprint is one the owner said or a blank waiting for one. ⛔ Never invent a number, a tax rate, or an annual volume to make the document look finished.

## Phasing

Never ship eleven tables at once. Sequence delivery so the base is useful within a week.

- **Phase 1, the spine and the money.** Spine table, one revenue table, one counterparty table, the stage field, the rollups, two views. This alone answers "what is live and what is owed." When the revenue table is a Mirror and no route works yet, Phase 1 builds its structure and fills it with one export imported by hand, labelled with its date; "what is owed" is then as of that date, and the blueprint says so.
- **Phase 2, the work and the people.** Tasks, deliverables, the people table, workload rollups.
- **Phase 3, the reporting layer.** Cost side if it exists, the period table, the monthly dashboard, forms for onboarding.

Say out loud which phase each table belongs to when you present the blueprint, and get agreement on Phase 1 scope before discussing the rest.

Routes sit outside the three phases. Each one is its own job, done when it can be done, and each is reported at the state it has proven.

## Finishing

Close with:

1. The Base Blueprint document.
2. The Phase 1 build order, numbered, with nothing in it that is not Phase 1. The full sequencing logic is in [lark-mechanics.md](lark-mechanics.md).
3. The metric traceability table, framed as the acceptance test: build it, enter two real records, and if any of these numbers is wrong, the design is wrong and you come back. For every route, the second half of the test: one real change at the home, seen arriving in the Base, no duplicate, nothing written back. Until then the route is reported at its honest state, never as done.
4. One sentence on what you deliberately left out and what would have to change for it to be worth adding.
5. The Plan Sheet, written by the rules in [plan-sheet.md](plan-sheet.md), and one line telling the owner how to use it: open a new session and ask it to build the Base from the sheet.

Then offer to save the blueprint and the Plan Sheet as markdown files where the user chooses. If they keep a second-brain vault, their vault's own filing rules decide where it lands; this skill does not override them.
