# As-Built — Environment

> This document records the validated lab/development environment. It is not a substitute for environment discovery during a production deployment.

## Virtualization

- Hypervisor: VirtualBox
- Network mode: bridged
- Address assignment: DHCP
- Recommended production practice: DHCP reservations by MAC or managed DNS/IPAM

## Hosts

| Host | OS | Current role | Current address |
|---|---|---|---|
| `odoo-srv` | Ubuntu 22.04 LTS amd64 | Odoo + PostgreSQL | `192.168.0.7/24` |
| `nginx-srv` | Debian 12 minimal | Docker + Nginx | `192.168.0.8/24` |

## Gateway

Current gateway: `192.168.0.1`.

## Validation performed

- `nginx-srv` can reach `odoo-srv` by ICMP.
- `nginx-srv` can request Odoo directly on TCP 8069.
- Nginx container is published on TCP 80.
- Client access through Nginx reaches the Odoo login page.

## Security note

The exact addresses are included because this is an as-built record. They must not be copied blindly into another environment. Public documentation must never contain credentials, private keys or other secrets.
