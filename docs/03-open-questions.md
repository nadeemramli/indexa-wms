# 03 — Open Questions (Discovery)

Questions to answer **before** building. Grouped by module; the P0-blocking ones are marked 🔴. Answering these turns assumptions in the other docs into facts.

## Business & sellers

- 🔴 How many sellers are there, and how do they currently reach you (WhatsApp individual chats? group? other)?
- 🔴 What's the commercial arrangement with sellers — fixed wholesale price, commission on sale, or seller sets own end price? (Determines what "margin" means and what sellers may see.)
- Roughly how many orders per day/week now, and what growth do you expect? (Affects how much automation is worth it.)
- Do end-customers ever order directly (not via a seller)? Via the website?
- Does the indexalab.shop website take real orders today, or is it primarily a catalog/brand page?

## Catalog & products

- 🔴 Full product list with variants: which compounds, which dosages, which supplies? (Needed to seed the catalog — even a photo of a price list works.)
- Do products have batch/lot numbers or expiry dates that need tracking? (Common for vials; changes inventory model significantly if yes.)
- Any storage constraints (cold chain?) that affect where stock can be held?

## Inventory

- 🔴 Who are all the stock holders? Just you + one fulfiller, or others (second employee, seller consignment stock)?
- Do sellers ever hold stock themselves (consignment) and fulfil directly?
- When a new order comes in, who decides whether it ships from your stock vs the fulfiller's stock? Any rule (e.g. always fulfiller unless out)?
- How often do you want physical stock counts (weekly/monthly)?

## Orders

- 🔴 What's the minimum info a seller must provide at submission? Is delivery address always known at submission time, or later?
- What payment methods do customers/sellers use (bank transfer, COD, cash to seller)? Who collects — seller or Indexa?
- 🔴 When is an order "confirmed" — on submission, on payment, on your approval?
- Are partial fulfilments a thing (send 2 of 3 vials now)?
- Cancellations/returns: do they happen, and what happens to stock/money?

## Delivery & scheduling

- 🔴 Full list of delivery methods used (Lalamove, which couriers, pickup?) and roughly what share of orders each covers.
- Who books Lalamove today, and is it one batch run (multiple orders per run) or per-order?
- Are there fixed dispatch days (e.g. Lalamove runs only Sat) or fully ad-hoc?
- Who pays delivery — customer, seller, or absorbed by Indexa? (Feeds margin calc F4.)

## Restocking & costing

- 🔴 How do you buy stock today — how many suppliers, typical lead time, typical batch size?
- Does unit cost change meaningfully between batches? Which costs should count in "landed cost" (product, shipping, customs, payment fees)?
- Current pricing: one standard price per SKU, or does it vary by seller/volume?

## People & adoption

- 🔴 Will sellers actually use a form/app, or will some keep sending WhatsApp texts no matter what? (If the latter, the paste-to-parse assist C5 gets promoted to P0 and an admin data-entry flow is essential.)
- Fulfiller's comfort with apps: Android/iPhone? Any language preference for the UI?
- Who besides you should see margins? (Default: only you.)

## Tech

- Confirm direction: Supabase + web app on Vercel? Any preference for how sellers access it (WhatsApp-shared link vs installed PWA)?
- Any existing data to import (spreadsheets, notes with current stock/prices)?
- Budget/appetite: hand-built app vs internal-tool builder (Retool) for the admin side first?

---

### How to use this doc

Work through the 🔴 items first — each unblocks a P0 module. Suggested method: one voice-note or chat session per section, then we update `01-current-state.md` and `02-requirements.md` and delete answered questions from here.
