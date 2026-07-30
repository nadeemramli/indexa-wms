# 02 — Requirements / Feature Specs (v1)

Functional requirements for INDEXA WMS v1, organized by module. Each module maps to a pain point in [`01-current-state.md`](01-current-state.md).

**Key decisions baked in (2026-07-30):**

- Internal-only app: users are Nadeem (admin) + operational team. **No seller/customer logins** — sellers and customers stay on WhatsApp.
- Tech: Supabase + Vercel web app, mobile-first.
- Batch/lot + expiry tracking is **mandatory** (cold-chain vials, batch numbers, expiry dates).
- Order confirmed **on payment**. No partial fulfilment. No cancellations/returns flow (a simple void/cancel status is enough for mistakes).

Priorities: **P0** = v1 must-have · **P1** = v1 nice-to-have · **P2** = later.

---

## Module A — Product Catalog

| ID | Requirement | Priority |
|----|-------------|----------|
| A1 | Product list: compound name, SKU, dosage variant, category, active/inactive. Seed with actual inventory: GHK-Cu, Retatrutide, BPC-157, etc., each with its own dosages | P0 |
| A2 | Each (compound × dosage) is a distinct SKU | P0 |
| A3 | Two price tiers per SKU: **seller price** and **public price** | P0 |
| A4 | **Packaging materials as inventory items** (branded boxes; later: bac water, syringes, ice packs if stocked) — tracked like stock but not sold | P0 |
| A5 | Product bundles/blends sold as one line item | P2 |

**Why:** "1 vial 60mg" must resolve to exactly one SKU, at the right price tier. Boxes are P0 because running out of boxes blocks shipping just as hard as running out of vials.

---

## Module B — Inventory (batch-tracked, multi-location)

| ID | Requirement | Priority |
|----|-------------|----------|
| B1 | Live stock per SKU **per location** (Nadeem's house, team's house — extensible) | P0 |
| B2 | Stock is held as **batches**: batch number, expiry date, quantity remaining, which purchase it came from | P0 |
| B3 | Total per SKU = sum across locations/batches, visible at a glance; expiry-soonest shown first (FEFO — first expired, first out) | P0 |
| B4 | Every change is a recorded **movement**: stock-in (receiving), transfer between locations, deduction on fulfilment, adjustment (count/damage/expiry write-off) with reason + who + when | P0 |
| B5 | Team can update stock from a phone in seconds (mobile-first flows) | P0 |
| B6 | **Expiry alerts**: batches nearing expiry flagged on dashboard | P1 |
| B7 | Stock **reserved** on confirmed (paid) orders; available = on-hand − reserved | P1 |
| B8 | Cycle count flow: enter physical count → variance logged as adjustment | P1 |

**Why:** kills the "how much stock do we have?" messages. The movement ledger (B4) is what makes the number trustworthy; batch tracking (B2) is required by the physical reality of the product (batch numbers, expiry, cold chain, lab-test-per-batch).

---

## Module C — Order Management (internal entry)

| ID | Requirement | Priority |
|----|-------------|----------|
| C1 | Order record with standard e-commerce fields: customer name, phone, delivery address, line items (SKU + qty + price), channel (seller / direct), price tier applied, notes | P0 |
| C2 | **Paste-to-parse assist**: paste the WhatsApp text (NAMA / PHONE NO / QUANTITY format and similar) → form pre-fills, team completes the rest. This is the primary intake accelerator since sellers/customers stay on WhatsApp | P0 |
| C3 | Lifecycle: `Draft (unpaid) → Confirmed (paid) → Packed → Shipped/Out for delivery → Completed` + `Void` for mistakes. **Confirmation happens on payment** | P0 |
| C4 | Payment tracking per order: amount, method (transfer/QR/COD), attach receipt/QR screenshot as proof | P0 |
| C5 | Seller attribution on seller-channel orders (even with one seller today — supports margin-per-channel and future sellers) | P0 |
| C6 | Order list + search (by customer name/phone/status/date) — replaces chat scrollback | P0 |
| C7 | Order edit history (who changed what) | P1 |
| C8 | Repeat-customer recognition by phone number (supports the QR-on-box direct-sales goal: see that a customer came back) | P1 |
| C9 | WhatsApp status notifications to seller/customer | P2 |

**Why:** structured records with payment-gated confirmation mirror how the business actually works. C2 is the adoption bridge — nobody outside the team changes behavior.

---

## Module D — Fulfilment & Delivery Scheduling

| ID | Requirement | Priority |
|----|-------------|----------|
| D1 | Each order carries **delivery method** and **target dispatch date**. Methods: Lalamove, COD/meet-up, self-pickup, courier postage (City-Link, Best Express, DHL eC, KEX, Celsius Express Chilled/Frozen, Pos Laju, Aramex, LEX, Skynet, Pos MELPlus, SPX, J&T, …— maintainable list, not hardcoded) | P0 |
| D2 | **Prep queue**: what must be packed, grouped by dispatch date + method ("Saturday: 1 Lalamove run, 3 courier parcels") | P0 |
| D3 | Marking Packed/Shipped deducts stock (FEFO suggestion by batch) from the fulfilling location and records which **batch** each vial came from | P0 |
| D4 | **Delivery cost + who pays** (customer / Indexa / seller) captured per order — feeds margin | P0 |
| D5 | Tracking number / Lalamove reference field per order | P1 |
| D6 | Calendar/agenda view of upcoming dispatches | P1 |
| D7 | Short-stock warning on the queue (order can't be fulfilled from current stock) | P1 |

**Why:** the delivery method + date drives daily work. Lalamove is booked per order with no fixed days, so the queue must be date-driven, not route-driven. Recording batch-per-order (D3) also gives traceability if a batch has issues.

---

## Module E — Restocking / Purchasing

| ID | Requirement | Priority |
|----|-------------|----------|
| E1 | Supplier list: name, products supplied, typical lead time, notes (multiple suppliers; one main) | P0 |
| E2 | Purchase record: supplier, SKUs + quantities, **unit cost**, order date, expected arrival, status (ordered / in transit / received) | P0 |
| E3 | Receiving creates **batches**: batch number, expiry date, qty, landed unit cost → stock-in movement at chosen location | P0 |
| E4 | **Testing cost per batch** (Janoshik): record test fee (+ result/link) against the batch; amortized into that batch's unit cost | P0 |
| E5 | Reorder point per SKU; dashboard flag when available stock falls below it | P0 |
| E6 | Incoming stock visible ("50 vials arriving ~next week") | P1 |
| E7 | Other landed-cost components per purchase (shipping, customs, payment fees) | P1 |
| E8 | Suggested reorder quantity from sales velocity | P2 |

**Why:** restocking becomes proactive, and E3+E4 make batch-level landed cost real — which is what makes Module F honest.

---

## Module F — Costing & Margin

| ID | Requirement | Priority |
|----|-------------|----------|
| F1 | Landed cost per batch = (purchase cost + allocated testing cost [+ E7 components]) ÷ units | P0 |
| F2 | Margin per order line = selling price (seller or public tier) − batch landed cost − Indexa-paid delivery cost share | P0 |
| F3 | Margin views: per order, per product, per channel (seller vs direct), per month | P0 |
| F4 | Price list management for both tiers, with change history | P1 |
| F5 | Simple monthly summary: revenue, COGS, delivery costs, testing costs, gross margin | P1 |

**Why:** "price/costing = margin calculation" is a stated core problem. The seller-vs-direct margin view (F3) also quantifies the strategic value of the QR-on-box direct-sales push.

---

## Module G — Users, Roles & Access

| ID | Requirement | Priority |
|----|-------------|----------|
| G1 | Roles: **Admin** (Nadeem — everything incl. costs/margins/pricing) and **Ops** (inventory, orders, fulfilment — no cost/margin visibility by default; configurable) | P0 |
| G2 | Supabase Auth, email login, small fixed user set (no self-signup) | P0 |
| G3 | Audit trail on order status changes and stock movements (who/when) | P1 |

---

## Non-functional requirements

| ID | Requirement |
|----|-------------|
| N1 | **Mobile-first.** Every P0 flow must work one-handed on a phone. |
| N2 | **Low friction beats features.** If updating stock takes >15 seconds, it won't be done and the numbers rot. |
| N3 | Data privacy: customer names + phones are PII; role-limited access; Supabase RLS on all tables. |
| N4 | Works on unstable mobile connections — no lost submissions. |
| N5 | MYR currency, DD/MM/YYYY dates, Asia/Kuala_Lumpur timezone. |

---

## Explicitly out of scope for v1

- Seller or customer logins / seller self-serve portal (seller stays on WhatsApp)
- Website checkout integration (indexalab.shop → WhatsApp handoff stays as-is, by design)
- Automated Lalamove API booking (manual booking; system tracks the schedule)
- WhatsApp API automation (revisit as C9/P2)
- Returns/cancellation workflows (a `Void` status suffices)
- Accounting integration
- Marketplace integrations

## Suggested v1 build order

1. **Catalog + batch inventory (A, B)** — immediate value: shared live stock with expiry across both houses
2. **Orders with paste-to-parse + payment gating (C)** — structured intake, statuses, proof attached
3. **Fulfilment queue + delivery scheduling (D)** — the "what ships when" view
4. **Purchasing/receiving + costing (E, F)** — batches enter with real landed cost; margins light up
5. **Role hardening + audit (G)**
