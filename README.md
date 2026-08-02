# INDEXA WMS

Order & stock management system for **Indexa Lab** ([indexalab.shop](https://indexalab.shop/)) — centralizing inventory, order intake, fulfilment, costing/margins, and delivery scheduling that currently live in WhatsApp messages and people's heads.

> **Status:** 📋 Requirements phase — discovery largely done, data model drafted, ready to move toward schema + build.

## What it is

An **internal operations app** for Nadeem and the operational team. Sellers and customers do **not** log in — they keep dealing via WhatsApp (by design). The team records orders, tracks stock across locations, and manages fulfilment inside the app.

## Why this exists

Today the operation runs on manual coordination:

- Orders arrive as free-text WhatsApp messages (name / phone / quantity), from a seller or directly from customers via the website (checkout intentionally hands off to WhatsApp).
- Inventory is split between two cold-chain storage locations (Nadeem's house + team's house) with no shared, live view.
- Products are bought in batches with batch numbers and expiry dates — none of it tracked in a system.
- Restocking, landed costing (including lab testing via [Janoshik](https://janoshik.com/)), and margins are ad-hoc.
- Delivery scheduling (Lalamove runs, courier postage, COD, self-pickup) is tracked mentally, but it determines what gets prepared and when.

## Core problem areas (v1 scope)

| # | Area | Problem today |
|---|------|---------------|
| 1 | **Inventory** | Stock split across two locations, batch/expiry untracked, no live central count |
| 2 | **Order management** | Free-text WhatsApp orders; no status/progress tracking |
| 3 | **Restocking** | No reorder signals; multiple suppliers with lead times untracked |
| 4 | **Costing & margin** | Batch costs, testing costs, seller vs public pricing not systematically tracked |
| 5 | **Delivery scheduling** | Delivery method + date determine prep work, but nothing is scheduled in a system |
| 6 | **Cash & cashflow** | Cash position, stock purchases, salary, packaging and other fixed/variable costs not tracked anywhere |

## Documentation

| Doc | Purpose |
|-----|---------|
| [`docs/01-current-state.md`](docs/01-current-state.md) | How the business operates today (as-is) |
| [`docs/02-requirements.md`](docs/02-requirements.md) | Feature specs / functional requirements for v1 (to-be) |
| [`docs/03-open-questions.md`](docs/03-open-questions.md) | Remaining questions before/while building |
| [`docs/04-data-model.md`](docs/04-data-model.md) | Draft data model for the Supabase schema |
| [`docs/05-user-journeys.md`](docs/05-user-journeys.md) | How admin + ops move through the app, journey by journey |
| [`docs/06-ui-ux.md`](docs/06-ui-ux.md) | Screens, navigation, wireframes, design conventions |
| [`docs/mockups/app-mockup.html`](docs/mockups/app-mockup.html) | Clickable mockup — open in a browser to see the app |

## Tech stack (decided)

- **Backend / DB:** Supabase — Postgres, Auth (team logins only), Row Level Security
- **Frontend:** Mobile-first web app deployed on **Vercel** (default `.vercel.app` domain is fine)
- **Currency/locale:** MYR, Malaysia (Asia/Kuala_Lumpur)

## Working agreement

Docs first, then schema, then build. Every requirement in `docs/02-requirements.md` should trace back to a real pain point in `docs/01-current-state.md`.
