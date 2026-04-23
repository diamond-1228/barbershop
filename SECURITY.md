# Security Policy

## Supported versions

| Version | Support |
| --- | --- |
| `main` | Yes |
| `v0.9.0-pilot` | Yes |

## Reporting vulnerabilities

If you find a vulnerability, avoid publishing sensitive details in an open issue.

Send a private summary to the maintainer or open an issue without exploitable data, describing:

- affected area;
- expected impact;
- general steps to reproduce;
- version, commit, or environment used.

## Current security scope

barbershop is a React SPA with Supabase Auth, Database, and RLS.

The current model uses `barbershop_id` as the multi-tenant isolation key:

- owner operates only their own barbershop;
- barber operates only their own barbershop and their own `barber_id`;
- public booking resolves the barbershop by slug at `/book/:slug`;
- public booking should not silently assume a default tenant;
- public booking should not perform public `SELECT` on the full `appointments` table;
- public availability should use `public.get_public_appointment_slots(uuid)`;
- public inserts into `appointments` should not request return of sensitive rows;
- commercial barber linking requires invitation and explicit acceptance via `accept_team_invitation`; see `docs/team-invitations.md`.

## Supabase

RLS policies and manual SQLs must be applied to the correct Supabase project.

Reference files:

- `docs/security-model.md`
- `docs/supabase-schema.sql`
- `docs/supabase-tenant-rls-plan.sql`
- `docs/public-appointment-availability-rpc.sql`
- `docs/appointments-active-slot-unique-index.sql`
- `docs/team-invitations.md`
- `docs/deployment.md`

Do not disable RLS to fix a frontend bug.

Do not grant public `SELECT` on `appointments`; this table contains customer data such as name, phone, and notes.

## Local fallback

The `localStorage` fallback exists for local development or demonstration without Supabase.

In production, the app should fail closed when `VITE_SUPABASE_URL` or `VITE_SUPABASE_ANON_KEY` are not properly configured. Production should not save tenant, catalog, profiles, schedule, or financial data in `localStorage` due to missing Supabase.

## Best practices

- Do not publish backups with real data.
- Do not share reports containing customer data.
- Do not commit `.env`.
- Confirm Vercel env vars before pilot or deploy.
- Run `npm audit --audit-level=moderate` before releases.
- Run the validation suite before merge:

```bash
npm run test
npm run typecheck
npm run build
npm run validate
npm run check
npm audit --audit-level=moderate
npx playwright test
```

## Known limitations

This document does not claim absolute security.

Points that can still evolve:

- CAPTCHA/external rate limit for public booking;
- observability and operational auditing outside the console;
- additional hardening against XSS and session theft in SPA;
- periodic RLS review before any tenant/RBAC change.
