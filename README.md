# Unboxd Commerce Platform

Deployment manifest for the **Unboxd Commerce Platform** running at **https://commerce.unboxd.cloud**.

Hosted on `unboxd.cloud`.

---

## Overview

This repository deploys the **Unboxd Commerce Platform**, a multi-service commerce system running behind a unified domain with subdomain routing.

It is a self-hosted infrastructure stack using Docker Compose for service orchestration and Caddy for TLS termination and reverse proxying.

---

## Architecture

One commerce platform, four subdomains (wildcard TLS certificate via Caddy):

| Subdomain | Service | Description |
|---|---|---|
| `commerce.unboxd.cloud` | storefront | Customer-facing storefront |
| `api.commerce.unboxd.cloud` | backend API | Core commerce API |
| `admin.commerce.unboxd.cloud` | admin UI | Platform administration (served by backend) |
| `vendor.commerce.unboxd.cloud` | vendor panel | Seller/vendor dashboard |

---

## UI (Composable Applications)

The platform follows a **composable monorepo architecture**, where each UI is an independent app under `src/apps` and deployed as its own service.

### Storefront
- **Path:** `src/apps/storefront`
- **Runtime:** Next.js
- **Service:** `mercur-storefront`
- **Role:** Customer-facing storefront (catalog, cart, checkout)

### Vendor Panel
- **Path:** `src/apps/vendor-panel`
- **Runtime:** Vite
- **Service:** `mercur-vendor`
- **Role:** Seller dashboard (products, inventory, orders)

### Admin UI
- **Path:** `src/apps/backend`
- **Runtime:** Medusa Admin (served via backend)
- **Service:** `mercur-backend`
- **Role:** Platform administration (products, orders, users, vendors)

> All UI apps are built from `src/apps/*` and exposed via Docker services defined in `docker-compose.yml`.

---

## Repository Structure

```
.
├── docker-compose.yml
├── Caddyfile
├── .env.example
└── src/
    ├── apps/
    │   ├── backend/
    │   ├── storefront/
    │   └── vendor-panel/
```

---

## Deployment

### 1. Clone repository

```bash
git clone git@github.com:unboxdcloudplatform/commerce.git
cd commerce
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
- `PUBLIC_DOMAIN`

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

- https://github.com/mercurjs/mercur
