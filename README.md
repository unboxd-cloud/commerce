# Mercur Commerce Deployment

Deployment manifest for **Mercur Commerce** ([Mercur](https://github.com/mercurjs/mercur) – Medusa v2 multi-vendor commerce platform) running at **https://commerce.unboxd.cloud**.

Hosted on `unboxd.cloud`.

---

## Overview

This repository deploys a full commerce stack based on Mercur, composed of multiple services running behind a single domain with subdomain routing.

It is a self-hosted deployment using Docker Compose and Caddy for TLS and reverse proxying.

---

## Architecture

One commerce platform, four subdomains (wildcard TLS certificate via Caddy):

| Subdomain | Service | Description |
|---|---|---|
| `commerce.unboxd.cloud` | storefront (Next.js) | Customer-facing storefront |
| `api.commerce.unboxd.cloud` | backend (Medusa v2) | Core commerce API |
| `admin.commerce.unboxd.cloud` | admin (Medusa Admin) | Platform administration UI |
| `vendor.commerce.unboxd.cloud` | vendor panel (Vite app) | Seller/vendor dashboard |

---

## Repository Structure

```
.
├── docker-compose.yml
├── Caddyfile
├── .env.example
└── src/   (Mercur upstream source)
```

> `src/` is not tracked by git and contains the upstream Mercur monorepo.

---

## Deployment

### 1. Clone deployment repo

```bash
git clone git@github.com:Unboxd-Cloud/mercur.git
cd mercur
```

### 2. Fetch upstream Mercur

```bash
git clone https://github.com/mercurjs/mercur.git src
```

### 3. Configure environment

```bash
cp .env.example .env
```

Required variables include:

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

Caddy automatically provisions HTTPS certificates on first request for all subdomains using a wildcard-enabled configuration.

---

## Notes

- Upstream Mercur is pulled manually via `git pull` inside `src/`
- The backend service is expected to be built from `src/apps/backend`
- Ensure upstream structure matches before first deployment

---

## Upstream

- Mercur: https://github.com/mercurjs/mercur
- Medusa v2 Docs: https://docs.medusajs.com
