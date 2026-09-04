# Network Architecture

## Addressing model

The current lab uses DHCP. Stable service addresses should be implemented with DHCP reservations by MAC address or managed DNS.

Current as-built values:

| Host | Address | Function |
|---|---|---|
| nginx-srv | 192.168.0.8/24 | Reverse proxy |
| odoo-srv | 192.168.0.7/24 | Odoo + PostgreSQL |
| Gateway | 192.168.0.1 | LAN gateway |

These values are environment-specific and should be treated as examples in reusable deployments.

## Required traffic

| Source | Destination | Port | Purpose |
|---|---|---:|---|
| LAN/client | nginx-srv | 80 | HTTP |
| LAN/client | nginx-srv | 443 | HTTPS |
| nginx-srv | odoo-srv | 8069 | Odoo HTTP |
| odoo-srv | localhost | 5432 | PostgreSQL |

PostgreSQL must not be published by Nginx and should not be reachable from untrusted network segments.

## DNS

For a public deployment, use a real FQDN such as `nuba.<DOMINIO_REAL>` and point public DNS to the public endpoint. For LAN-only deployments, use internal DNS/split DNS and an internal CA for trusted HTTPS.

Do not use a private IP address as a public certificate identity.

## Firewall target state

- nginx-srv: expose only TCP 80/443 as required.
- odoo-srv: expose TCP 8069 only to nginx-srv after proxy validation.
- PostgreSQL: listen locally for the current architecture; no LAN exposure.
- Management access such as SSH should be restricted independently according to the administrator's network policy.
