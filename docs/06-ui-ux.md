# 06 — UI/UX Specification

How INDEXA WMS looks and navigates. Mobile-first (phones are the primary device for both roles); desktop gets the same screens with more breathing room. A clickable mockup of the key screens lives at [`mockups/app-mockup.html`](mockups/app-mockup.html) — open it in a browser.

## Design principles

1. **Speed over completeness.** The two hero flows — paste-an-order and check-stock — must feel instant. Anything that adds a tap to those flows needs a very good reason.
2. **Glanceable truth.** Numbers (stock, cash) are always live and always derived — the UI never shows an editable quantity field, only actions that create movements.
3. **Role-shaped UI.** Ops and Admin share components, but Ops simply *does not have* the Cash tab, prices, costs, or margins anywhere in their build of the UI (enforced by RLS underneath — hiding is UX, RLS is security).
4. **Thumb-reach first.** Primary actions live at the bottom of the screen; the New Order button is a floating action button (FAB) reachable one-handed.
5. **Bahasa-friendly labels.** Short English/Malay-neutral words where possible ("Stok", "Order", "Queue", "Cash"); no jargon.

## Information architecture & navigation

Bottom tab bar (mobile) / left sidebar (desktop):

```
┌─────────────────────────────────────────────────────┐
│  ADMIN (Nadeem)                                     │
│  🏠 Home   📦 Stok   ➕(FAB)   🚚 Queue   💰 Cash   │
│                                                     │
│  Overflow (Home → cards / menu):                    │
│  Orders list · Purchases · Margins · Products ·     │
│  Suppliers · Settings/Users                         │
├─────────────────────────────────────────────────────┤
│  OPS (fulfiller)                                    │
│  🏠 Home   📦 Stok   ➕(FAB)   🚚 Queue             │
│  (no Cash, no Margins, no costs/prices anywhere)    │
└─────────────────────────────────────────────────────┘
```

The ➕ FAB opens the context-right action: **New Order** by default; inside Stock it offers *Receive / Transfer / Adjust*.

## Screens

### S1 · Home (Dashboard)

*Admin sees money + stock health; Ops sees work + stock health.*

```
┌─────────────────────────────┐
│ INDEXA          Salam, N. 👋│
│                             │
│ ┌───────────┐ ┌───────────┐ │  Admin-only row:
│ │ CASH      │ │ MARGIN MTD│ │  cash position &
│ │ RM 12,450 │ │ RM 3,180  │ │  margin this month
│ └───────────┘ └───────────┘ │
│ ┌─────────────────────────┐ │
│ │ 🚚 Today: 2 Lalamove,   │ │  tap → Queue
│ │    3 courier parcels    │ │
│ └─────────────────────────┘ │
│ ┌─────────────────────────┐ │
│ │ ⚠ Reta 60mg below       │ │  tap → Purchases
│ │   reorder point (4 left)│ │
│ │ ⚠ BPC #B2405 expires    │ │  tap → batch view
│ │   in 21 days            │ │
│ └─────────────────────────┘ │
│ Recent orders …             │
├─────────────────────────────┤
│ 🏠   📦   ➕   🚚   💰      │
└─────────────────────────────┘
```

### S2 · Stock (Stok) — the most-used screen

```
┌─────────────────────────────┐
│ Stok            🔍 search   │
│ [All] [Vials] [Packaging]   │
│ ┌─────────────────────────┐ │
│ │ Retatrutide 60mg   ⚠ 4  │ │  total available
│ │ N-house 1 · A-house 3   │ │  split per location
│ │ next exp 2026-11 #B2406 │ │
│ ├─────────────────────────┤ │
│ │ GHK-Cu 50mg        23   │ │
│ │ N-house 20 · A-house 3  │ │
│ ├─────────────────────────┤ │
│ │ Box (branded)      41   │ │  packaging tracked
│ │ Labels             120  │ │  same list
│ └─────────────────────────┘ │
│                      ➕     │  Receive/Transfer/Adjust
├─────────────────────────────┤
│ 🏠   📦   ➕   🚚   💰      │
└─────────────────────────────┘
```

**SKU detail** (tap a row): batches with expiry + qty (FEFO order), per-location split, movement history feed ("−1 fulfil #IDX-0042 · Aiman · 2 Aug"), buttons: Transfer · Adjust. Admin additionally sees landed cost per batch.

**Adjust flow** (3 taps): qty ± → reason chip (count / damaged / expired / other) → save. Every adjustment is a ledger row, never an overwrite.

### S3 · New Order — the paste-to-parse flow

```
┌─────────────────────────────┐
│ ← New Order                 │
│ ┌─────────────────────────┐ │
│ │ 📋 Paste from WhatsApp  │ │  big paste box on top
│ │ "NAMA : ROHAYA…"        │ │
│ └─────────────────────────┘ │
│  ── parsed ──               │
│ Nama    [Rohaya Binti Ism.] │  editable, pre-filled
│ Phone   [012-919 2977]      │
│ Item    [Reta 60mg ▾] x[1]  │  product picker
│ Channel (● Seller ○ Direct) │  sets price tier
│ Price   RM 350 (seller)     │  auto, editable
│ Address [………………]            │
│ Delivery[Lalamove ▾]        │
│ Dispatch[Sat 8 Aug ▾]       │
│ Del. fee RM[25] paid by[▾]  │
│ ┌─────────────────────────┐ │
│ │      Save Draft         │ │
│ └─────────────────────────┘ │
└─────────────────────────────┘
```

Parser rules: tolerant of `NAMA/NAME`, `PHONE NO/NO TEL`, `QUANTITY/QTY`; unmatched lines → notes; raw text always stored (`source_text`). Parse failure is silent — fields just stay empty, no error walls.

**Order detail:** status timeline (Draft → Confirmed → Packed → Shipped → Completed), items, payment section (**Add payment** → amount + method + receipt photo → auto-confirm when total covered), delivery block, WhatsApp deep-link on the phone number (`wa.me/60129192977`), Void in overflow menu.

### S4 · Queue (fulfilment)

```
┌─────────────────────────────┐
│ Queue        [Today ▾]      │
│ ── SAT 8 AUG ── ─────────── │
│ 🛵 LALAMOVE (1)             │
│ ┌─────────────────────────┐ │
│ │ #0042 Rohaya · Shah Alam│ │
│ │ 1× Reta 60mg ← #B2406   │ │  FEFO batch pick
│ │ box ✓ label ✓ icepack ✓ │ │  BOM checklist
│ │ [ Packed ]              │ │
│ └─────────────────────────┘ │
│ 📮 COURIER (2)              │
│ │ #0040 … [ Packed ]      │ │
│ │ #0041 ⚠ short stock     │ │  flagged red
│ ── MON 10 AUG ── ────────── │
│ │ #0043 … (preview)       │ │
├─────────────────────────────┤
│ 🏠   📦   ➕   🚚   💰      │
└─────────────────────────────┘
```

Tapping **Packed** → card flips to **Shipped?** with tracking-ref field. On Shipped: stock + BOM components deduct from the fulfiller's location, timeline updates, done. Undo within the session via the movement ledger (reversing entry).

### S5 · Cash (Admin only)

```
┌─────────────────────────────┐
│ Cash                        │
│ ┌─────────────────────────┐ │
│ │ Position   RM 12,450    │ │  derived, not editable
│ └─────────────────────────┘ │
│ [Aug ▾]  in RM4,900 │ out RM3,730
│ ── auto ──────────────────  │
│ + RM350  sale #0042  2 Aug  │
│ − RM980  stock PO-12 1 Aug  │
│ − RM120  testing #B2406     │
│ − RM25   delivery #0039     │
│ ── manual ────────────────  │
│ − RM1,500 salary (Aug) ⟳    │  recurring, 1-tap confirm
│ − RM230  label printing 📎  │  receipt attached
│ [+ Add expense]             │
├─────────────────────────────┤
│ 🏠   📦   ➕   🚚   💰      │
└─────────────────────────────┘
```

**Margins** (from Home → Margins, admin): per product / per channel / per month; each order line traceable to its batch landed cost.

### S6 · Purchases & receiving

List of POs with status chips (ordered / in transit / received / paid). **Receive** flow per line: batch no → expiry → qty → location → test fee (skippable, fillable later) → confirm. Creates batches + stock-in movements; landed unit cost computed and shown (admin).

## Component & state conventions

- **Status chips:** Draft=grey · Confirmed=blue · Packed=amber · Shipped=violet · Completed=green · Void=strikethrough grey. Same colors everywhere.
- **Badges:** ⚠ amber = low stock / near expiry; red = short-stock blocking an order.
- **Batch numbers** always monospace (`#B2406-11`) — scannable at a glance.
- **Money:** MYR, thousands separator, red for outflows, green for inflows; hidden entirely in Ops role.
- **Empty states** teach the flow ("No orders yet — tap ➕ and paste from WhatsApp").
- **Optimistic UI + offline queue:** actions apply instantly, sync when connection returns (N4); a small ⟳ shows pending sync. Failed sync never silently drops — it re-queues with a banner.
- **Confirmations:** only destructive/irreversible actions (Void, write-off) get a confirm dialog. Packed/Shipped don't — they're undoable via reversing movements.

## Visual direction

Clean, clinical, calm — matches the lab-brand positioning. Light UI, near-white background, slate text, one accent color (deep teal `#0F766E`) used only for primary actions and the active tab. Amber/red reserved for warnings. Generous tap targets (≥44px), system font stack, no decorative imagery inside the tool. The brand moment is the login screen and the word INDEXA in the header — the rest of the app stays neutral and fast.

## Build notes

- Framework: Next.js (App Router) on Vercel + Supabase JS client; PWA manifest so both users can "install" it to their home screen.
- All list screens are server-driven queries with realtime subscriptions on `stock_movements` and `orders` (Supabase Realtime) — that's what makes the "no more asking" promise real.
- The mockup file (`mockups/app-mockup.html`) is throwaway reference, not production code.
