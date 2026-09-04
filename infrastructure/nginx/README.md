# Nginx Infrastructure

This directory contains the reproducible Nginx reverse-proxy definition for the current two-VM Odoo architecture.

## Files

- `compose.yml` — Docker Compose service definition.
- `nginx.conf` — reverse-proxy configuration template.

## Current deployment

The validated deployment runs Nginx in a Docker container on `nginx-srv` and forwards requests to Odoo on TCP 8069.

The template intentionally uses the placeholder `${ODOO_UPSTREAM}` rather than embedding an environment-specific address. Before deployment, render or generate the runtime configuration with the approved Odoo upstream address.

## Validation sequence

Run configuration validation before reloading Nginx:

```bash
docker compose config
docker compose up -d
docker exec nginx-odoo nginx -t
curl -I http://127.0.0.1/web/login
```

For a configuration change, prefer:

```bash
docker exec nginx-odoo nginx -t && docker exec nginx-odoo nginx -s reload
```

Do not expose PostgreSQL through this stack.

## HTTPS evolution

The next version will add:

- explicit production FQDN;
- TLS certificate mounts;
- HTTP-to-HTTPS redirect;
- certificate renewal/reload procedure;
- security headers;
- secure cookie handling;
- optional websocket proxying only if Odoo multiprocessing/gevent mode is enabled.
