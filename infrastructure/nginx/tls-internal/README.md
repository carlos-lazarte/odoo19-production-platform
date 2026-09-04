# Internal TLS for Nuba / Odoo 19

This directory documents the laboratory/internal-CA TLS stage. It is intentionally separate from public ACME/Let's Encrypt deployment.

## Target

```text
LAN client
   |
   | HTTPS :443
   v
nginx-srv (192.168.0.8)
   |
   | HTTP :8069
   v
odoo-srv (192.168.0.7)
```

The certificate is terminated at Nginx. PostgreSQL remains local to `odoo-srv`.

## Certificate identity

Use the real FQDN selected for the service, for example:

```text
nuba.<DOMINIO_REAL>
```

Do not put a guessed domain, internal IP, password, private key, or CA private key in Git.

## Recommended approach

For a controlled LAN, create an internal root CA and issue a server certificate whose SAN contains the exact FQDN. Clients must trust the CA certificate. Do not use a certificate containing only the IP address if the service will be accessed by FQDN.

Keep the CA private key offline or on a dedicated administrative host. Only the issued certificate and private key required by Nginx belong on `nginx-srv`.

## Nginx TLS mount

The Docker Compose deployment should eventually mount a host directory read-only:

```yaml
volumes:
  - ./tls:/etc/nginx/tls:ro
```

Expected files on `nginx-srv`:

```text
/opt/nginx-odoo/tls/fullchain.pem
/opt/nginx-odoo/tls/privkey.pem
```

Restrict the private key permissions. Never commit these files.

## Validation sequence

1. Confirm the FQDN resolves to `nginx-srv` from the LAN.
2. Install the internal CA certificate into the trust store of a test client.
3. Place the issued certificate and key on `nginx-srv`.
4. Run `docker exec nginx-odoo nginx -t`.
5. Add HTTPS listener on TCP/443.
6. Reload Nginx only after the configuration test succeeds.
7. Validate `https://nuba.<DOMINIO_REAL>/web/login` from a trusted client.
8. Validate the certificate SAN and chain.
9. Validate Odoo's generated URLs and redirects with `proxy_mode = True`.
10. Only after HTTPS is stable, enable HTTP -> HTTPS redirect and later consider HSTS.

## Security rules

- Never expose the internal CA private key to Nginx or commit it to the repository.
- Do not disable certificate verification on clients as a permanent workaround.
- Do not enable HSTS until the FQDN, certificate chain, HTTPS listener, and redirect behavior are stable.
- Keep TCP/8069 restricted to `nginx-srv` at the host firewall layer.
