# ADR-0001: Adopt hexagonal architecture as a deliberate learning vehicle

## Status
Accepted

## Context
The v1 domain (`Item` + `StockMovement` + a low-stock signal, see
CONTEXT.md) is small enough that a plain CRUD app — one table, a quantity
column, a query for items under threshold — would satisfy the literal
requirements. Hexagonal architecture is not forced by domain complexity at
this scale.

This project's goals explicitly include learning systems design, DDD, and
hexagonal architecture in a real, working codebase, for portfolio and
learning purposes. That's a legitimate reason to take on structure the
domain doesn't yet strictly require — as long as it's a conscious trade-off,
not an accident.

## Decision
Adopt hexagonal (ports & adapters) architecture, with two ports chosen
because each has a concrete, demonstrable payoff rather than being applied
on faith:

- **Repository port** — persistence starts as SQLite during development;
  can be swapped to Postgres in production without touching domain logic.
- **Alert port** — low-stock notification starts as an in-app signal; can
  later be swapped or extended to email/SMS without touching domain logic.

The domain core (`Item`, `StockMovement`, the negative-stock invariant, the
threshold-crossing signal) has no dependency on either port's concrete
implementation.

## Consequences
- More upfront structure than the domain strictly requires at this scale —
  an intentional trade-off for learning value, made explicit here rather
  than left implicit.
- Two adapters must exist for v1 even though only one variant of each is
  needed today (a SQLite repository, an in-app alert). This is what makes
  the pattern's value testable rather than theoretical — we should be able
  to swap one adapter later and prove the domain layer didn't change.
- Future features (repairs, a customer-facing layer) can be added as new
  use cases against the same core, or as new bounded contexts, without this
  decision needing to be revisited.
