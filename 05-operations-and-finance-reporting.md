# Case Study: Operations & finance reporting for field days

**Role:** Full-stack engineer  
**Domain:** Service logistics with same-day cash and digital payments (anonymized)  
**Type:** Office reporting hub + print/CSV exports on top of the ops database  

---

## Problem

After linking CRM and the operations DB, managers still asked:

- What did each vehicle do today?  
- Which stops succeeded vs missed targets?  
- What was collected, what is still unpaid, what has signed proof?  
- How productive was the packing floor?  
- Do driver shift printouts match the office summary?  

Spreadsheets exported from CRM were stale by midday. Finance and ops needed **live reports** against the same database drivers and packers write to — without giving everyone raw SQL or a separate BI warehouse.

## What I built

An **orders / reports hub** (with a permissioned dashboard shell) that serves ops and finance views as web pages, JSON APIs, CSV downloads, and print/PDF where needed.

### Operations

- Schedules by date and vehicle  
- Teams view (vehicle ↔ crew assignments)  
- Monthly bonus / success-vs-target style summaries  
- Stop timeline + repair tools (CRM history vs DB)  

### Finance / reconciliation

- Per-vehicle day summary — stops, payments, unpaid balances, units, signed proof  
- Payments editor for office corrections  
- Gateway / online payment ledgers  
- Tips lock & print flows  
- Browser-side invoice / quote PDF generation for commercial follow-up  

### Analytics / plant floor

- Measurement summaries (counts and area where relevant)  
- Packers daily summary — pieces, orders, unpacks — with CSV export  

### Drivers alignment

- A4 shift reconciliation printouts designed to match the office day summary so crews and managers argue from the same numbers  

## Design principles

- **One source of truth** — reports read the ops DB that field apps mutate; they don’t invent a parallel dataset  
- **Role-gated** — dashboard shell embeds only what each role should see  
- **Actionable** — several screens are not read-only; ops can repair payments or inspect stop timelines  
- **Exportable** — CSV / print / PDF for people who still close the day offline  

## My role

- Built and iterated the report surfaces managers actually use to close the day  
- Aligned driver printouts with office summaries to reduce disputes  
- Connected reporting to CRM↔DB stop and payment semantics (so totals don’t disagree with stages)  
- Kept exports free of secrets while remaining useful operationally  

## Outcome

- Day close moved from ad-hoc spreadsheets toward shared live reports  
- Finance and ops can reconcile cash, online payments, and unpaid work per vehicle  
- Packing productivity is visible without manual floor tallies  

## Technologies

Node.js · Express · PostgreSQL · web print/PDF · CSV APIs · SSO/role-gated dashboard shell · CRM-aligned payment/stop data  

## What’s intentionally not shared

Employer name, exact bonus formulas, customer financials, credentials, and source code.
