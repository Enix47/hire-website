# DJ hire site: framework decisions

Settled by three role agents (owner, customer, hire desk) after researching BJ's Sound & Lighting (Brisbane), Supersonic Hire, DJ Warehouse, Don't Stop The Party, Wwave, Kennards, Modern Musician Hire, DJ Meina, and rental software (Current RMS, Booqable, Rentman). All prices AUD incl. GST. Numbers marked *placeholder* are defaults to revisit.

## Customer request page
- Real prices on every item and package: night, weekend (Fri–Mon) and week. The cheapest rate is applied automatically (Kennards model).
- Packages are sold by guest count (Party 50, Party 120, Club Booth, All-in-one + PA), and each one lists everything in the case.
- Availability is shown as bands for the chosen dates: Available / Limited / Ask us. Never stock counts.
  - Bands are built from paid holds only. Quotes still waiting on payment show as Limited.
- Request-then-quote, not instant booking. The page always says the request is "held once deposit paid".
- Two-step form:
  - Step 1: dates, fixed pickup/return slots and gear, with a running total.
  - Step 2: name, mobile, email, pickup/delivery, suburb/zone, optional event type and guest count.
- Two required ticks at submit: 18+ and T&Cs.
- An experience question appears only when club gear is in the cart.
- Collected at quote acceptance rather than on the first request: residential address, venue, power and stairs. Photo ID is sighted at handover and never uploaded.
- Peak dates (e.g. NYE) are flagged in the date picker before submit.
- Each request gets an instant reference ("Request received", not "Booked") and a private status track: Received → Quote sent → Confirmed → Ready for pickup → With you → Checking gear → Bond released.

## Hire desk (tracker)
- Statuses:
  - Requested → Quoted (pencilled, 72h expiry) → Confirmed (deposit paid, hard hold) → Prepped (serials assigned, tested) → Out (ID sighted, bond taken) → Returned (assets need checks) → Closed (bond decision).
  - Exits: Declined, Cancelled, Expired.
  - Overdue is computed, never set by hand.
- No hold without a paid deposit.
- Quotes are built from packages and single items, plus one discount line that needs a reason. No custom bundles.
- Stock check:
  - Each product has buffers (default 2h before, 4h after).
  - Confirm is blocked on a shortage, and the clashing refs are shown.
  - Overrides need a logged reason.
- Check-in uses the product's kit checklist, marking each item OK / Missing / Damaged.
  - Damaged sets the asset to Faulty and records a fault.
  - Missing adds a pending charge.
- Late fees after a 1h grace period are added as *pending* charges. The desk confirms or waives them with a reason; nothing is taken from the bond automatically.
- A bond can't be closed until every asset passes its check. Deductions over $100 need owner approval.
- Store the ID type and last 4 digits only. Cables and stands are bulk counts; decks, mixers and speakers are serialised.
- The at-a-glance strip shows:
  - New requests
  - Pickups today
  - Returns today
  - Overdue
  - Needs check
  - Quotes expiring
  - Unpaid starting within 48h
- Numbers: revenue, quote conversion, and utilisation per product (what to buy more of).

## Pricing defaults (*placeholder*)
- Weekend about 1.5× night; week about 3× night.
- Deposit 30%. Bond floor $200; Club Booth bond $1,000.
- Delivery by zone (inner / middle / outer). Setup per hour.

## Backend shape
- **catalog**: products and packages (public read, staff write).
- **settings**: zones, slots, deposit %, peak dates.
- **inbox**: one private record per customer holding their requests and public status. Customers see only their own.
- **hires**: internal records (staff only), including customer details, lines, money, log and check-in.
- **assets**: serialised units and bulk counts with state (staff only).
- **public/availability**: per-product day bands the desk republishes after every change. No customer data and no counts.

This maps directly onto a real backend later (e.g. Postgres/Supabase tables with row-level security).

## Still to decide
Business name and branding; final rates and bond amounts; payment provider for deposits/bond pre-auth; SMS provider; delivery zone boundaries; T&Cs text; whether low-risk PA packs get instant booking later.
