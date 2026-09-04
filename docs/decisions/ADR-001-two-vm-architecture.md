# ADR-001: Two-VM Architecture

- **Status:** Accepted
- **Date:** 2026-09-04

## Context

The platform needs a clear separation between the web/reverse-proxy tier and the Odoo application/database host while remaining simple enough for a small deployment.

## Decision

Use two virtual machines:

1. `nginx-srv` for Nginx and Docker.
2. `odoo-srv` for Odoo 19 and PostgreSQL.

Nginx is the only externally exposed application entry point. Odoo communicates with PostgreSQL locally.

## Rationale

- Reduces the attack surface of the application host.
- Separates TLS/reverse-proxy responsibilities from application execution.
- Keeps PostgreSQL isolated from the network.
- Provides a straightforward path to later scaling or separating PostgreSQL if requirements change.

## Consequences

- Requires reliable network connectivity between the two VMs.
- Requires stable service addressing or DNS.
- Firewall rules must explicitly permit Nginx-to-Odoo traffic.
- Troubleshooting must consider two hosts instead of one.

## Revisit when

Reconsider this architecture if availability, database scale, horizontal application scaling or operational requirements justify additional tiers.
