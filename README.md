# ARP Racing Portfolio

Monorepo for the ARP Racing photography website and its deployment stack.

## What this project is

This repository contains everything needed to run the ARP Racing platform:

- `ARP-frontend` (git submodule): Angular web app for the public website.
- `ARP-backend` (git submodule): Node.js/Express API used by the frontend.
- `nginx`: reverse proxy and TLS entrypoint for public traffic.
- `docker-compose.yml`: orchestration for frontend hosting, backend API, and n8n webhook automation.

## How it works

1. A visitor opens `https://arpracing.duckdns.org`.
2. Nginx serves the built Angular app from `ARP-frontend/dist/arpweb_ng_v19/browser` (mounted via `/usr/share/nginx/html` in the container).
3. Frontend API calls go to `/api/*`.
4. Nginx proxies `/api/*` to the backend container on port `3000`.
5. Backend reads media metadata from ImageKit and returns JSON to the frontend.
6. Contact form submissions are forwarded by backend to an n8n webhook.

## Architecture overview

- **Frontend**: Angular SPA with routes for home, about, portfolio, library, archive, and contact pages.
- **Backend**: Express API with security middleware (Helmet, CORS, rate limiting).
- **Media source**: ImageKit file API (carousel + library data).
- **Workflow automation**: n8n for processing contact form webhooks.
- **Ingress**: Nginx handles HTTPS termination and path-based routing.

## Repository structure

```text
.
|- ARP-frontend/   # Angular app (submodule)
|- ARP-backend/    # Express API (submodule)
|- nginx/          # Nginx config
|- docker-compose.yml
|- .gitmodules
`- .env            # local secrets/config (not for commit)
```

## Getting started

### 1) Clone with submodules

```bash
git clone --recurse-submodules <repo-url>
cd ARP-Racing-photography
```

If already cloned:

```bash
git submodule update --init --recursive
```

### 2) Configure environment

Create or update `.env` in the repository root with values for:

- `IMAGEKIT_PRIVATE_KEY`
- `IMAGEKIT_PUBLIC_KEY`
- `IMAGEKIT_BASE`
- `FRONTEND_URL`
- `CORS_ORIGIN`
- `CONTACT_WEBHOOK_URL`
- `N8N_BASIC_AUTH_ACTIVE`
- `N8N_BASIC_AUTH_USER`
- `N8N_BASIC_AUTH_PASSWORD`
- `N8N_HOST`
- `N8N_PROTOCOL`
- `N8N_PATH`
- `N8N_SECURE_COOKIE`
- `N8N_EDITOR_BASE_URL`
- `WEBHOOK_URL`

Never commit real secrets.

### 3) Build frontend assets

The compose volume maps `ARP-frontend/dist` to `/usr/share/nginx/html`, and Nginx root is `/usr/share/nginx/html/arpweb_ng_v19/browser`. Build frontend first so this exact path exists:

```bash
cd ARP-frontend
npm install
npm run build
cd ..
```

### 4) Start the stack

```bash
docker compose up -d --build
```

### 5) Verify

- Site: `https://<your-domain>/`
- API health check: `https://<your-domain>/api/`
- n8n UI: `https://<your-domain>/n8n/`

## Submodule documentation

- `ARP-frontend/README.md`
- `ARP-backend/README.md`
