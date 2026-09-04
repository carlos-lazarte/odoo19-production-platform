# As-Built — Versions

Validated versions from the current deployment.

| Component | Version |
|---|---|
| Odoo | 19.0 |
| Python | 3.10.12 |
| Docker Engine | 29.7.2 |
| Docker Compose | 5.5.0 |
| containerd | 2.3.4 |
| runc | 1.4.3 |
| Nginx container image | `nginx:stable` (validated runtime: 1.30.4) |
| Odoo OS | Ubuntu 22.04 LTS amd64 |
| Nginx OS | Debian 12 minimal |

## Installation model

- Odoo: source installation under `/opt/odoo19`.
- Python virtual environment: `/opt/odoo19/venv`.
- Odoo service: systemd unit `odoo19.service`.
- Nginx: Docker Compose project under `/opt/nginx-odoo`.
- PostgreSQL: local to `odoo-srv`.

## Dependency notes

Odoo's dependency set must be managed from the Odoo source release requirements. The current environment uses Python 3.10 and the Odoo 19 dependency constraints. `libsass` is pinned to `0.20.1` in the validated environment.
