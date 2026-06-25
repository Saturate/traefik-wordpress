# Traefik WordPress

Example setup for running [Traefik](https://traefik.io/) v3 as a reverse proxy in front of WordPress, with automatic Let's Encrypt TLS certificates.

## Prerequisites

- Docker with the Compose plugin
- A domain name pointing to your server

## Getting started

1. Create the shared network:

   ```bash
   docker network create web
   ```

2. Configure and start Traefik:

   ```bash
   cd traefik
   cp .env.example .env
   # Edit .env with your email, dashboard hostname, and dashboard credentials
   docker compose up -d
   ```

3. Configure and start WordPress:

   ```bash
   cd wordpress
   cp .env.example .env
   # Edit .env with your domain and database passwords
   docker compose up -d
   ```

## www redirect

The WordPress router only matches the exact `WORDPRESS_HOST` value. If you need `www.example.com` to redirect to `example.com`, add these labels to the WordPress service:

```yaml
- "traefik.http.routers.wordpress-www.rule=Host(`www.${WORDPRESS_HOST}`)"
- "traefik.http.routers.wordpress-www.entrypoints=websecure"
- "traefik.http.routers.wordpress-www.tls.certresolver=letsencrypt"
- "traefik.http.routers.wordpress-www.middlewares=www-redirect"
- "traefik.http.middlewares.www-redirect.redirectregex.regex=^https://www\\.(.*)"
- "traefik.http.middlewares.www-redirect.redirectregex.replacement=https://$${1}"
```

## Adding more sites

Add another service with `traefik.enable=true` labels and the `web` network. Traefik picks up new containers automatically. Each site needs its own router name and `Host()` rule.

## Dashboard

The Traefik dashboard is available at `https://<TRAEFIK_DASHBOARD_HOST>/dashboard/` (trailing slash required), protected by HTTP basic auth. Generate credentials with:

```bash
echo $(htpasswd -nB admin) | sed -e 's/\$/\$\$/g'
```

Paste the output into `TRAEFIK_DASHBOARD_AUTH` in `traefik/.env`.
