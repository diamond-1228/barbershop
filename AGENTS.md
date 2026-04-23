# AGENTS.md - barbershop

## Context

This repository is a React 19 + TypeScript + Vite SPA for barbershops.

The app includes:

- Internal appointment scheduling.
- Public booking at `/book` and `/agendar`.
- Financial dashboard, commissions, advances, and PDF/CSV reports.
- Supabase persistence with `localStorage` fallback.
- Supabase Auth with `owner` and `barber` roles.
- Tests with Vitest, CI on GitHub Actions, and deploy on Vercel.

## Main Rule

Stability first.

Make small, focused, and reviewable changes. Do not make large refactors without explicit request.

## Before Making Changes

1. Check the branch and status:

```bash
git status --short --branch
```

2. Read relevant files before editing.
3. Preserve existing user changes.
4. Do not touch financial, commission, PDF/CSV, dashboard, or Supabase schema without direct relation to the task.

## Standard Validation

Before finishing, run:

```bash
npm run check
npm run build
npm audit --audit-level=moderate
```

If any command cannot be executed, explain the reason.

## Supabase

- Never commit `.env`.
- Use `.env.example` to document variables.
- Public booking must not read sensitive data from `appointments`.
- In public flow, use `public.get_public_appointment_slots(uuid)` for occupied availability per tenant. Do not query `public.appointments` or `public_appointment_slots` directly in the public frontend.
- Full reading of `appointments` must be restricted to the authenticated internal panel.
- Do not solve Supabase bugs by disabling RLS, granting public SELECT on `appointments`, or creating a global tenant fallback.
- For Supabase tasks, generate review-only SQL; remote application is manual.

## SaaS Security

- Preserve tenant isolation.
- Public flows must be scoped by `barbershop_id` or slug-resolved tenant.
- Do not add global tenant fallback.
- Do not grant public SELECT on `appointments`.
- Prefer small review-only SQL files for schema/RLS/security changes.
- Security PRs must be small, focused and auditable, with validation commands and rollout notes.

## Git

- Commits should be small and clearly scoped.
- Use `gh` to open PR when the branch is ready.
- Do not include `node_modules`, `dist`, `.env`, or temporary files.
