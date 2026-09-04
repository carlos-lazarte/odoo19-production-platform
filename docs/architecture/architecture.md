# Architecture

## Objective

Document the validated two-VM Odoo 19 topology and its production hardening boundaries.

## Logical flow

```text
Client
  |
  | TCP 80/443
  v
nginx-srv
  |
  | HTTP 8069
  v
odoo-srv
  |
  | local TCP 5432
  v
PostgreSQL
```

## Responsibilities

### nginx-srv

- Debian 12.
- Docker Engine and Docker Compose.
- Nginx reverse proxy.
- Public entry point for HTTP/HTTPS.
- TLS termination.
- Forwarded-header handling.
- Access to Odoo only through the application endpoint.

### odoo-srv

- Ubuntu 22.04 LTS.
- Odoo 19 Community installed from source.
- Python virtual environment.
- systemd service management.
- PostgreSQL local to the host.
- Application port 8069.

### PostgreSQL

PostgreSQL is intentionally colocated with Odoo for the current architecture. The database is not exposed to the LAN or Internet.

## Trust boundaries

1. Client to Nginx: untrusted network boundary.
2. Nginx to Odoo: controlled application-tier boundary.
3. Odoo to PostgreSQL: local host boundary.

## Current deployment mode

Odoo currently runs in threaded mode. Therefore, the architecture does not expose a separate Odoo gevent/websocket port. A websocket listener such as 8072 must only be introduced when Odoo multiprocessing/gevent workers are deliberately enabled.

## Production evolution

Before production exposure, validate stable DNS, HTTPS, firewall policy, secret rotation, backups, restore procedures, monitoring and rollback. Environment-specific values must be supplied through deployment configuration rather than committed as secrets.
