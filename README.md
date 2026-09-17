# Case Studies

Anonymized write-ups of production systems I’ve built and owned as a full-stack engineer.

These are **architecture / outcome case studies** — not source code. Sensitive employers and clients are not named. No credentials, customer data, proprietary implementations, or code samples are included.

## Studies

1. [Field-service operations platform](./01-field-service-operations-platform.md) — multi-app Node platform for orders, field teams, and ops tooling  
2. [CRM ↔ database sync for physical inventory units](./02-crm-inventory-unit-sync.md) — bidirectional sync, quantity expansion, idempotent reconciliation  
3. [Drivers & packers apps](./03-drivers-and-packers-apps.md) — field + plant apps on a shared ops DB with selective CRM push/pull  
4. [Stop lifecycle across CRM and database](./04-stop-lifecycle-crm-and-database.md) — schedule → day run → finish → stage update  
5. [Operations & finance reporting](./05-operations-and-finance-reporting.md) — day summaries, payments reconciliation, packer productivity, print/CSV  

## How to read these

Each study covers:

- Problem & constraints  
- Architecture (high level)  
- My role  
- Hard technical challenges  
- Outcomes (where shareable without sensitive metrics)  

## Stack themes across projects

TypeScript/JavaScript · Node.js · Express · Next.js · PostgreSQL · MongoDB · Socket.IO · Zoho CRM / Deluge · payment providers · Linux VPS / PM2  

---

*Built for hiring managers and technical interviewers who want depth without requiring a private codebase walkthrough.*
