# AGENTS.md

## Project
Internal, staff-only inventory tracking app for a small bike shop (parts,
consumables, accessories — not full bikes, no repair workflow). Full
context lives in three places, read them before acting, not just once:

- `CONTEXT.md` — domain glossary and rules. Read before touching anything
  under `src/domain` or `src/application`.
- `docs/adr/` — architecture and infrastructure decisions. Read before
  restructuring folders, adding a dependency, or introducing a new port.
- `docs/TICKETS.md` — the only backlog. Work it top-to-bottom, respecting
  each ticket's blocking edges. Don't start a blocked ticket.

## Workflow
- Implement one ticket at a time. Reference its ID (e.g. `T4`) in commit
  messages and PR titles.
- Run lint, typecheck, and `vitest` before considering a ticket done.
- Open a PR per ticket. Once it has a matching GitHub Issue, put
  `Fixes #N` in the PR description so merging closes it automatically.

## Architecture
- Respect the ports & adapters boundary from `docs/adr/0001-*`:
  `src/domain` has zero imports from `ports`, `adapters`, or `app`. See
  `src/domain/AGENTS.md` for the stricter rules that apply inside it.
- Don't add a new port, adapter, or dependency without checking it against
  the existing ADRs first — if it's not covered, ask instead of assuming.

## Stack
Next.js · Drizzle (SQLite dev / Postgres prod) · Vitest · Tailwind +
shadcn/ui · GitHub Actions · Sentry. Rationale for each in
`docs/adr/0002-v1-infrastructure-choices.md` — don't swap one out without
a new ADR.

## Restraint
- Build only what the current ticket and `CONTEXT.md` describe. If a
  ticket seems to need something neither covers, stop and ask rather than
  filling the gap yourself.
- Prefer the standard library and what's already a dependency over adding
  something new.
- Ponytail is installed for this — if it pushes back on scope, treat that
  as a signal to recheck the ticket, not something to route around.
- Graphify / code-review-graph isn't installed yet. Add it once `T1`
  exists and there's a real codebase to map — not useful before that.

## Git safety
- Never run `git reset --hard`, `git checkout .`, `git clean -fd`,
  `git stash`, `git add -A` / `git add .`, or `git commit --no-verify`.
- Never force-push.
