# Unboxd Commerce Platform

Deployment manifest for the **Unboxd Commerce Platform** running at **https://commerce.unboxd.cloud**.

Hosted on `unboxd.cloud`.

---

## Overview

This repository deploys the **Unboxd Commerce Platform**, a full-featured multi-service commerce system running behind a unified domain with subdomain routing.

It is a self-hosted infrastructure stack using Docker Compose for service orchestration and Caddy for TLS termination and reverse proxying.

---

## Architecture

One commerce platform, four subdomains (wildcard TLS certificate via Caddy):

| Subdomain | Service | Description |
|---|---|---|
| `commerce.unboxd.cloud` | storefront (Next.js) | Customer-facing storefront |
| `api.commerce.unboxd.cloud` | backend (API service) | Core commerce API |
| `admin.commerce.unboxd.cloud` | admin panel | Platform administration UI |
| `vendor.commerce.unboxd.cloud` | vendor panel (Vite app) | Seller/vendor dashboard |

---

## Repository Structure

```
.
├── docker-compose.yml
├── Caddyfile
├── .env.example
└── src/   (external commerce engine source)
```

> `src/` contains the external commerce engine source code and is not tracked by git.

---

## Deployment

### 1. Clone deployment repository

```bash
git clone git@github.com:Unboxd-Cloud/mercur.git
cd mercur
```

### 2. Fetch engine source

```bash
git clone https://github.com/mercurjs/mercur.git src
```

### 3. Configure environment

```bash
cp .env.example .env
```

Required variables:

- `POSTGRES_PASSWORD`
- `JWT_SECRET`
- `COOKIE_SECRET`

Generate secure values:

```bash
openssl rand -hex 32
```

---

### 4. DNS configuration

Point all records to your VM:

- `commerce.unboxd.cloud`
- `api.commerce.unboxd.cloud`
- `admin.commerce.unboxd.cloud`
- `vendor.commerce.unboxd.cloud`

---

### 5. Build and start services

```bash
docker compose up -d --build
```

---

### 6. Run database migrations

```bash
docker compose exec backend npx medusa db:migrate
```

---

### 7. Create admin user

```bash
docker compose exec backend npx medusa user \
  -e admin@example.com \
  -p <password>
```

---

## TLS / Networking

Caddy automatically provisions HTTPS certificates on first request for all subdomains using wildcard-enabled configuration.

---

## Powered by

This platform is powered by:

- **Mercur Commerce Engine** – https://github.com/mercurjs/mercur (Medusa v2 multi-vendor commerce system)

---

## Notes

- Engine source is pulled manually via `git pull` inside `src/`
- Backend service is built from `src/apps/backend`
- Ensure upstream structure matches before deployment

---

## References

- Medusa v2 Docs: https://docs.medusajs.com
