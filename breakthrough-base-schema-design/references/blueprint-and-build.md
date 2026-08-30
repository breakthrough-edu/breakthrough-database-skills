# The blueprint, the phasing, and the close

## Output format

Deliver a single document called the Base Blueprint.

```
# <Business name> Base Blueprint

## The spine
One row per <unit>. Roughly <N> per year.

## Stage flow
<stage> -> <stage> -> <stage>   (trigger for each transition)

## Tables

### <Table Name>
Purpose: one row per <thing>.
Primary field: <what the row is called>
Links: to <Table> (many <this> to one <that>)

| Field | Type | Notes / formula |
|---|---|---|
| ... | ... | ... |

Views:
- <view name>: <type>, grouped by <x>, filtered to <y>, for <who and when>

## Metric traceability
| Monday morning question | Where the answer comes from |
|---|---|
| ... | ... |

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

- **Phase 1, the spine and the money.** Spine table, one revenue table, one counterparty table, the stage field, the rollups, two views. This alone answers "what is live and what is owed."
- **Phase 2, the work and the people.** Tasks, deliverables, the people table, workload rollups.
- **Phase 3, the reporting layer.** Cost side if it exists, the period table, the monthly dashboard, forms for onboarding.

Say out loud which phase each table belongs to when you present the blueprint, and get agreement on Phase 1 scope before discussing the rest.

## Finishing

Close with:

1. The Base Blueprint document.
2. The Phase 1 build order, numbered, with nothing in it that is not Phase 1. The full sequencing logic is in [lark-mechanics.md](lark-mechanics.md).
3. The metric traceability table, framed as the acceptance test: build it, enter two real records, and if any of these numbers is wrong, the design is wrong and you come back.
4. One sentence on what you deliberately left out and what would have to change for it to be worth adding.

Then offer to save the blueprint as a markdown file where the user chooses. If they keep a second-brain vault, their vault's own filing rules decide where it lands; this skill does not override them.
