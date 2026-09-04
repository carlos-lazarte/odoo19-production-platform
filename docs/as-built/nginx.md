# As-Built — Nginx

## Installation model

- Host: `nginx-srv`.
- OS: Debian 12 minimal.
- Docker Engine: 29.7.2.
- Docker Compose: 5.5.0.
- Project directory: `/opt/nginx-odoo`.
- Compose file: `/opt/nginx-odoo/compose.yml`.
- Nginx configuration: `/opt/nginx-odoo/nginx/nginx.conf`.
- Container: `nginx-odoo`.
- Image: `nginx:stable`.

## Current Compose model

The container publishes:

```text
0.0.0.0:80 -> container:80
```

The configuration is mounted read-only into `/etc/nginx/nginx.conf`.

## Current upstream

```text
odoo -> 192.168.0.7:8069
```

## Proxy behavior

The current configuration forwards:

- `Host`
- `X-Real-IP`
- `X-Forwarded-For`
- `X-Forwarded-Proto`

Production configuration should also preserve `X-Forwarded-Host` and introduce HTTPS termination before public exposure.

Current application timeouts include a 60-second connect timeout and 720-second send/read timeouts.

## Validation

The Nginx container is running and local access to `/web/login` returns HTTP 200 through the reverse proxy.

## Production evolution

The next Nginx configuration stage is HTTPS with an explicit FQDN, certificate lifecycle, HTTP-to-HTTPS redirect and security headers. Websocket proxying should only be added when Odoo multiprocessing/gevent mode is intentionally enabled.
