# Reporting Module — Full-Stack Code Sample

**Oksana Finch** · University at Buffalo application · November 2026

## Overview

This code sample is an excerpt of an internal application developed for a charitable foundation. Staff use reports to review operations across selected periods, monitor volunteer activity, and coordinate participating organizations. I developed the Angular frontend, Express/Sequelize backend, data aggregation, access controls, and automated tests.

**Scope:** A reviewable excerpt from a larger application — **not a standalone runnable project**. The original application's database models, app bootstrap, environment and test setup, authentication infrastructure, translation catalogs and several support services are not distributed here. Relative imports are preserved to show how the module integrates with the original codebase. See [INTEGRATION_NOTES.md](INTEGRATION_NOTES.md) before attempting to run it.

## Technologies

Angular, TypeScript, Angular Signals, typed reactive forms, RxJS, Angular Material, PrimeNG, ngx-translate, Node.js, Express, PostgreSQL, Sequelize, Zod, Jest/Supertest, Jasmine/Karma, and ExcelJS.

## Features

- Four report types: **General**, **Personal**, **School Coordination**, **By Occasion**.
- Monthly, quarterly and annual selections, with report-specific constraints.
- Aggregation by reporting period or occasion; `Map` and `Set` avoid counting the same entity more than once.
- Server-side database filtering and validation of incoming requests.
- Validated JSON API responses, loading/empty/error states, tabular output, Excel export, and translated labels.
- UI filtering of report options by permissions and protection against stale asynchronous responses.

## Request flow

1. The Angular component builds a typed report request based on user selections.
2. `ReportsService` calls `POST /api/reports/get-report/`.
3. Express authenticates, validates the request with Zod, and applies report-specific authorization.
4. Sequelize loads the relevant orders and related records from PostgreSQL.
5. `ctrl-generate-reports.js` groups and aggregates records, producing report rows.
6. The client validates the response, displays it, and can export to Excel.

The API also exposes `GET /api/reports/get-statistic/` with a separate permission check.

## Authorization rules

| Report | Access | `userId` |
| --- | --- | --- |
| General (1) | `VIEW_FULL_REPORTS` | `null` |
| Personal (2) | Any authenticated user, for their own data only | Must equal authenticated user's ID |
| School Coordination (3) | `VIEW_LIMITED_REPORTS` or `VIEW_FULL_REPORTS` | `null` |
| By Occasion (4) | `VIEW_FULL_REPORTS` | `null` |

**Security is enforced by the server.** Angular filters visible options for usability, but the server does not trust the client-supplied `userId`. `require-report-access.js` checks both report type and authenticated identity/role permissions.

## Code navigation

- `client/src/app/pages/reports/` — UI, form logic, template, and component tests.
- `client/src/app/services/reports.service.ts` — HTTP calls and response schemas.
- `server/routes/reports-api.js` — secured REST endpoints and Sequelize queries.
- `server/controllers/ctrl-generate-reports.js` — aggregation and period calculations.
- `server/middlewares/require-report-access.js` — business-level authorization rules.
- `server/tests/int/reports-api.int.test.js` — backend integration tests.
- `shared/` — shared reporting contracts and supporting constants.

## Tests

These tests were run **in the original full application** on November 15, 2026:

- **17/17** reporting API integration tests passed.
- **28/28** Angular reports component tests passed.

Additional service tests are included; this document does not assert a verified run count for those tests. **The excerpt itself has not been run independently.**

Original full-project commands (Windows PowerShell):

```powershell
npm.cmd run test:int -- reports-api.int.test.js --verbose
npm.cmd test -- --watch=false --include="**/reports.component.spec.ts"
```

These commands are informative and require the original project's package scripts and test infrastructure; there is no package.json in this excerpt.

## Engineering decisions and tradeoffs

- **Shared Zod contracts** reduce mismatches between server output and frontend expectations.
- **Permission checks before queries** prevent inappropriate report access, including another user's Personal report.
- **`Map` and `Set`** simplify grouping and unique-count calculations.
- **UTC date ranges** use inclusive start and exclusive end to avoid overlapping periods.
- **Signals and a request-version guard** prevent outdated results from appearing if newer requests or permission changes invalidate them.
- **Known limitation:** periods with no orders are not generated as explicit zero-valued rows; this is noted in the aggregation code for future requirements.

## Privacy and provenance

Test records use fictional names and example data. Production records, credentials, `.env` files, and database dumps are not included. This repository excerpt preserves module code and is intended for technical review, not as a deployable copy of the foundation application.
