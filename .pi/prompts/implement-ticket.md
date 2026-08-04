---
description: Implement a ticket from docs/TICKETS.md
argument-hint: "<ticket-id>"
---
Open `docs/TICKETS.md` and find ticket ${1}.

Check its "Blocked by" line first. If any listed ticket isn't done, stop
and say so instead of starting.

Read `CONTEXT.md` and any ADRs in `docs/adr/` that ticket ${1} touches, so
the terms and rules you use match what's already decided.

Implement only what ticket ${1}'s "Done when" bullets ask for — nothing
extra. If it seems to need something outside what `CONTEXT.md` or the ADRs
define, stop and ask instead of guessing.

Write tests for any new domain logic (Vitest, no I/O, no mocks). Run the
full test suite before finishing.

When done, report against ticket ${1}'s "Done when" bullets, one line each.
