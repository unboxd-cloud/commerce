# mercur

Deployment manifest for [Mercur](https://github.com/mercurjs/mercur) (Medusa v2 multi-vendor marketplace) at **https://marketplace.unboxd.cloud**.

Hosted on `unboxd.cloud`.

## Architecture

One marketplace, four subdomains (one wildcard cert covers all):

| Subdomain | Service | Purpose |
|---|---|---|
| `marketplace.unboxd.cloud` | storefront (Next.js) | customer-facing |
| `api.marketplace.unboxd.cloud` | backend (Medusa v2) | REST/GraphQL API |
| `admin.marketplace.unboxd.cloud` | backend (Medusa admin) | platform admin UI |
| `vendor.marketplace.unboxd.cloud` | vendor-panel (Vite) | per-vendor seller dashboard |

## Deploy

Mercur has no published Docker image; we build from source.

```bash
# 1. clone this deploy repo
git clone git@github.com:Unboxd-Cloud/mercur.git && cd mercur

# 2. clone upstream Mercur into ./src (ignored by git)
git clone https://github.com/mercurjs/mercur.git src

# 3. configure
cp .env.example .env
# fill in POSTGRES_PASSWORD, JWT_SECRET, COOKIE_SECRET
# (generate secrets: openssl rand -hex 32)

# 4. point DNS — 4 records
# A    marketplace.unboxd.cloud         -> <VM public IP>
# A    api.marketplace.unboxd.cloud     -> <VM public IP>
# A    admin.marketplace.unboxd.cloud   -> <VM public IP>
# A    vendor.marketplace.unboxd.cloud  -> <VM public IP>

# 5. build + start
docker compose up -d --build

# 6. run migrations
docker compose exec mercur-backend npx medusa db:migrate

# 7. create admin user
docker compose exec mercur-backend npx medusa user -e admin@example.com -p <password>
```

Caddy auto-issues TLS on first request for all 4 subdomains.

## Notes

- `src/` holds upstream Mercur source, ignored by git — pull upstream updates with `cd src && git pull`.
- Dockerfile paths (`src/apps/backend/Dockerfile` etc.) assume Mercur's monorepo layout. Verify upstream structure hasn't changed before first build.

## Upstream
- App: https://github.com/mercurjs/mercur
- Medusa v2 docs: https://docs.medusajs.com
