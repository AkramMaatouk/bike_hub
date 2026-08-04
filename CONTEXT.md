# CONTEXT.md — Bike Shop Inventory App

Domain glossary, resolved through a grilling session. Terms here are the ones
that should show up in code, verbatim — not synonyms, not "close enough."

## Scope (v1)

- Internal, staff-only tool. No customer-facing layer.
- No repair/workshop ticketing — the shop is 1–2 people, so a formal job
  status workflow has no one to coordinate with. May revisit if the team grows.
- Tracks parts/consumables and accessories only (not complete bikes).
- Single shop location — confirmed, not an assumption.
- Initial stock gets entered manually by staff — no bulk import in v1.
  Revisit if the real SKU count turns out to make that painful.

## Ubiquitous language

**Item** — a stocked good the shop carries (a part, consumable, or
accessory). Has a name, a `Category`, a current quantity, a per-item
reorder threshold, and an optional free-text code (SKU/manufacturer
barcode) staff can record — not required, not scanned in v1. Identity is a
system-generated internal ID, not the code.

**Category** — a named label on an `Item` (e.g. "Tubes", "Brake pads",
"Apparel"). A small, staff-managed list: seeded with a starter set, staff
can add new ones from the app as needed. Deliberately minimal — a name and
nothing else, no independent rules or attributes. Not free text (avoids
"Tube" vs "tube" vs "Tubes" drift); not hardcoded (avoids a code change to
add "E-bike batteries" next year).

**StockMovement** — an immutable, timestamped record of a quantity change to
an `Item`. Two kinds:
  - **Adjustment** (relative): `+N` (in) or `-N` (out), always tagged with a
    `MovementReason`.
  - **Correction** (absolute): sets quantity to a physically-counted number,
    for reconciling drift between the record and reality (recounts).

**MovementReason** — the "why" on a `StockMovement`.
  - In: `Restock`, `Correction`
  - Out: `Sold`, `UsedInternally`, `DamagedOrLost`, `Correction`
  - Deliberately deferred: `ReturnedToSupplier` — not the current pain point.

**Reorder threshold** — per-`Item`, staff-configurable. An `Adjustment` that
crosses an `Item`'s quantity below its threshold raises the low-stock
signal (see ADR-0001 for how this is delivered).

## Domain rule

An `Adjustment` may never take an `Item`'s quantity below zero — the app
rejects an "out" movement that would do that, since it's almost always a
data-entry error, not a real state. A `Correction` can always set an
arbitrary count to reconcile with a physical recount; it is the one
sanctioned way to make quantity "jump."

## v1 operational notes

- **Low-stock alert delivery**: a count badge in the nav. Clicking it
  navigates to the existing item list, pre-filtered to items at or below
  their reorder threshold — no dedicated low-stock screen. Keeps the
  `AlertPort`'s v1 adapter genuinely thin.
- **Movement ledger**: every `StockMovement` is still recorded from day
  one — the negative-stock invariant and the low-stock signal both depend
  on it existing. What's deferred is a UI to *browse* that history; the
  data is there, just not surfaced yet.

## Deferred (explicitly out of scope for v1)

Written down on purpose, so scope doesn't quietly creep back in:

- Repair/workshop ticket tracking and job status.
- Customer records or purchase history — stock movements stay anonymous.
- Complete bikes as inventory items.
- Unit cost / inventory valuation reporting.
- `ReturnedToSupplier` as a movement reason.
- Supplier entity or automated reordering.
- Public/customer-facing layer, SEO.
- Movement history / audit trail UI (data is captured; the view isn't).
