# Case Study: Field-service operations platform

**Role:** Full-stack engineer (end-to-end ownership)  
**Domain:** Field-service / logistics operations (UAE) — anonymized employer  
**Type:** Production multi-app platform on a Linux VPS  

---

## Problem

The business ran pickup → processing → delivery workflows with many moving parts:

- Office staff managing orders, pricing, and customer communication  
- Field crews capturing measurements / piece counts on-site  
- Warehouse / packing stages with their own UI needs  
- Finance needing payments, receipts, and invoice-ready totals  
- CRM used commercially, while operational truth lived in a Postgres database  

Spreading this across disconnected tools created sync gaps, pricing mistakes, and slow debugging when something went wrong in production.

## What I built

A **family of cooperating Node.js services** behind one operational platform, including:

| Area | Responsibility |
|------|----------------|
| Orders / reports app | Order detail, pieces, pricing, CRM pull/push, invoices |
| Drivers / field app | On-site piece entry, statuses, order workflows |
| Packers / ops views | Stage-specific queues and completion tracking |
| Payments | Payment links / provider integrations, receipts |
| Media / gallery | Job photos and signed assets |
| Messaging / notifications | Internal and customer-facing channels |
| Shared API layer | Auth’d CRUD used by CRM automation and internal tools |
| CRM bridge / automation | Keep CRM deals aligned with DB state |

Deployed with **PM2** on a VPS, Postgres as system of record for operations, and CRM as the commercial front-office system.

## Architecture (high level)

```
CRM (deals, subforms, automation)
        ↕  sync / webhooks / API
   Shared API  +  Postgres
        ↕
  Orders UI · Drivers · Packers · Payments · Ops tools
```

Key design choices:

- **Postgres as operational source of truth** for pieces, stops, payments, and order totals  
- **CRM as commercial UI + automation host** (not the only database)  
- **Multiple focused apps** instead of one monolith UI — faster iteration per role  
- **Idempotent sync paths** where CRM and DB can disagree temporarily  

## My responsibilities

- Designing and implementing core order/piece/payment flows  
- Wiring CRM automation to the API safely (create/update/delete rules)  
- Pricing recalculation and edge cases (minimums, paid pieces, service-specific rates)  
- Production debugging across sync races, duplicate rows, and stale CRM payloads  
- Deployments, env isolation, and keeping multiple PM2 apps healthy  

## Hard problems (examples)

1. **Dual writes** — CRM and DB both mutable; needed clear ownership rules so driver measurements weren’t wiped by stale CRM sync.  
2. **Role-specific UIs** — same data model, different mental models for office vs field vs packing.  
3. **Money correctness** — order totals had to stay consistent after discounts, VAT-style multipliers, restorations, and partial payments.  
4. **Operational reliability** — failures had to be diagnosable from logs/timeline, not “sync and pray”.  

## Outcome

- A production platform used daily by office and field teams  
- Faster iteration: new ops tools (kanban, maps, status boards) could ship as separate apps against the same DB/API  
- Reduced spreadsheet / manual CRM edits for core workflows  
- Clearer separation between commercial CRM data and operational execution data  

## Technologies

Node.js · Express · Next.js / server-rendered ops UIs · PostgreSQL · Socket.IO (where realtime helped) · Zoho CRM + Deluge · PM2 · Linux VPS · payment provider APIs  

## What’s intentionally not shared

Source code, credentials, customer data, internal domains, and employer branding.
