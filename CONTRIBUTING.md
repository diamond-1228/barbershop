# Contributing

Thank you for considering contributing to barbershop.

This project uses a simple flow: create an issue, open a small branch, validate locally, and submit a pull request.

## Local environment

```bash
npm install
npm run dev
```

## Before opening a PR

Run the full gate:

```bash
npm run check
npm audit --audit-level=moderate
```

## Branch naming

Use short and descriptive names:

- `fix/short-description`
- `feat/short-description`
- `docs/short-description`
- `test/short-description`

## Commit conventions

Prefer small commits with clear prefixes:

- `fix: correct commission calculation`
- `feat: add barber filter`
- `docs: update installation guide`
- `test: cover report rules`

## PR checklist

- The change resolves a clear issue or improvement.
- The PR explains what changed and how it was validated.
- `npm run check` passes locally.
- `npm audit --audit-level=moderate` reports no vulnerabilities.

## Closing issues

When the PR resolves an issue, include in the PR body:

```text
Closes #ISSUE_NUMBER
```
