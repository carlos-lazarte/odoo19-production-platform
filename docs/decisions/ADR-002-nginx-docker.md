# ADR-002: Nginx in Docker

- **Status:** Accepted
- **Date:** 2026-09-04

## Context

Nginx provides the reverse-proxy and future TLS termination layer. The deployment should be reproducible and isolated from host package drift.

## Decision

Run Nginx using the official Nginx container image and Docker Compose on `nginx-srv`.

## Rationale

- Reproducible container image deployment.
- Simple configuration mounting.
- Explicit port publication.
- Docker Compose provides a declarative service definition.

## Consequences

- Docker becomes part of the operational dependency chain.
- Container lifecycle, image updates and logging must be managed.
- Host firewalling must account for Docker networking and published ports.

## Operational rule

Validate configuration with `nginx -t` inside the container before reloading. Prefer reload over restart for routine configuration changes when the configuration is valid.
