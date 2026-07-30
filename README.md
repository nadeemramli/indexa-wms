# INDEXA WMS

Order & stock management system for **Indexa Lab** ([indexalab.shop](https://indexalab.shop/)) — centralizing inventory, order intake, fulfilment, costing/margins, and delivery scheduling that currently live in WhatsApp messages and people's heads.

> **Status:** 📋 Discovery / requirements phase. No application code yet — we are documenting the business and requirements before building.

## Why this exists

Today the operation runs on manual coordination:

- Orders arrive as free-text messages from sellers (name / phone / quantity).
- Inventory is split between two people (owner + fulfiller), with no shared, live view — "how much stock do we have?" requires asking someone.
- Restocking decisions, costing, and margin calculations are ad-hoc.
- Delivery scheduling (e.g. "Lalamove this Saturday" vs "next week") is tracked mentally, but it determines what gets prepared and when.

INDEXA WMS centralizes all of this into one system with a single source of truth.

## Core problem areas (v1 scope)

| # | Area | Problem today |
|---|------|---------------|
| 1 | **Inventory** | Stock split across two holders, no live central count |
| 2 | **Order management** | Free-text order submission; no status/progress tracking |
| 3 | **Restocking** | No reorder signals; restock decisions are reactive |
| 4 | **Costing & margin** | Cost price, selling price, and margin not systematically tracked |
| 5 | **Delivery scheduling** | Delivery method + date determine prep work, but nothing is scheduled in a system |

## Documentation

| Doc | Purpose |
|-----|---------|
| [`docs/01-current-state.md`](docs/01-current-state.md) | How the business operates today (as-is) |
| [`docs/02-requirements.md`](docs/02-requirements.md) | Feature specs / functional requirements for v1 (to-be) |
| [`docs/03-open-questions.md`](docs/03-open-questions.md) | Discovery questions to answer before building |

## Tech stack (proposed, not final)

Nothing is locked in yet. Candidate direction, based on tools already in use:

- **Backend / DB:** Supabase (Postgres, Auth, Row Level Security, Edge Functions)
- **Frontend:** Web app (mobile-friendly — the fulfiller will use it from a phone), deployed on Vercel
- **Possible fast-track:** internal tool builder (e.g. Retool) for an admin back-office while the seller/fulfiller-facing app is built properly

See open questions before committing to any of this.

## Working agreement

Docs first, then schema, then build. Every requirement in `docs/02-requirements.md` should trace back to a real pain point in `docs/01-current-state.md`.
