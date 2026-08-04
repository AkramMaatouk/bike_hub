# ADR-0002: v1 infrastructure choices

## Status
Accepted

## Context
With the domain settled (CONTEXT.md, ADR-0001), v1 needs a concrete
framework, persistence, and auth approach sized to a staff-only tool for a
1–2 person shop — not a hypothetical larger team.

## Decision
- **Framework**: Next.js. API routes give the application layer's driving
  adapter a home; deploys to Vercel with no config; matches v0's component
  output for the UI layer.
- **Persistence**: Drizzle ORM over SQLite in development, Postgres in
  production — the concrete implementation behind ADR-0001's repository
  port.
- **Auth**: a single shared staff passcode behind a session cookie, not a
  full user-account system — sized to the actual team, not a bigger one.
  Revisit if the team grows past "everyone already knows everyone."
- **Testing**: Vitest against the domain core (pure functions, no I/O
  required to test them) plus one integration test on the real repository
  adapter. No e2e for v1.
- **CI/CD & monitoring**: GitHub Actions (lint, typecheck, test on PR) and
  Sentry error monitoring from day one. Treated as process/habit rather
  than domain scope, so it's exempt from the "add complexity gradually"
  rule that governs feature scope.

## Consequences
- Auth needs rework if the shop adds staff beyond the size where a shared
  passcode is appropriate — an accepted, cheap-to-defer cost.
- The SQLite → Postgres swap is a config change plus a migration run, not a
  rewrite, because the repository port already isolates it (ADR-0001).
