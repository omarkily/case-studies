# Case Study: Stop lifecycle across CRM and the operations database

**Role:** Full-stack engineer  
**Domain:** Scheduled field visits for a service business (anonymized)  
**Type:** Scheduling + completion workflow spanning CRM and Postgres  

---

## Problem

A “stop” is the operational visit: pickup, delivery, or both — sequenced on a vehicle for a given day, with a time window, crew, and outcome.

Without a clear lifecycle:

- CRM schedules and DB visit rows drifted apart  
- Drivers finished work that never moved the commercial stage  
- Office couldn’t tell whether a problem was “CRM history” vs “DB reality”  
- Reordering and reschedules created duplicate or orphaned visits  

The business needed **one mental model**: CRM owns commercial scheduling intent; the database owns runtime execution; both stay reconcilable.

## Model (conceptual)

Each visit carries roughly:

- Type — pickup / delivery / combined  
- Sequence — stop number on the route (CRM sequence treated as primary)  
- Assignment — date, vehicle, crew  
- Window — planned time range  
- Outcome — open → **success** or **unsuccessful**  
- Links — related order, physical units, payments  

Local UI reorder can exist for convenience; the commercial stop number remains the source of sequence truth.

## Lifecycle I implemented / owned

### 1. Create & assign

Mostly from CRM automation: stage, date, vehicle, and stop number upsert a visit row in Postgres. Office tools can also create/edit stops through the orders/ops APIs when needed.

### 2. Day run (drivers)

- Select vehicle / crew  
- Work today’s stop list  
- Capture units → payments → finish (crew required for completion)  
- Paths for cancel, reschedule, and quotation-style outcomes  

### 3. Complete

**Database first** — persist status, route/crew, order finished flags — then **push CRM stage** (and related commercial fields) so front-office mirrors what happened in the field. Optional re-sync repairs drift.

### 4. Office visibility

- Schedules by date / vehicle  
- Teams (vehicle ↔ crew)  
- Stop timeline comparing CRM history vs DB state  
- Repair tools when the two sides disagree  

## Hard parts

- **Anti-loop sync** — tagging CRM-sourced writes so DB→CRM bridges don’t echo forever  
- **Claim before insert** — match existing visits by CRM id before creating new rows  
- **Preserve terminal history** — don’t wipe completed stops when the schedule reshuffles  
- **Asymmetric ownership** — CRM is strong on inbound schedule; apps/bridge are strong on finish and selective field updates  

## My role

- Defined stop semantics shared by CRM automation, drivers app, and office tools  
- Built completion paths that update DB then CRM without double-booking stages  
- Added timeline / repair surfaces so ops can diagnose “why doesn’t this stop match?”  
- Kept reschedule and unsuccessful outcomes from corrupting unit and payment links  

## Outcome

- Drivers finish against a stop list that matches the day’s commercial plan  
- Office sees success/unsuccessful outcomes reflected in CRM stages  
- Fewer “ghost” visits and clearer audit when something goes wrong  

## Technologies

PostgreSQL · Node.js APIs · CRM automation (Deluge) · drivers app · office schedule/report UIs  

## What’s intentionally not shared

Employer branding, production field catalogs, credentials, customer schedules, and source code.
