# Case Study: CRM ↔ database sync for physical inventory units

**Role:** Full-stack engineer  
**Domain:** Field-service operations (anonymized)  
**Type:** Bidirectional sync between CRM subforms and a relational database  

---

## Problem

Each job (deal/order) tracks **physical units** (pieces) with dimensions, quantity, status, price, and payment flags.

Commercial users edit these in a **CRM subform**. Field/office tools edit the same units in **Postgres**.

Two representations collided:

| Side | Shape |
|------|--------|
| CRM | Often one visual line with quantity, or expanded lines (`id`, `id__2`, …) for counting |
| Database | Sometimes one row with `quantity > 1`, sometimes one row per physical unit |

Naïve sync caused:

- Duplicate “ghost” units after delete/recreate cycles  
- Inflated order totals  
- CRM looking correct while the DB (and ops UI) showed too many rows  

## Goals

1. CRM remains easy for sales/ops to edit  
2. DB remains accurate for warehouse/field workflows  
3. Sync must be **idempotent** — running twice shouldn’t keep multiplying rows  
4. No silent resurrection of units a driver already deleted  

## Approach (high level)

### Quantity model

- CRM expansions for a base unit ID are counted to restore quantity on the linked DB row  
- Ops UIs that need one-row-per-unit run a **breakdown** step (split `quantity > 1` into unit rows)  
- Breakdown must **reuse existing orphan unit rows** (same dimensions, no CRM link) instead of always inserting  

### Ownership rules

- If CRM points at a DB id that no longer exists → treat as removed; don’t recreate blindly  
- Prefer claiming pending DB rows (no CRM id yet) by id/dimensions before insert  
- Expansion lines in CRM map to the parent DB row — don’t upsert them as separate entities  

### Reconciliation

- After CRM pull/update, run breakdown + price recalculation in one transactional path where possible  
- Prune excess orphans when sync restores quantity on a base row that was already expanded  

## Hard bug class (example)

A user deleted all units, created two CRM lines (qty 4 and qty 2). CRM showed **6** correctly. DB grew to **10+** because:

1. Sync restored qty 4 / qty 2 on two base rows  
2. UI expand created unit clones without CRM ids  
3. Sync restored qty again on the bases  
4. Expand cloned again → ghost rows  

**Fix direction:** make expand idempotent (reuse/prune orphans; case-insensitive service matching) so sync↔expand loops stop growing the table.

## My role

- Traced CRM vs DB divergence with live deal/order forensics  
- Implemented idempotent expand/reconcile behavior in the orders path  
- Tightened sync assumptions around expansion ids and orphan cleanup  
- Repaired affected production orders and verified totals against CRM  

## Outcome

- CRM and DB unit counts stay aligned through delete/recreate cycles  
- Order prices stop inflating from duplicate unit rows  
- Ops UI can still show one-row-per-unit without fighting CRM’s quantity model  

## Technologies

PostgreSQL · Node.js · CRM REST / function APIs · Deluge automation · transactional updates  

## What’s intentionally not shared

Employer name, CRM org IDs, production schemas, credentials, and proprietary formula/field catalogs.
