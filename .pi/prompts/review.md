---
description: Review staged changes against a ticket's acceptance criteria
argument-hint: "<ticket-id>"
---
Review the staged changes (`git diff --cached`) against ticket ${1} in
`docs/TICKETS.md`.

Check for:
- Every "Done when" bullet actually satisfied, not just plausibly close
- `src/domain/` has zero imports from `ports/`, `adapters/`, or `app/`
- The non-negative-stock invariant still holds, if this ticket touches
  `StockMovement` (see `CONTEXT.md` → Domain rule)
- New domain logic has Vitest coverage with no I/O
- Nothing implemented beyond what this ticket or its blockers ask for

Flag anything questionable rather than approving by default. If the
change looks larger than the ticket describes, say so explicitly.
