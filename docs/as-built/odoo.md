# As-Built — Odoo

## Installation

- Version: Odoo 19.0 Community.
- Source directory: `/opt/odoo19`.
- Virtual environment: `/opt/odoo19/venv`.
- Python: 3.10.12.
- Service user: `odoo19`.
- Configuration: `/etc/odoo19.conf`.
- systemd unit: `/etc/systemd/system/odoo19.service`.
- Log: `/var/log/odoo/odoo.log`.

## Runtime

The validated service starts Odoo with:

```text
/opt/odoo19/venv/bin/python3.10 /opt/odoo19/odoo-bin -c /etc/odoo19.conf
```

Odoo listens on `0.0.0.0:8069` in the current threaded configuration.

## Database connection

PostgreSQL is local to the Odoo host and is accessed through `127.0.0.1:5432` using the dedicated `odoo19` role.

Credentials are intentionally excluded from this repository.

## Reverse proxy mode

`proxy_mode` is not enabled in the current validated HTTP-only state. It must be enabled when the application is placed behind the trusted Nginx reverse proxy for the production HTTPS configuration.

Do not bind Odoo HTTP to `127.0.0.1` in this two-VM topology because Nginx is on a different host. Network filtering must instead restrict TCP 8069 to the reverse-proxy host.

## Module/asset state

The frontend asset issue was resolved by restoring the `html_editor` module through Odoo's normal module mechanism and regenerating assets. The resulting `/web/assets/1/web.assets_frontend.min.css` returned HTTP 200 and real CSS content during validation.

## Operational caution

Configuration files may contain sensitive values. File permissions and secret rotation must be addressed before production use. Never commit the live Odoo configuration containing credentials.
