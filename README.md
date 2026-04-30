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

## Service Mapping (Source → Service → Domain → Port)

| App Path | Docker Service | Domain | Internal Port |
|---|---|---|---|
| `src/apps/backend` | `mercur-backend` | `api.commerce.unboxd.cloud` | 9000 |
| `src/apps/storefront` | `mercur-storefront` | `commerce.unboxd.cloud` | 3000 |
| `src/apps/vendor-panel` | `mercur-vendor` | `vendor.commerce.unboxd.cloud` | 5173 |
| (Caddy) | `mercur-caddy` | all domains | 80 / 443 |

> Admin UI is served via **backend** (`/admin`) and mapped to `admin.commerce.unboxd.cloud` via Caddy.

---

## UI (Composable Applications)

The platform follows a **composable monorepo architecture**, where each UI is an independent app under `src/apps` and deployed as its own service.

### Storefront
- **Path:** `src/apps/storefront`
- **Runtime:** Next.js
- **Service:** `mercur-storefront`

### Vendor Panel
- **Path:** `src/apps/vendor-panel`
- **Runtime:** Vite
- **Service:** `mercur-vendor`

### Admin UI
- **Path:** `src/apps/backend`
- **Runtime:** Medusa Admin (served via backend)
- **Service:** `mercur-backend`

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

## Naming Note (Optional Cleanup)

Current service names use `mercur-*` (from upstream). For full branding consistency, you may rename them to:

- `commerce-backend`
- `commerce-storefront`
- `commerce-vendor`
- `commerce-caddy`

This is optional but recommended for long-term clarity.

---

## Powered by

- https://github.com/mercurjs/mercur
