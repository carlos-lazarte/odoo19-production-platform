# ADR-003: PostgreSQL Local to Odoo

- **Status:** Accepted
- **Date:** 2026-09-04

## Context

The current deployment is a single Odoo application host. Introducing a separate database VM would add network and operational complexity without a current requirement.

## Decision

Keep PostgreSQL on `odoo-srv` and bind Odoo's database connection to the local PostgreSQL service.

## Rationale

- PostgreSQL is not exposed to the network.
- Low latency between Odoo and the database.
- Simpler backup and recovery topology for the current scale.
- Fewer infrastructure components.

## Consequences

- Odoo and PostgreSQL share the lifecycle of the application host.
- Host resource contention must be monitored.
- A future HA or scaling requirement may justify moving PostgreSQL to a dedicated managed or remote database service.

## Security requirement

The PostgreSQL role used by Odoo should follow least privilege. Credentials must never be committed to Git.
