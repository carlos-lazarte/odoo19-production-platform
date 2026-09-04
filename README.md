# Odoo 19 Production Platform

Production-oriented reference architecture for deploying **Odoo 19 Community** from source with PostgreSQL and an Nginx reverse proxy on separate virtual machines.

## Architecture

```text
                         Internet / LAN clients
                                  |
                             TCP 80 / 443
                                  |
                                  v
                    +---------------------------+
                    | nginx-srv                  |
                    | Debian 12                  |
                    | Docker + Nginx             |
                    | 192.168.0.8                |
                    +-------------+-------------+
                                  |
                             HTTP 8069
                                  |
                                  v
                    +---------------------------+
                    | odoo-srv                   |
                    | Ubuntu 22.04               |
                    | Odoo 19 source             |
                    | 192.168.0.7                |
                    | PostgreSQL localhost       |
                    +-------------+-------------+
                                  |
                             TCP 5432
                                  |
                                  v
                    +---------------------------+
                    | PostgreSQL                 |
                    | Local to odoo-srv          |
                    +---------------------------+
```

## Goals

- Reproducible Odoo 19 source installation.
- Clear separation between application and reverse-proxy tiers.
- PostgreSQL kept local to the Odoo application host.
- Nginx containerized with Docker Compose.
- HTTPS, firewalling, backups, restore testing and observability treated as first-class operational concerns.
- Infrastructure and operational knowledge maintained as code and documentation.

## Repository layout

```text
odoo19-production-platform/
├── docs/
│   ├── architecture/
│   ├── runbooks/
│   ├── operations/
│   ├── security/
│   ├── decisions/
│   └── as-built/
├── infrastructure/
│   ├── nginx/
│   ├── odoo/
│   └── firewall/
├── scripts/
│   ├── preflight/
│   ├── validation/
│   ├── backup/
│   └── recovery/
├── .github/
│   ├── workflows/
│   ├── ISSUE_TEMPLATE/
│   └── pull_request_template.md
├── CHANGELOG.md
├── CONTRIBUTING.md
├── SECURITY.md
├── LICENSE
└── README.md
```

## Current implementation

The initial platform consists of two VMs:

| Component | Host | Address | Role |
|---|---|---:|---|
| Nginx | `nginx-srv` | `192.168.0.8` | Reverse proxy |
| Odoo + PostgreSQL | `odoo-srv` | `192.168.0.7` | Application + database |

The addresses above describe the current lab/as-built environment. Production deployments should use stable addressing, preferably DHCP reservations or managed DNS.

## Security principles

- Never commit passwords, private keys, database dumps, certificates or other secrets.
- Odoo PostgreSQL access remains local unless there is an explicit architectural requirement to separate the database tier.
- Odoo port `8069` should be reachable only from the reverse-proxy host once the proxy path is validated.
- Public access should terminate TLS at Nginx.
- `proxy_mode = True` should be enabled in Odoo only when it is actually deployed behind the trusted reverse proxy.
- HSTS should be enabled only after HTTPS is stable and validated.

## Documentation

Documentation will be organized as operational runbooks, architecture records, security controls and as-built evidence. Major architectural decisions are recorded as ADRs.

## Status

**Phase: platform bootstrap**

The repository is being populated incrementally from the validated Odoo 19 deployment and its production hardening plan.

## License

To be defined for the project. No license is granted until a `LICENSE` file is explicitly added.
