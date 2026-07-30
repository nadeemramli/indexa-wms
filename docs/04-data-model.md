# 04 — Data Model (Draft)

Draft entity model for the Supabase (Postgres) schema, derived from [`02-requirements.md`](02-requirements.md). This is a design doc, not final DDL — table/column names will be refined when we write migrations.

## Design principles

1. **Stock is derived, never edited.** On-hand quantity = sum of `stock_movements`. No editable "quantity" field anywhere — that's what makes counts trustworthy (B4).
2. **Batches are first-class.** Physical reality: vials arrive in batches with batch numbers, expiry, a test certificate, and a landed cost. Every unit in and out is tied to a batch.
3. **Money facts are captured at the moment they happen.** Order lines snapshot the price charged; fulfilments snapshot the batch (and thus its landed cost). Margin is then pure arithmetic, never reconstruction.

## Entity overview

```
suppliers ──< purchases ──< purchase_lines
                                │ (receiving)
                                ▼
products ──< product_batches ──< stock_movements >── locations
   │                                    ▲
   │                                    │ (fulfilment deducts)
   └──< order_lines >── orders ──< payments
              │            ├── delivery info (method, date, cost, payer)
              └── batch allocation (which batch filled which line)
```

## Tables

### `products`
Sellable SKUs and internal consumables.

| Column | Notes |
|--------|-------|
| id | uuid PK |
| name | e.g. "Retatrutide" |
| dosage | e.g. "60mg" — (name, dosage) unique; both together = the SKU |
| sku | short human code, e.g. `RETA-60` |
| kind | `vial` \| `packaging` \| `supply` — packaging/supplies are tracked but not sold |
| seller_price / public_price | MYR; current prices (history in `price_changes`, P1) |
| reorder_point | qty that triggers restock flag (E5) |
| requires_cold_chain | bool |
| active | bool |

### `locations`
| Column | Notes |
|--------|-------|
| id, name | "Nadeem's house", "Team house" — extensible |

### `suppliers`
| Column | Notes |
|--------|-------|
| id, name, lead_time_days, notes | products-supplied via purchases history; `is_main` flag optional |

### `purchases` / `purchase_lines`
A restock order to a supplier (E2).

| purchases | purchase_lines |
|-----------|----------------|
| id, supplier_id, ordered_at, expected_at, status (`ordered`/`in_transit`/`received`/`partial`), shipping_cost, other_costs, notes | id, purchase_id, product_id, qty, unit_cost |

### `product_batches`
Created at receiving (E3). The costing anchor.

| Column | Notes |
|--------|-------|
| id | uuid PK |
| product_id, purchase_line_id | provenance |
| batch_number | supplier's batch/lot number |
| expiry_date | drives FEFO + expiry alerts |
| qty_received | units in |
| testing_cost | Janoshik fee allocated to this batch (E4); nullable |
| test_result_url | link/attachment to certificate |
| landed_unit_cost | computed: (line cost share + testing + allocated purchase costs) ÷ qty_received |

### `stock_movements`
Append-only ledger (B4). One row per change, signed quantity.

| Column | Notes |
|--------|-------|
| id, product_id, batch_id, location_id | what & where |
| qty | +in / −out |
| type | `receive` \| `transfer_in` \| `transfer_out` \| `fulfil` \| `adjust` \| `expire` |
| order_line_id | set when type=`fulfil` — traceability of which batch went to which customer |
| reason, created_by, created_at | audit (G3) |

*On-hand per (product, location) = Σ qty. Available = on-hand − reserved (reservations P1: either a `reservations` table or derived from confirmed-not-shipped order lines).*

### `orders`
| Column | Notes |
|--------|-------|
| id, order_no | short human ref, e.g. `IDX-0042` |
| customer_name, customer_phone, address | PII — RLS-protected; phone is the repeat-customer key (C8) |
| channel | `seller` \| `direct` |
| seller_id | nullable FK (one seller today; future-proof) |
| status | `draft` → `confirmed` → `packed` → `shipped` → `completed`, or `void` |
| delivery_method | FK to small `delivery_methods` table (Lalamove, COD, pickup, City-Link, Celsius Chilled, … maintainable list, D1) |
| dispatch_date | target date — drives the prep queue (D2) |
| delivery_cost, delivery_payer | payer: `customer` \| `indexa` \| `seller` (D4) |
| tracking_ref | courier tracking no. / Lalamove ref (D5) |
| notes, source_text | raw pasted WhatsApp text kept for reference (C2) |
| created_by, timestamps per status change | audit |

### `order_lines`
| Column | Notes |
|--------|-------|
| id, order_id, product_id, qty | |
| unit_price | snapshot of price charged (tier applied at entry) |
| price_tier | `seller` \| `public` |

### `payments`
| Column | Notes |
|--------|-------|
| id, order_id, amount, method (`transfer`/`qr`/`cod`), paid_at | order flips to `confirmed` when payments ≥ order total (C3/C4) |
| proof_url | receipt/QR screenshot in Supabase Storage |

### `sellers`
| id, name, phone, notes | one row today; enables C5/F3 and future growth |

### `app_users` (Supabase Auth + profile)
| id (auth uid), name, role (`admin`/`ops`) | RLS: ops can't select cost/margin columns (G1) |

## Margin (F1/F2) — pure queries, no stored totals

```
line_margin = order_lines.unit_price × qty
            − Σ(fulfil movements' batch.landed_unit_cost × qty)
            − (order.delivery_cost when delivery_payer = 'indexa', apportioned)
```

Views: margin per order / product / channel / month (F3) as Postgres views — no denormalized margin columns to drift out of sync.

## Open modeling questions

- Reservation mechanics (B7): derive from confirmed orders vs explicit table — decide when building.
- Purchase-level costs (shipping/customs, E7): allocate by value or by units? Default: by units, v1.
- Price history (F4): simple `price_changes` audit table, added when needed.
- Packaging consumption: auto-deduct one box per shipped order, or manual? Default: auto with manual override.

## Next step

Turn this into Supabase migrations (tables + RLS policies + views), seed with the SKU/supplier/opening-stock data from [`03-open-questions.md`](03-open-questions.md), and stand up the first screen: the stock view.
