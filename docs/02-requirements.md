# 02 — Requirements / Feature Specs (v1)

Functional requirements for INDEXA WMS v1, organized by module. Each module maps to a pain point in [`01-current-state.md`](01-current-state.md).

Priorities: **P0** = v1 must-have · **P1** = v1 nice-to-have · **P2** = later.

---

## Module A — Product Catalog

The foundation everything else references.

| ID | Requirement | Priority |
|----|-------------|----------|
| A1 | Maintain a product list: name, SKU, dosage/size variant (e.g. Tirzepatide 60mg vial), category, active/inactive | P0 |
| A2 | Support variants of the same compound by dosage (5mg / 10mg / 60mg are distinct SKUs) | P0 |
| A3 | Track default cost price and default selling price per SKU | P0 |
| A4 | Support non-vial products (supplies: bac water, syringes) | P1 |
| A5 | Product bundles/blends sold as one line item | P2 |

**Why:** "1 vial 60mg" must resolve to exactly one SKU. Without a catalog, orders and stock counts stay ambiguous.

---

## Module B — Inventory (centralized, multi-holder)

| ID | Requirement | Priority |
|----|-------------|----------|
| B1 | Live stock quantity per SKU, **per holder/location** (Nadeem's stock, fulfiller's stock, + future locations) | P0 |
| B2 | Total stock per SKU = sum across holders, visible at a glance | P0 |
| B3 | Every stock change is a recorded **movement**: stock-in (restock), transfer between holders, deduction on fulfilment, adjustment (count correction, damage, loss) with reason | P0 |
| B4 | Fulfiller can update/adjust their own stock from their phone in seconds | P0 |
| B5 | Stock is **reserved** when an order is accepted and **deducted** when it ships, so "available = on-hand − reserved" | P1 |
| B6 | Periodic stock count (cycle count) flow: enter physical count → system logs variance as adjustment | P1 |
| B7 | Stock movement history per SKU (who, when, why) | P1 |

**Why:** kills the "how much stock do we have?" messages. B3 (movement ledger, not just editable numbers) is what makes the count trustworthy.

---

## Module C — Order Management

| ID | Requirement | Priority |
|----|-------------|----------|
| C1 | Structured order submission: customer name, phone, product/SKU + qty, delivery address, delivery method, target date, seller | P0 |
| C2 | Sellers can submit orders themselves via a simple form/app link (no login friction; per-seller link acceptable) | P0 |
| C3 | Order lifecycle statuses: `Submitted → Confirmed → Packed → Shipped/Out for delivery → Delivered` (+ `Cancelled`, `On hold`) | P0 |
| C4 | Anyone with access sees order status without asking anyone — sellers see their own orders' status | P0 |
| C5 | Quick-parse assist: paste today's free-text format (NAMA/PHONE/QUANTITY) and the system pre-fills the form — smooth migration for sellers | P1 |
| C6 | Payment status per order (unpaid / deposit / paid) and amount | P1 |
| C7 | Order edit/cancel with history | P1 |
| C8 | Attach proof: tracking number, delivery photo, payment receipt | P1 |
| C9 | Notifications (WhatsApp/Telegram) on status change to seller | P2 |

**Why:** replaces chat-scrollback orders with records that carry their own status. C5 matters because seller behavior change is the biggest adoption risk.

---

## Module D — Fulfilment & Delivery Scheduling

| ID | Requirement | Priority |
|----|-------------|----------|
| D1 | Each order carries **delivery method** (Lalamove, courier/postage, pickup, …) and **target dispatch date** | P0 |
| D2 | **Prep queue for the fulfiller**: "what must be packed, grouped by dispatch date and method" — e.g. everything for Saturday's Lalamove run in one list | P0 |
| D3 | Fulfiller marks orders Packed / Shipped from phone; stock deducts automatically from their holder's inventory | P0 |
| D4 | Calendar/agenda view of upcoming dispatch dates | P1 |
| D5 | Flag orders that can't be fulfilled from current stock (short-stock warning on the queue) | P1 |
| D6 | Batch view: group same-day Lalamove orders for a single pickup | P2 |

**Why:** the delivery method + date is what actually drives daily work today; the system should present work the way it's executed.

---

## Module E — Restocking / Purchasing

| ID | Requirement | Priority |
|----|-------------|----------|
| E1 | Reorder point per SKU; dashboard + alert when available stock falls below it | P0 |
| E2 | Record purchases/restocks: supplier, quantity, **unit cost for that batch**, order date, expected arrival, received date | P0 |
| E3 | Receiving a purchase increases stock (stock-in movement) at the chosen holder | P0 |
| E4 | Incoming stock visible ("50 vials arriving ~next week") so orders can be accepted against it | P1 |
| E5 | Supplier list with lead times | P1 |
| E6 | Suggested reorder quantity based on sales velocity | P2 |

**Why:** restocking becomes proactive; E2's per-batch cost is also the input that makes margin calculation honest.

---

## Module F — Costing & Margin

| ID | Requirement | Priority |
|----|-------------|----------|
| F1 | Cost per unit tracked per purchase batch; margin per order line = selling price − unit cost (− attributable delivery cost) | P0 |
| F2 | Margin visible per order, per product, per seller, per period | P0 |
| F3 | Price list management: standard price per SKU, optionally per-seller pricing tiers | P1 |
| F4 | Delivery cost captured per order (Lalamove fee etc.) and included in margin | P1 |
| F5 | Simple P&L-ish summary: revenue, COGS, delivery costs, gross margin per week/month | P2 |

**Why:** "price/costing = margin calculation" is a stated core problem. F1 depends on E2 — costing lives or dies on recording batch costs at purchase time.

---

## Module G — Users, Roles & Access

| ID | Requirement | Priority |
|----|-------------|----------|
| G1 | Roles: **Admin** (Nadeem — everything), **Fulfiller** (inventory + fulfilment queue, no costs/margins), **Seller** (submit + track own orders only) | P0 |
| G2 | Sellers must never see cost prices, margins, other sellers' orders, or total stock levels **(confirm: should sellers see availability at all — e.g. "in stock / out of stock"?)** | P0 |
| G3 | Audit trail: every order status change and stock movement records who did it and when | P1 |

---

## Non-functional requirements

| ID | Requirement |
|----|-------------|
| N1 | **Mobile-first.** Fulfiller and sellers operate from phones. Every P0 flow must work one-handed on a phone. |
| N2 | **Low friction beats features.** If updating stock takes >15 seconds, the fulfiller will stop doing it and the system dies. |
| N3 | Bahasa Malaysia-friendly UI labels where sellers touch the system **(confirm language preference)**. |
| N4 | Data privacy: customer names + phone numbers are PII — access limited by role; consider what sellers/fulfiller can export. |
| N5 | Works on unstable mobile connections (graceful failure, no lost submissions). |

---

## Explicitly out of scope for v1

- Public e-commerce storefront / integration with indexalab.shop checkout (revisit later)
- Marketplace integrations (Shopee/Lazada/TikTok)
- Automated Lalamove API booking (v1 = manual booking, system tracks the schedule)
- Accounting integration
- Multi-warehouse beyond the two-holder model

## Suggested v1 build order

1. **Catalog + Inventory (A, B)** — immediate value: shared live stock count, fulfiller updates from phone
2. **Orders (C) + Fulfilment queue (D)** — structured intake, status visibility, prep-by-date
3. **Restocking (E) + Costing/Margin (F)** — needs A/B/C data flowing to be meaningful
4. **Roles hardening + seller self-serve (G, C2 at scale)**
