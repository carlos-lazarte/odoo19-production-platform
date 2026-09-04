# Odoo infrastructure

This directory contains the reproducible definitions for the Odoo application host (`odoo-srv`).

## Current deployment model

- Source: `/opt/odoo19`
- Virtual environment: `/opt/odoo19/venv`
- Configuration: `/etc/odoo19.conf`
- Systemd unit: `/etc/systemd/system/odoo19.service`
- Service account: `odoo19`
- PostgreSQL: local (`127.0.0.1:5432`)
- HTTP listener: `0.0.0.0:8069`

The repository contains templates, not production secrets.

## Installation

Copy the systemd unit to `/etc/systemd/system/odoo19.service` and the configuration template to `/etc/odoo19.conf`, then replace placeholders on the target host.

Protect the configuration because it contains credentials:

```bash
sudo chown root:odoo19 /etc/odoo19.conf
sudo chmod 640 /etc/odoo19.conf
```

Validate before restarting:

```bash
sudo systemctl daemon-reload
sudo systemctl restart odoo19
sudo systemctl status odoo19 --no-pager
sudo journalctl -u odoo19 -n 100 --no-pager
```

Validate the HTTP endpoint locally:

```bash
curl -I http://127.0.0.1:8069/web/login
```

## Reverse proxy

`proxy_mode = True` is appropriate once Odoo is operated behind the trusted Nginx reverse proxy. It should not be enabled merely because a proxy is planned; the proxy must actually be part of the deployment path.

Do not bind Odoo to `127.0.0.1` in this two-VM architecture: Nginx runs on a different host and must reach TCP/8069. Instead, restrict TCP/8069 at the host firewall to the Nginx server.

## Workers

The current validated installation uses Odoo's threaded mode. Worker sizing is deliberately not hard-coded here. Multiprocessing/gevent configuration must be introduced as a measured capacity-planning change, including the corresponding long-polling/websocket path and resource limits.

## Secrets

Never commit:

- `admin_passwd`
- `db_password`
- private keys
- TLS certificates
- database dumps
- production logs

Rotate any credential that has previously been exposed outside the target host.
