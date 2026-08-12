# Take Home Assessment: Inventory Ledger

## The Situation

A core feature of the inventory system is tracking stock accurately as it changes over time. Stock moves in two directions: it goes up when a delivery arrives (an invoice), and it goes down when food is prepared and served (a Kitchen Order Ticket, or KOT, sometimes called an indent).

You're given three files in the `data/` folder:

- `current_inventory.csv`, the starting stock for 53 items
- `invoices.csv`, purchase line items grouped by `invoice_id`, 20 invoices, each with several items delivered together
- `kots.csv`, consumption line items grouped by `kot_id`, 45 KOTs, each with one or more items used together

Every invoice and every KOT is unique, no two contain the exact same set of items and quantities.

## What's Different About This Dataset

`current_inventory.csv` contains 53 baseline items. `invoices.csv` and `kots.csv` reference those 53 items, plus 8 additional items that are **not** in the baseline inventory at all. Those 8 items only show up because an invoice introduces them for the first time.

This means your system needs to handle two real cases:

- **Applying an invoice for an item that doesn't exist yet in inventory should create it.** A delivery can include something you've never stocked before, the invoice is what brings it into existence.
- **Applying a KOT for an item that doesn't exist yet in inventory should fail with a clear error, not create it.** You can't consume stock of something you were never delivered. If a KOT references an item, and no invoice introducing that item has been applied yet, that's an error state your system should catch and surface, not silently ignore or auto-create.

Concretely: some of the KOTs in this dataset reference the same 8 new items. If you apply one of those KOTs before applying the invoice that first introduces that item, your system should reject it. Apply the invoice first, and the same KOT should then succeed. This is a real ordering dependency, not a data error, work through it deliberately rather than assuming every KOT is always valid.

## What to Build

### Tier 1 (required)

A system where a user can browse the invoices and KOTs from the provided data, apply them, and see inventory update correctly as a result. Applying an invoice should add its items' quantities to inventory, creating the item first if it doesn't exist yet. Applying a KOT should subtract its items' quantities from inventory, and should fail clearly if the item doesn't exist yet.

That's the goal. How you design the data model, the API, and the screens to achieve it is up to you. We're not specifying exact endpoints or exact screens.

A few things we do want, regardless of how you build it:

- An invoice or KOT is a group of line items, not a single row. Applying "INV-1001" should apply everything under that invoice ID at once, not one line item in isolation.
- Applying something twice should not double-count it. Think about what happens if the same invoice gets applied a second time by mistake.
- At any point, it should be possible to see current inventory levels reflecting everything applied so far.

### Tier 2 (required, builds on Tier 1)

Allow a user to create a new invoice or a new KOT directly, rather than only applying the ones we provided. This should let them:

- Select items from a dropdown (populated from the existing inventory list)
- Enter a quantity for each item they add
- Submit it as a new invoice (adds to inventory) or a new KOT (subtracts from inventory)

A user-created invoice or KOT should behave exactly like the provided ones once submitted, same grouping, same apply logic, same effect on inventory, including the same rule that a KOT can't reference an item that doesn't exist yet.

## Stack

- Backend: FastAPI, with SQLite or any file-based database
- Frontend: a web app, React or plain HTML/JS, your choice
- Load all three CSVs as a first step, and write this as a real import step, not a one-off snippet, since everything else depends on it

## Explicitly Out of Scope

- Real authentication or user accounts
- Uploading real invoice files or images, or any OCR. The CSVs are already the extracted data, treat them as ground truth input
- Deployment of any kind
- Automated tests (fine as a bonus, not required)

**On AI:** not in scope for this assessment, there is no requirement to use or integrate any AI or ML component anywhere in the system. If you want to implement something AI-related anyway on top of Tier 2, feel free to, it just won't be scored as a requirement.

## PROCESS.md

Fill this in as you go, not reconstructed at the end. A template is included in this repo. It asks about how you interpreted the goal, your data model and why, how you handled the new-item and missing-item cases, how Tier 2 fits into your Tier 1 design, and one decision you'd reconsider with more time.

## Optional Stretch Goals

Pick any of these, however many you get to. Attempt them only after Tier 1 and Tier 2 both work end to end.

- Undo: allow un-applying an invoice or KOT and correctly reversing its effect on inventory
- A simple audit view: for any item, show the history of what added or removed stock and when
- Flag any item whose current quantity has fallen below a threshold you define
- A couple of automated tests around the apply logic specifically, since that's the part most likely to have a subtle bug

A clean, fully working Tier 1 and Tier 2 beats an ambitious but broken attempt at the stretch goals.

## What to Submit

Push your work to this repository, committing as you go rather than in one final commit. We're interested in how the work progressed, not just the end state.

1. Your code (backend and frontend)
2. `PROCESS.md`, filled in
3. Setup and run instructions for both backend and frontend
