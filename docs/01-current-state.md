# 01 — Current State (As-Is)

How the Indexa Lab operation runs today. This is the baseline every requirement must improve on. Items marked **(assumption)** need confirmation.

## 1. The business

Indexa Lab ([indexalab.shop](https://indexalab.shop/)) sells research-grade peptide products. Products are dosage-specific vials (e.g. "1 vial 60mg"), plus related supplies **(assumption: supplies like bacteriostatic water/syringes are also stocked and sold — confirm)**.

Sales flow through **sellers** (resellers/agents) who collect end-customer orders and submit them to Indexa for fulfilment. **(assumption: sellers are external agents on some commission/wholesale arrangement — confirm the commercial model, since it affects costing/margin.)**

## 2. People involved

| Role | Who | What they do today |
|------|-----|--------------------|
| Owner / admin | Nadeem | Restocking, pricing, coordination, holds part of the inventory |
| Fulfiller | 1 employee | Packs and ships orders, holds part of the inventory |
| Sellers | multiple | Submit customer orders as text messages |

## 3. Order intake today

Sellers submit orders as free-form text (via WhatsApp **(assumption)**), typically:

```
NAMA : ROHAYA BINTI ISMAIL
PHONE NO : 012-919 2977
QUANTITY : 1 vial 60mg
```

Problems with this format:

- **No product identity** — "1 vial 60mg" implies a product but doesn't name it; ambiguous once more than one product shares a dosage.
- **No delivery info** — address, delivery method, and target date arrive separately or are agreed verbally.
- **No seller attribution captured** — who submitted it lives in the chat thread, not in a record.
- **No price** — the amount charged / collected isn't part of the submission.
- **No status** — once submitted, there is no shared record of whether it's pending, packed, shipped, or delivered. Progress-checking = messaging the fulfiller.

## 4. Inventory today

- Stock is physically **split between two holders**: Nadeem keeps some, the fulfiller keeps some.
- There is **no shared ledger**. Current quantities exist only in each person's head / private notes.
- Answering "can we fulfil this order?" or "how much of X is left?" requires asking the fulfiller, repeatedly.
- Stock movements (received from supplier, transferred between holders, consumed by an order, damaged/lost) are not recorded anywhere.

## 5. Restocking today

- No reorder points or alerts. Restocking is triggered by noticing (or being told) that something is running low — sometimes after an order already can't be fulfilled **(assumption: stockouts have happened — confirm)**.
- Supplier lead times, purchase costs per batch, and incoming-stock status are not tracked in a system.

## 6. Costing & margin today

- Cost per unit (landed cost of a vial) and selling price exist, but margin per product / per order / per seller is **not calculated systematically**.
- Costs may vary by purchase batch **(assumption)**; there is no record connecting a sold unit to what that unit cost.

## 7. Delivery & scheduling today

- Delivery methods include **Lalamove** (same-day/scheduled courier) and likely standard couriers/postage **(assumption: e.g. J&T / Pos Laju for outstation — confirm)**.
- Orders have **target dispatch dates** ("must go out by Lalamove this Saturday", "next week is fine"). These dates drive what the fulfiller must prepare and when — but the schedule lives in chat messages and memory.
- There is no view of "what needs to be packed for Saturday" or "what's going out this week".

## 8. Pain points summarized

1. Owner is the human API between sellers and fulfiller — constant status-checking messages.
2. No single source of truth for stock; split inventory makes it worse.
3. Order records are chat scrollback — unstructured, unsearchable, no lifecycle.
4. Margin is invisible; pricing and restocking decisions are made without numbers.
5. Prep/dispatch scheduling is mental load, with real deadlines (Lalamove slots).
