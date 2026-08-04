# TICKETS.md — v1 tracer-bullet tickets

Read alongside `CONTEXT.md` (domain) and `docs/adr/` (architecture, infra) —
this file only sequences the work, it doesn't re-explain it. Each ticket
names its blocking edges explicitly, in place of a real tracker.

### T1 — Project skeleton
Blocked by: none · Blocks: everything else
Stand up the hexagonal skeleton end-to-end before any real feature exists.
- Next.js app scaffolded with `src/domain`, `src/application`, `src/ports`,
  `src/adapters`, `src/app` per ADR-0001.
- Drizzle + SQLite wired through a real `InventoryRepository` port/adapter.
- One hardcoded round-trip proves it: seed a dummy record, read it back
  through a use case, render it on a page.
- `src/domain/AGENTS.md` created: no imports outside the domain layer,
  pure functions only, no I/O — the rules the root `AGENTS.md` refers to.

### T2 — CI/CD + Sentry
Blocked by: T1 · Blocks: nothing (parallel track)
Makes ADR-0002's "from day one" call real.
- GitHub Actions runs lint + typecheck + `vitest` on every PR.
- Sentry initialized, catches a deliberately-thrown test error.

### T3 — Passcode auth gate
Blocked by: T1 · Blocks: nothing directly, but land before real deployment
- Unauthenticated requests redirect to a passcode entry screen.
- Correct passcode sets a session cookie; app works normally after.

### T4 — Category management
Blocked by: T1 · Blocks: T5
The staff-managed Category list from CONTEXT.md.
- Starter set of categories seeded.
- Staff can add a new category from the app.

### T5 — Add Item
Blocked by: T4 · Blocks: T6, T7
Create an Item — name, Category, reorder threshold, optional code.
- Required fields validated; code stays optional.
- New items start at quantity 0.

### T6 — List & filter Items
Blocked by: T5 · Blocks: T8
The main working screen.
- List shows name, category, quantity, threshold.
- Filterable by category and by "at/below threshold."

### T7 — Record stock movements
Blocked by: T5 · Blocks: T8
Both `Adjustment` and `Correction` from CONTEXT.md, one form.
- Adjustment (in/out + `MovementReason`) enforces the non-negative
  invariant.
- Correction sets an absolute quantity, no reason required.
- Every movement writes to the ledger.

### T8 — Low-stock signal
Blocked by: T6, T7 · Blocks: none — capstone ticket
The actual value driver.
- An Adjustment crossing an Item below its threshold raises the domain
  event.
- Nav badge shows a live count; clicking it opens the item list
  pre-filtered to low-stock.

---

## Tracking

GitHub Issues + Projects on the same repo — no extra infrastructure, and a
PR description with `Fixes #N` closes the linked issue automatically on
merge, which is the same PR↔issue state-sync Plane would've given you, for
free. One Project board (Todo / In progress / Done) covering these 8
tickets is enough at this size — no need for Cycles/Modules-style
structure yet.

To create them: `gh issue create --title "T1 — Project skeleton" --body
"..."` per ticket, or paste the blocks above in by hand. Either way,
reference the ticket ID (T1, T2…) in commit messages and PR titles so
Pi's changes stay traceable back to this file even before they're real
Issues.
