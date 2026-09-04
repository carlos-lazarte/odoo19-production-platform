# ADR-004: Odoo Source Installation

- **Status:** Accepted
- **Date:** 2026-09-04

## Context

The platform is based on Odoo 19 Community and requires controlled application dependencies and explicit service management.

## Decision

Install Odoo 19 from source under `/opt/odoo19`, using a dedicated Python virtual environment and a dedicated systemd service account.

## Rationale

- Explicit control of the Odoo source version.
- Isolated Python dependencies.
- Predictable systemd lifecycle management.
- Clear separation from distribution-managed application packages.

## Consequences

- Application upgrades require deliberate source/dependency management.
- Python and native dependencies must be validated against the selected Odoo release.
- The exact source revision must be recorded for reproducibility.

## Operational requirement

Do not treat a source deployment as reproducible until the Odoo source revision, Python version, dependency set and operating-system assumptions are recorded in the As-Built documentation.
