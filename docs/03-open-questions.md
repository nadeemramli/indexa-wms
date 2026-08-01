# 03 — Open Questions

Updated 2026-07-30 after discovery session with Nadeem. Most 🔴 blockers are resolved (answers folded into docs 01/02). What remains is mostly **seed data to collect** and a few smaller decisions — none block starting the schema.

## Seed data to collect (needed before/at go-live, not before schema)

- [ ] **Full SKU list**: every compound + dosage currently stocked (GHK-Cu, Retatrutide, BPC-157, …), with current seller price + public price per SKU.
- [ ] **Supplier list**: names, which products each supplies, typical lead time, who the main supplier is.
- [ ] **Current stock snapshot**: per SKU, per location (Nadeem's house / team's house), with batch numbers + expiry dates — the opening balances.
- [ ] **Packaging inventory**: box types/sizes stocked and current counts. Any other consumables to track (ice packs, bac water, syringes)?
- [ ] **Typical costs**: recent per-batch purchase costs and a typical Janoshik test fee, so margin outputs can be sanity-checked.

## Cash module — data to enter at setup (admin only)

- [ ] **Opening bank balance** and the date it's "as of".
- [ ] **Recurring fixed costs**: salary amount + pay day; any subscriptions (domain, tools) with amounts.
- [ ] Recent packaging/label production costs (for the packaging expense category baseline).
- [ ] Typical payment/bank/forex fees when paying suppliers (flat or %?).

## Smaller decisions (can be settled during the build)

- **Testing:** is every batch tested at Janoshik, or only some? Flat fee per test or varies by compound? (Affects how testing cost is allocated in F1.)
- **Landed cost components:** besides product + testing — supplier shipping? customs? payment/forex fees? Include which in v1?
- **Ops visibility:** should the ops team see costs/margins, or admin-only (current default: admin-only)?
- **COD:** who does COD handoffs today — fulfiller, Nadeem, or a runner? Does COD cash need tracking in the app?
- **Cold chain in transit:** when do you use Celsius Express Chilled/Frozen vs normal couriers? Any packing rules per method worth encoding in the prep queue (e.g. "frozen → needs ice pack + Celsius")?
- **Expiry policy:** how far before expiry should a batch be flagged (30/60/90 days)? Can near-expiry vials still be sold?
- **Data entry:** who besides Nadeem will enter orders from WhatsApp — will the fulfiller also do intake?
- **Existing records:** any spreadsheets/notes with past orders or purchases worth importing, or start clean?
- **Team devices:** Android/iPhone for the ops team (PWA install instructions differ)?

## Resolved (for the record)

**2026-08-01:** cash & cashflow module added (single bank account, balance derived from ledger, admin-only) · BOM per product with auto-consumption of box/label at ship (manual override) · ops role sees no cash/expenses/margins.


Single seller, WhatsApp-only, no seller login · internal app for Nadeem + ops team · seller price vs public price tiers · ~20 vials/month, fluctuating · website checkout hands to WhatsApp by design · QR-on-box drives direct sales goal · batches with batch numbers + expiry, cold chain, two storage locations · multiple suppliers (one main) · Janoshik testing costs count · order = standard e-commerce fields · payment to Nadeem's bank via QR/transfer with receipt proof · **confirmed on payment** · no partial fulfilment · no cancellations/returns · delivery = Lalamove (one order per run, no fixed days), COD, self-pickup, courier postage (City-Link, Best, DHL eC, KEX, Celsius chilled/frozen, Pos Laju, Aramex, LEX, Skynet, Pos MELPlus, SPX, J&T) · delivery payer varies (customer/Indexa/seller) · stack = Supabase + Vercel (default domain).
