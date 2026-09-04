# Firewall hardening

This directory defines the intended network policy for the two-VM deployment.

## Policy

### nginx-srv

Allow:

- SSH (TCP/22) from the administration network.
- HTTP (TCP/80) from the LAN and, when public exposure is enabled, the Internet through the router/firewall.
- HTTPS (TCP/443) from the LAN and, when public exposure is enabled, the Internet through the router/firewall.
- Established/related traffic.

Do not expose PostgreSQL or Odoo directly from nginx-srv.

### odoo-srv

Allow:

- SSH (TCP/22) from the administration network.
- Odoo HTTP (TCP/8069) **only from nginx-srv**.
- Established/related traffic.

PostgreSQL (TCP/5432) remains bound to localhost and must not be exposed to the LAN.

## Implementation rule

Before applying rules, identify the active firewall manager:

```bash
sudo ufw status verbose
sudo nft list ruleset
```

Use one authoritative host firewall manager. Do not blindly apply UFW and nftables rule sets simultaneously.

## Safe rollout

1. Keep an existing SSH session open.
2. Verify the current firewall state.
3. Add the minimum required rules.
4. Test SSH from the administration network.
5. Test nginx -> Odoo on TCP/8069.
6. Test LAN -> nginx on TCP/80 (and 443 once HTTPS exists).
7. Verify that LAN clients cannot reach Odoo:8069 directly.
8. Verify that PostgreSQL:5432 is not reachable remotely.
9. Only then consider removing temporary/legacy rules.

## Variables

The following values are environment-specific and must not be assumed for another deployment:

```text
NGINX_IP=<nginx-srv-address>
ODOO_IP=<odoo-srv-address>
ADMIN_NETWORK=<administration-cidr>
LAN_NETWORK=<lan-cidr>
```

The current lab addresses are documented only in `docs/as-built/`; reusable firewall definitions must remain parameterized.

## Validation checklist

```bash
# On nginx-srv
ss -lntp

# On odoo-srv
ss -lntp

# From nginx-srv
curl -I http://<ODOO_IP>:8069/web/login

# From an unauthorized LAN host
curl -I --connect-timeout 3 http://<ODOO_IP>:8069/web/login

# PostgreSQL must remain local
sudo ss -lntp | grep ':5432'
```

A timeout/refusal from unauthorized networks is expected for protected services; do not weaken the policy merely to make connectivity tests succeed.
