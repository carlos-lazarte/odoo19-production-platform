# As-Built — Network

## Current topology

```text
LAN client
   |
   | HTTP :80
   v
192.168.0.8 nginx-srv
   |
   | HTTP :8069
   v
192.168.0.7 odoo-srv
   |
   | TCP :5432 localhost
   v
PostgreSQL
```

## Current addresses

- Gateway: `192.168.0.1`
- `odoo-srv`: `192.168.0.7/24`
- `nginx-srv`: `192.168.0.8/24`
- Docker bridge on `nginx-srv`: `172.17.0.1/16`

## Current exposed ports

| Host | Port | Purpose | Intended exposure |
|---|---:|---|---|
| nginx-srv | 80 | HTTP reverse proxy | LAN/current lab; later redirect to HTTPS |
| nginx-srv | 443 | HTTPS | Production public/LAN entry point |
| odoo-srv | 8069 | Odoo HTTP | Only Nginx should reach it after hardening |
| odoo-srv | 5432 | PostgreSQL | Local host only |

## Validation evidence

- `nginx-srv -> 192.168.0.7` ICMP: successful, 0% loss.
- `nginx-srv -> http://192.168.0.7:8069/web/login`: HTTP 200.
- `nginx-srv -> http://127.0.0.1/web/login`: HTTP 200 through the Nginx container.

## Production target

The final network policy should expose only TCP 80/443 on the reverse proxy. TCP 8069 must be restricted to the Nginx host. PostgreSQL must remain inaccessible from the LAN/Internet.
