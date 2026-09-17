# Case Study: Drivers & packers apps on a shared operations database

**Role:** Full-stack engineer (product + backend ownership)  
**Domain:** Field-service / plant-floor logistics (anonymized employer)  
**Type:** Role-specific mobile/tablet web apps tied to CRM commercial data  

---

## Problem

Commercial work lived in a **CRM** (deals, contacts, line items, stages, invoices). Day-to-day execution needed something crews could actually use:

- **Drivers** — today’s visits, measurements, payments, finish/reschedule flows  
- **Packers** — plant-floor pack/unpack queues against the same physical units  
- **Office** — trust that field and plant changes land cleanly back in CRM  

One desktop CRM screen was too slow and cluttered for vans and packing tables. Spreadsheets and chat transcriptions created drift between “what CRM says” and “what the crew did.”

## What I built

Two role-focused apps on a **shared Postgres operations database**, with selective CRM push/pull:

### Drivers / field app

- Day list of assigned visits (by vehicle / crew)  
- Capture and edit physical units (service-specific forms: dimensions, product types, quantities)  
- Collect / record payments and proof where required  
- Finish a visit (success / unsuccessful), reschedule, or move into quotation-style paths  
- Supporting field extras used in production (shift summaries, tips handling, thermal/print flows, photo proof)  
- Live office map fed from the same location ingest path (native wrapper for background GPS where needed)  

Realtime refresh (e.g. Socket.IO) kept office screens in sync when crews saved.

### Packers / plant app

- Tablet UI for pack and unpack against shared unit delivery states  
- Queue filtered to what the floor actually needs now  
- When an order is fully packed, push unit statuses and move the commercial stage toward “ready to deliver”  
- Audit history plus a daily productivity summary (pieces / orders / unpacks), exportable for managers  

Both apps write the **operations DB first**, then participate in the same CRM reconciliation rules as the office tools — they are not a separate shadow system.

## How the apps relate to CRM + DB

```
CRM (schedule, commercial fields, stages)
        │  pull / automation
        ▼
Postgres ops DB  ←── drivers app / packers app / office tools
        │  selective push (finish, pack-complete, PCS, payments)
        ▼
CRM (updated stage / amounts / unit statuses)
```

Key design rules I owned:

- Field-measured units can exist **before** they have a CRM link — sync must claim them, not blindly duplicate  
- Terminal visit history stays intact when schedules change  
- Packing updates must not fight driver measurements or office edits  
- Loop guards so DB→CRM notify and CRM→DB automation don’t thrash each other  

## Constraints

- Patchy mobile networks → clear save/error recovery; avoid silent partial writes  
- Multiple service types sharing one unit model  
- Crew UX simplicity vs office/CRM auditability  
- Same pricing and payment flags the commercial team already trusts  

## My role

- Designed and shipped the drivers and packers experiences against real crew feedback  
- Wired finish / pack-complete / unit-status paths into CRM stage and subform updates  
- Debugged production races where field saves and CRM automation collided  
- Kept office reporting and live maps consistent with what crews actually recorded  

## Outcome

- Crews run the day without living inside the full CRM UI  
- Plant floor and vans share one unit/order truth with the office  
- Fewer manual “copy from chat into CRM” steps; clearer handoff from pickup → pack → deliver  

## Technologies

Node.js · Express · mobile/tablet web UI · PostgreSQL · Socket.IO · CRM REST + automation · VPS / PM2  

## What’s intentionally not shared

Employer name, app URLs, customer/job data, schemas, credentials, and source code.
