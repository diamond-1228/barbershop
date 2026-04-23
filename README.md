# barbershop

Multi-tenant SaaS for barbershop operations, focused on scheduling, public booking, per-tenant catalog, barbers, services, and operational control.

The project started as an internal control tool and now operates as a React + Supabase application with authentication, `barbershop_id` isolation, tenant-aware RLS, and separate flows for owner, barber, and end customer.

<p align="left">
  <img src="https://img.shields.io/badge/React-19-61DAFB?style=for-the-badge&logo=react&logoColor=111827" />
  <img src="https://img.shields.io/badge/TypeScript-3178C6?style=for-the-badge&logo=typescript&logoColor=white" />
  <img src="https://img.shields.io/badge/Vite-646CFF?style=for-the-badge&logo=vite&logoColor=white" />
  <img src="https://img.shields.io/badge/TailwindCSS-38B2AC?style=for-the-badge&logo=tailwind-css&logoColor=white" />
  <img src="https://img.shields.io/badge/Supabase-3FCF8E?style=for-the-badge&logo=supabase&logoColor=111827" />
  <img src="https://img.shields.io/badge/Vitest-6E9F18?style=for-the-badge&logo=vitest&logoColor=white" />
  <img src="https://img.shields.io/badge/Playwright-2EAD33?style=for-the-badge&logo=playwright&logoColor=white" />
</p>

---

## Overview

**barbershop** is a SPA for barbershop management with a multi-tenant SaaS model.

The product covers three main areas:

- internal barbershop operations;
- public booking by slug at `/book/:slug`;
- tenant isolation by `barbershop_id`.

The app currently supports internal scheduling, per-tenant catalog, owner onboarding, barber-to-user linking, barber's own panel, and operational barbershop configuration.

Current classification:

```txt
Technical MVP: completed
Pilot baseline: completed
Controlled pilot: ready
Scalable commercial SaaS: still evolving
```

---

## Problem it solves

Small and medium barbershops typically manage scheduling, commissions, services, and closing via spreadsheets, notebooks, or messages.

This workflow tends to cause:

- loss of operational history;
- manual commission calculation;
- mixed scheduling between professionals;
- difficulty opening public booking without exposing internal data;
- dependency on manual database adjustments to bring a new barbershop into operation;
- risk of data mixing between tenants in a poorly isolated SaaS evolution.

barbershop centralizes these operations in a single application, with tenant isolation and role-based flows.

---

## Main features

- internal scheduling with appointment status;
- public booking by slug;
- per-tenant barber catalog;
- per-tenant service catalog;
- business hours and `slot_step_minutes` configuration per barbershop;
- owner onboarding and operational checklist;
- operational empty states for tenants without data;
- public barbershop branding;
- barber access invitation with explicit acceptance;
- invitation UX with friendly messages;
- barber panel with own schedule;
- manual appointment creation by barber using authenticated session;
- gross revenue, calculated commission, estimated balance, advances, and reports;
- operational messages for known errors;
- contextualized and sanitized technical logs;
- Supabase persistence with local fallback for dev/demo only;
- authentication with `owner` and `barber` roles;
- multi-tenant isolation by `barbershop_id`.

---

## Current product state

barbershop is at multi-tenant pilot baseline. The technical MVP is completed and the app is ready for controlled pilot, as long as the real Supabase environment is configured and validated. Evolution to scalable commercial SaaS still depends on billing, formal team invitation, global admin, and external observability.

Summary:

```txt
Technical MVP: completed
Pilot baseline: completed
Controlled pilot: ready
Scalable commercial SaaS: still evolving
```

Current functional state:

- multi-tenant by `barbershop_id`;
- public booking via `/book/:slug`;
- guided owner onboarding to create their own barbershop;
- operational booking readiness checklist;
- per-tenant barber and service catalog;
- business hours and `slot_step_minutes` per barbershop;
- per-tenant public branding;
- empty states for barbershops without barbers, services, schedule, or financial data;
- commercial barber linking requires invitation and explicit acceptance via `accept_team_invitation`; see `docs/team-invitations.md`;
- invitation UX without exposing internal details;
- barber panel restricted to own schedule;
- manual appointment creation by barber using `authSession.barbershopId` and `authSession.barberId`;
- invalid slug blocking on public booking;
- payload validation on public booking;
- duplicate active slot blocking by `barbershop_id`, `barber_id`, and `start_at`;
- reports with explicit naming: gross revenue, calculated commission, and estimated balance;
- human-readable messages for known operational errors;
- contextualized and sanitized technical logs for diagnostics;
- fail-closed in production without configured Supabase;
- automated coverage with Vitest and Playwright;
- initial bundle, splash/loading, and initial render optimizations.

The local fallback exists for development and demonstration. In production, the app should not operate real data without configured Supabase.

---

## Demo

Main routes:

```txt
/                 internal panel or public flow based on context
/book/:slug       barbershop public booking
/book             public route without explicit slug
/agendar          public alias
/onboarding       owner's initial barbershop creation
```

Public flow:

```txt
Customer accesses /book/:slug
-> selects barber
-> selects service
-> selects date and time
-> enters name and phone
-> confirms appointment
```

Owner flow:

```txt
Owner enters the panel
-> configures barbershop
-> sets days/hours
-> creates barbers
-> creates services
-> checks checklist
-> uses the /book/:slug link
```

Barber flow:

```txt
Barber enters with linked account
-> sees only own schedule
-> creates manual appointment only for themselves
-> updates status of own operational flow
```

---

## Architecture

The application separates interface, business rules, and persistence through repositories.

```txt
React Frontend
|- public routes
|  \- public booking by slug
|
|- internal panel
|  |- owner
|  \- barber
|
|- domain layer
|  |- appointments
|  |- barbers
|  |- services
|  |- barbershops
|  \- finance
|
|- repositories
|  |- Supabase
|  \- local fallback for dev/demo
|
\- infrastructure
   |- Supabase Auth
   |- Supabase Database
   |- Supabase RLS
   \- Vercel
```

Key decisions:

- React + TypeScript on the frontend;
- Supabase as managed backend;
- repositories to separate UI and persistence;
- local fallback only outside production;
- public booking decoupled from full `appointments`;
- tenant isolation by `barbershop_id`.

---

## Stack

### Frontend

- React 19
- TypeScript
- Vite
- TailwindCSS
- Framer Motion
- jsPDF

### Backend as a Service

- Supabase Auth
- Supabase Database
- Supabase Row Level Security
- Supabase Storage for branding

### Quality

- Vitest
- Playwright
- TypeScript typecheck
- GitHub Actions

### Deploy

- Vercel

---

## Security and multi-tenant

The app uses `barbershop_id` as the main tenant isolation key.

Base model:

```txt
barbershops
|- profiles
|- barbers
|- services
|- appointments
\- financial records
```

Key points of the current model:

- tenant-aware RLS in Supabase;
- owner operates their own barbershop;
- barber operates only within their own authenticated scope;
- public booking does not perform public `SELECT` on `appointments`;
- public availability uses `public.get_public_appointment_slots(uuid)`;
- public payload is validated before reaching Supabase;
- active slot conflicts are blocked in the app and by partial unique index in the database;
- dedicated RPCs for invitation issuance, revocation, and acceptance;
- automated regression for sensitive cross-tenant mutations;
- technical logs are sanitized to avoid tokens, sessions, headers, and credentials;
- fallback/localStorage should not be treated as production auth.

Important limitations:

- this does not mean absolute security;
- SPA session persistence still deserves continuous hardening against XSS;
- CAPTCHA, external rate limiting, and observability can still evolve.

More details:

- [docs/security-model.md](./docs/security-model.md)
- [docs/team-invitations.md](./docs/team-invitations.md)
- [docs/production-error-visibility.md](./docs/production-error-visibility.md)
- [docs/deployment.md](./docs/deployment.md)

---

## Public booking

Public booking is tenant-aware and operates by slug.

Current behavior:

- loads barbershop by `/book/:slug`;
- lists only active barbers of the barbershop;
- lists only active services of the barbershop;
- uses business hours and `slot_step_minutes` from the slug's barbershop;
- validates `barbershop_id`, `barber_id`, `service_id`, `start_at`, and `end_at`;
- creates appointment without requiring public `SELECT` on `appointments`;
- blocks active time conflict for the same barber;
- displays specific messages for invalid slug, incomplete tenant, time conflict, and technical failure.

Public booking should not silently assume a default tenant in production.

---

## Internal panel

The internal panel now clearly differentiates owner and barber.

### Owner

- creates and configures their own barbershop;
- edits public branding;
- sets operating days and hours;
- sets `slot_step_minutes`;
- creates, edits, removes, or deactivates barbers and services;
- uses operational checklist for booking readiness;
- invites barber, who explicitly accepts to link their account;
- receives specific messages for catalog, configuration, upload, scheduling, and barber linking failures.

### Barber

- enters only with a consistent profile;
- sees only their own schedule;
- creates manual appointment only for their own `barberId`;
- uses `barbershopId` and `barberId` from the session, not the form;
- receives clear guidance when profile is incomplete or session needs renewal.

Full operational flow:

- [docs/owner-barber-operational-flow.md](./docs/owner-barber-operational-flow.md)

---

## Running locally

Clone the repository:

```bash
git clone https://github.com/diamond-1228/barbershop.git
cd barbershop
```

Install dependencies:

```bash
npm install
```

Create the environment file:

```bash
cp .env.example .env
```

Configure:

```txt
VITE_SUPABASE_URL=
VITE_SUPABASE_ANON_KEY=
```

Run in development:

```bash
npm run dev
```

Access:

```txt
http://localhost:5173
```

Without configured Supabase, the app can use local fallback in dev/demo only. In production, the expected behavior is fail-closed.

To configure a new Supabase environment, review and manually apply the sequence documented in [docs/deployment.md](./docs/deployment.md):

```txt
supabase/migrations (full sequence 001-034; manual review and remote application)
```

Do not apply only the base schema in production; tenant-aware RLS, public availability RPC, conflict index, and invitation RPCs are part of the current operational state.

---

## Tests and quality

Current coverage:

- Vitest for domain rules, repositories, and security regression;
- Vitest for readiness, public booking, RBAC, owner catalog, barber linking, reports, and error visibility;
- Playwright E2E for public booking;
- Playwright E2E for barber panel;
- Playwright E2E for owner operational flow;
- regression tests for tenant isolation and sensitive mutations;
- fail-closed validation without Supabase in production;
- friendly message and sanitized log validation for operational errors.

Main commands:

```bash
npm run test
npm run typecheck
npm run build
npm run validate
npm run check
npm audit --audit-level=moderate
npx playwright test
```

Related documentation:

- [docs/security-model.md](./docs/security-model.md)
- [docs/dependency-audit.md](./docs/dependency-audit.md)
- [docs/owner-barber-operational-flow.md](./docs/owner-barber-operational-flow.md)
- [docs/team-invitations.md](./docs/team-invitations.md)
- [docs/performance-notes.md](./docs/performance-notes.md)
- [docs/production-error-visibility.md](./docs/production-error-visibility.md)

---

## Performance

After the `v0.9.0-pilot` baseline, the app received a performance pass focused on initial loading:

- initial bundle reduced from approximately 738 kB to 491 kB;
- main chunk gzip reduced from approximately 203 kB to 138 kB;
- Vite warning about chunk larger than 500 kB no longer appears;
- lazy loading applied to dashboards, modals, and internal surfaces;
- public booking preserved as direct entry for `/book/:slug`;
- splash and initial loading were reduced to not dominate the first render;
- booking images use `decoding="async"` and the above-the-fold cover uses high priority when applicable.

Last recorded measurement on Lighthouse mobile simulation:

```txt
FCP         ~2.1s
LCP         ~3.3s
Speed Index ~2.1s
```

These numbers vary by network, device, cache, and tenant loaded data. The changes did not alter public booking, owner dashboard, barber dashboard, or tenant isolation.

---

## Current limitations

The project does not yet implement:

- billing or subscription;
- formal email invitation;
- global admin or multi-tenant operational support;
- external observability or centralized log collection;
- external rate limit and CAPTCHA;
- complete commercial flow for SaaS scale;
- additional session hardening and XSS surface.

---

## Roadmap

Most relevant next steps:

- validate controlled pilot with a real barbershop;
- evolve commercial barbershop onboarding;
- implement billing and subscription;
- create more formal team invitation;
- create global admin/operational support;
- expand observability and operational auditing outside the console;
- evaluate rate limiting and CAPTCHA for public booking;
- further harden session and XSS surface;
- continue polishing onboarding, owner, and barber UX.

---

## License

See the [LICENSE](./LICENSE) file.

---

## Author

Developed by diamond-1228.

GitHub:

https://github.com/diamond-1228

Last updated: 4 Mar 2026
