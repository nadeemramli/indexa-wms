# 01 — Current State (As-Is)

How the Indexa Lab operation runs today, per discovery with Nadeem (2026-07-30). This is the baseline every requirement must improve on.

## 1. The business

Indexa Lab ([indexalab.shop](https://indexalab.shop/)) sells research-grade peptide products — e.g. **GHK-Cu, Retatrutide, BPC-157**, and others — as dosage-specific vials (each compound has its own set of dosages, e.g. "1 vial 60mg").

Volume today: **~20 vials/month, fluctuating.** Growth levers identified by Nadeem: better service + logistics (the seller can sell more), and more product availability.

### Sales channels

1. **Seller channel** — currently **one seller**. Commercial model: Indexa gives the seller a **seller price**; there is also a **public price**. The seller collects end-customer orders and submits them to Nadeem via WhatsApp. The seller will *keep* working via WhatsApp — they will not use the app.
2. **Direct channel** — indexalab.shop takes orders, but checkout intentionally hands off to **WhatsApp** (this is the desired flow, not a limitation). Packaging boxes carry the website domain + a QR code, so end-customers who received a vial via the seller can buy directly from Indexa next time. Growing the direct channel is a strategic goal.

## 2. People involved

| Role | Who | What they do today |
|------|-----|--------------------|
| Owner / admin | Nadeem | Restocking, pricing, coordination, holds stock at his house |
| Operational team | 1 fulfiller (team may grow) | Packs and ships orders, holds stock at their house |
| Seller | 1 (external) | Sells to end-customers, submits orders via WhatsApp — **not an app user** |

The app is **internal only**: Nadeem + operational team log in. No seller or customer accounts.

## 3. Order intake today

Orders arrive as free-form WhatsApp text, typically:

```
NAMA : ROHAYA BINTI ISMAIL
PHONE NO : 012-919 2977
QUANTITY : 1 vial 60mg
```

The full info needed is standard e-commerce data: customer name, phone, **delivery address**, items + quantities — but it arrives unstructured and incomplete, then gets completed over chat.

**Payment:** customer/seller pays by bank transfer or QR to Nadeem's bank account; proof is a receipt/screenshot. **An order is confirmed only on payment.** No partial fulfilment. No cancellations/returns in practice.

Problems:

- No product identity ("1 vial 60mg" doesn't name the compound).
- No shared status — once submitted, checking progress = messaging the fulfiller.
- Payment proofs live in chat threads, disconnected from any order record.
- Seller attribution and price tier (seller vs public) aren't recorded anywhere.

## 4. Inventory today

- Stock is **cold chain** and physically split across **two storage locations**: Nadeem's house and the team member's house.
- Products are purchased **in batches**; every batch has a **batch number and expiry date**. None of this is tracked in a system.
- **Packaging boxes are inventory too** (branded boxes with domain + QR) and need tracking alongside vials.
- No shared ledger — quantities live in each person's head/notes. "Can we fulfil this?" = ask the fulfiller.
- Stock movements (received, transferred between houses, consumed by an order, expired/damaged) are not recorded.

## 5. Restocking today

- **Multiple suppliers**, though most purchases go through one main supplier. Supplier list + lead times to be documented (Nadeem will provide).
- No reorder points or alerts; restocking is reactive.
- Purchases arrive as batches (with batch number + expiry) — receiving is not systematized.

## 6. Costing & margin today

- Unit cost varies by purchase batch.
- **Lab testing is a real cost component**: batches are tested via [Janoshik](https://janoshik.com/) (third-party analytical testing). Testing costs must be part of landed cost / margin math.
- Two price tiers exist (seller price, public price) but margin per product/order/channel is not calculated systematically.

## 7. Delivery & scheduling today

Delivery methods in use / available (payer varies by order — sometimes customer, sometimes Indexa, sometimes the seller):

- **Lalamove** — booked per order (one order per run, no multi-order batching), on whatever date is agreed. No fixed dispatch days.
- **COD** (cash on delivery / meet-up)
- **Self-pickup**
- **Courier postage** — the Malaysian courier menu, e.g.: City-Link Express, Best Express, DHL eCommerce, KEX Express, **Celsius Express (Chilled / Frozen — relevant for cold chain)**, Pos Laju, Aramex, Lazada Express (LEX), Skynet, Pos MELPlus, SPX Xpress, J&T Express.

Orders carry target dispatch dates ("Lalamove this Saturday", "next week"). These drive what the fulfiller must prepare and when — but the schedule lives in chat and memory. There is no "what goes out this week" view.

## 8. Pain points summarized

1. Nadeem is the human API between seller, customers, and fulfiller — constant status-checking messages.
2. No single source of truth for stock; two locations + batch/expiry make it worse.
3. Order records are chat scrollback — unstructured, no lifecycle, payment proofs detached.
4. Margin is invisible; batch costs + testing costs + two price tiers are not connected to sales.
5. Prep/dispatch scheduling is mental load, with real deadlines (Lalamove bookings, courier cutoffs).
6. Packaging stock (boxes) can run out unnoticed, blocking fulfilment.
