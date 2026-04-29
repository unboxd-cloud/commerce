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
| `commerce.unboxd.cloud` | storefront (Next.js) | Customer-facing storefront |
| `api.commerce.unboxd.cloud` | backend (API service) | Core commerce API |
| `admin.commerce.unboxd.cloud` | admin panel | Platform administration UI |
| `vendor.commerce.unboxd.cloud` | vendor panel (Vite app) | Seller/vendor dashboard |

---

## UI (Composable Frontends)

The platform follows a **composable frontend architecture**, where each user experience is independently deployable and communicates with the shared commerce API layer.

### Storefront (Customer UI)
- Built with Next.js
- Product browsing, cart, checkout
- Fully decoupled from backend via API layer

### Admin Panel
- Internal platform management UI
- Order, product, and user administration
- Designed for operational workflows

### Vendor Panel
- Seller-facing dashboard
- Product and inventory management
- Order fulfillment and analytics

> Each UI is independently deployable and can be replaced or extended without impacting other layers.

---

## Repository Structure

```
.
├── docker-compose.yml
├── Caddyfile
├── .env.example
└── src/   (external commerce engine source)
```

> `src/` contains external source code and is not tracked by git.

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
