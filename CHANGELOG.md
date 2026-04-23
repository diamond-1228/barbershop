# Changelog

All notable changes to this project will be documented here.

The format follows a simple version inspired by Keep a Changelog, with versions based on GitHub tags.

## [v0.1.1] - 2026-06-05

### Added

- Local setup guide with requirements, recommended flow, and troubleshooting.
- Screenshots section and `docs/screenshots` folder.
- Label guide for issues and pull requests.
- Documented roadmap in `docs/ROADMAP.md`.
- Operational backup and restore guide.
- Security policy, support, and code of conduct.
- Dependabot configuration for npm and GitHub Actions.
- Architecture documents and release process.

### Changed

- README updated with links to operational guides and roadmap.
- Repository community health improved with standard GitHub files.

## [v0.1.0] - 2026-06-04

### Added

- Appointment, product, and advance registration flow.
- Daily dashboard and monthly summary.
- Report export in PDF and CSV.
- Local data backup and restore.
- Unit tests for financial rules, local dates, and CSV.
- CI with GitHub Actions.
- Issue and pull request templates.
- Contribution guide.

### Changed

- README restructured with stack, scripts, verification, and roadmap.
- PDF generator loaded on demand to reduce initial bundle.
- Dependencies updated and audited.

### Fixed

- Preservation of manual commission equal to zero.
- Report filter by local date.
- Validation of negative or invalid financial values.
- Basic backup validation before restoring data.

[v0.1.1]: https://github.com/diamond-1228/barbershop/releases/tag/v0.1.1
[v0.1.0]: https://github.com/diamond-1228/barbershop/releases/tag/v0.1.0
