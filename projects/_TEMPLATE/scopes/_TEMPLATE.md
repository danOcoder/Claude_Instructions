# Scope: {Outcome, not a ticket number}

**{Ticket number, or the several tickets this spans}.** {One or two lines: what's being built, where.}
Driver: {deadline, demo, dependency — or delete this line.}

Scope Model: {Notion URL}
{Other background pages worth reading before code — or delete.}

{The one framing sentence that stops the wrong mental model. For example, "the work is
page-level, not component-level" — the thing that, if assumed wrong, wastes a day.}

## Coordination

{Who else is touching these files, on what branch, and what the merge posture is. Delete if
nobody else is in here. Be specific about which files are contested and whether editing them is
fine or off-limits — "avoid the file" and "expect a conflict" are different instructions.}

## Where things are (verified in source {date})

```
{Only the files this scope actually touches, with a one-line role each. Not a repo tour —
the project layer covers topology. This is the working set.}
```

{Component signatures and mount conditions that aren't obvious from the file names.}

## The contract

> {Verified how, when, against what branch. If it's off an unmerged branch, say so and say to
> re-confirm on merge.}

{Endpoint tables. Split by branch/state when a contract is mid-change — showing only the target
shape hides why the current code looks wrong.}

{Semantics that the route signature doesn't carry: merge vs replace, null handling, whether a
full-state rebuild is safe. This is usually the highest-value paragraph in the file.}

## {The domain object — enum values, kinds, naming}

{Machine names, exact spelling, where confirmed. Value lists with their source file. Whether the
list is exhaustive, and what to do with values outside it.}

## Gotchas (verified in source {date})

- {Each one: the trap, why it bites, and what to do instead. If it's fixed, delete it — a
  crossed-out gotcha is a corrections-log entry, not a gotcha.}

## Open

- {Genuinely undecided, or blocked on someone. Each with what unblocks it and who owns it.}

## Resolved — do not re-open

- {Decisions already made, with the date and who made them where it matters. This section is
  what stops the same debate every session.}

## Corrections log

Facts that were wrong once. Kept so they don't get re-derived.

- {"X" — wrong, it's actually Y. One line each. When a correction turns out to be a durable
  subsystem fact rather than a scope fact, graduate it to the project layer or Notion Reference
  and delete the line here.}
