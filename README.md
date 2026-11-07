# Hire website

Framework for a Brisbane DJ and PA equipment hire business. It is one page with two views that share a database:

- **Customer site** (`#book`): browse packages and single items with real night, weekend and week rates. Pick pickup and return slots, see availability for those dates, and send a two-step hire request. Each request gets a reference, and customers can follow its status from "Received" to "Bond released".
- **Hire desk** (`#desk`): the staff tracker. It has an attention strip (new requests, pickups and returns today, overdue, needs check, quotes expiring, unpaid within 48 h), plus a list of hires that runs through this pipeline:
  - Requested → Quoted → Confirmed → Prepped → Out → Returned → Closed.
  - The desk also covers stock checks, tagged-unit assignment, check-in, bond decisions, a 7-day schedule, gear states and the numbers.

Business name, branding, rates and terms are placeholders. See [`docs/decisions.md`](docs/decisions.md) for what was decided and what is still open.

## How it runs today

`index.html` is a single self-contained page built for a claude.ai Artifact. It gets its database through the Artifact runtime (`claude.use("db")`). Opened outside Claude, it renders but shows a notice instead of data.

Access rules for the prototype:

| Path | Read | Write | Holds |
|---|---|---|---|
| `catalog/*`, `settings/main`, `public/availability` | everyone | staff | Products, packages, zones, slots, deposit %, and day-by-day availability bands (no counts, no customer data) |
| `inbox/<customerId>` | that customer + staff | that customer + staff | One record per customer holding their requests and public status |
| `hires/*` | staff | staff | Internal hire records: lines, money, assigned units, ID type and last 4, charges, faults, log |
| `assets/*` | staff | staff | Tagged units (`CDJ-01`…) with state, and bulk counts (stands) |

`seed/` holds the example catalogue, settings and units used to fill the database.

## Moving to a real backend

The shape maps one-to-one onto tables:

- `catalog`, `settings`, `inbox_requests`, `hires`, `assets`
- a published `availability` view

Use row-level security matching the table above, for example with Supabase/Postgres. You will also need:

- a payment provider for deposits and card pre-authorisation of the bond
- an SMS provider for confirmations and reminders

Swap the `claude.use("db")` calls in `index.html` for that client. All the pricing, availability and pipeline logic is plain JavaScript and stays as it is.
